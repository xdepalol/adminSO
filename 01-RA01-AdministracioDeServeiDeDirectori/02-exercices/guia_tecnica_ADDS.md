# Guia tècnica d'Active Directory

## 1. Objectiu de la guia

Aquesta guia recull procediments, eines i tècniques habituals per desplegar, administrar i diagnosticar un entorn **Active Directory Domain Services (AD DS)** amb Windows Server i clients Windows.

La guia està pensada com a **material de consulta**. No defineix com s'ha d'organitzar una empresa concreta ni proporciona una única solució de desplegament. Decisions com l'estructura d'unitats organitzatives (OU), els grups necessaris, l'assignació de permisos o l'àmbit de les polítiques de grup depenen de les necessitats de cada organització.

Al llarg de la guia es treballaran principalment:

- instal·lació i configuració d'Active Directory Domain Services;
- administració d'OU, usuaris, grups i equips;
- integració de clients Windows al domini;
- recursos compartits i permisos;
- espais de noms DFS;
- polítiques de grup (GPO);
- automatització bàsica amb PowerShell;
- eines de verificació i diagnòstic;
- resolució de problemes habituals.

> **Important:** no et limitis a seguir procediments. Després de cada configuració, comprova'n el resultat. En administració de sistemes és tan important saber desplegar un servei com saber verificar que funciona i diagnosticar-lo quan no ho fa.

---

# 2. Preparació del laboratori

## 2.1. Màquina virtual del servidor

Per desplegar Active Directory necessitarem una màquina amb **Windows Server**.

Abans d'instal·lar AD DS és recomanable comprovar:

- nom de l'equip;
- configuració de xarxa;
- adreça IP;
- servidor DNS configurat;
- data i hora del sistema.

El controlador de domini ha de disposar d'una **adreça IP estàtica**. Un canvi d'adreça podria impedir que els clients localitzessin correctament els serveis del domini.

El nom del servidor també s'hauria de definir **abans de promocionar-lo a controlador de domini**.

Pots consultar la configuració actual amb:

```powershell
hostname
ipconfig /all
```

### Canviar el nom del servidor amb PowerShell

Per exemple:

```powershell
Rename-Computer -NewName "SRV-DC01" -Restart
```

Adapta el nom a la infraestructura que estiguis desplegant.

---

## 2.2. Màquina virtual del client

Per verificar el funcionament del domini necessitarem almenys un client Windows.

El client permetrà comprovar, entre altres aspectes:

- incorporació d'un equip al domini;
- autenticació centralitzada;
- pertinences a grups;
- accés a recursos;
- aplicació de GPO.

### Compte amb l'edició de Windows

No totes les edicions de Windows permeten incorporar l'equip a un domini Active Directory.

Utilitza una edició compatible, com ara:

- Windows 11 Pro;
- Windows 11 Enterprise;
- Windows 11 Education.

> **Important:** **Windows 11 Home no es pot incorporar a un domini Active Directory.** Comprova l'edició abans de dedicar temps a configurar el client.

Pots consultar-la executant:

```text
winver
```

o des de **Configuració → Sistema → Quant a**.

---

## 2.3. Configuració de xarxa

El controlador de domini i el client han de tenir connectivitat entre ells.

En un laboratori amb màquines virtuals és habitual situar-los en una mateixa xarxa interna o xarxa de laboratori.

Un exemple podria ser:

| Equip | Adreça |
|---|---|
| Controlador de domini | `192.168.100.10/24` |
| Client | `192.168.100.99/24` |

Aquestes adreces són només un exemple. Utilitza les definides per al teu laboratori.

### Configuració del servidor

El controlador de domini ha de disposar d'una IP estàtica.

Es pot configurar gràficament des de les propietats d'IPv4 de l'adaptador o mitjançant PowerShell.

Per consultar els adaptadors:

```powershell
Get-NetAdapter
```

Per consultar la configuració IP:

```powershell
Get-NetIPConfiguration
```

### Configuració del client

El client també ha d'estar a la xarxa correcta i ha de poder comunicar-se amb el controlador de domini.

Una primera comprovació és:

```text
ping <IP-del-servidor>
```

Per exemple:

```text
ping 192.168.100.10
```

Que el `ping` funcioni **no garanteix** que Active Directory funcioni, però permet descartar un problema bàsic de connectivitat.

---

## 2.4. DNS: un requisit fonamental

Active Directory depèn fortament de **DNS**.

Els clients utilitzen DNS per localitzar els controladors de domini i els diferents serveis d'Active Directory.

Per aquest motiu, un client del domini ha d'utilitzar com a DNS un servidor capaç de resoldre el domini d'Active Directory. En un laboratori senzill, normalment serà el mateix controlador de domini.

Per exemple:

```text
Client
IP:  192.168.100.99
DNS: 192.168.100.10
```

> **Important:** un dels errors més habituals quan un client no pot incorporar-se al domini és tenir configurat un DNS extern —per exemple, el del router o un DNS públic— en lloc del DNS del domini.

Pots consultar la configuració amb:

```text
ipconfig /all
```

Més endavant utilitzarem `nslookup` per comprovar que el client pot resoldre correctament el domini.

---

# 3. Instal·lació d'Active Directory Domain Services

## 3.1. Instal·lar el rol AD DS

Active Directory Domain Services s'instal·la com un rol de Windows Server.

Es pot fer des de:

**Administrador del servidor → Administrar → Agregar roles y características**

Selecciona:

**Servicios de dominio de Active Directory (AD DS)**

i incorpora també les eines d'administració proposades.

També es pot instal·lar amb PowerShell:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

Pots comprovar-ne l'estat amb:

```powershell
Get-WindowsFeature AD-Domain-Services
```

---

## 3.2. Promocionar el servidor a controlador de domini

Instal·lar AD DS **no converteix automàticament el servidor en controlador de domini**.

Després d'instal·lar el rol, cal promocionar-lo.

Des de l'Administrador del servidor apareixerà l'opció:

**Promover este servidor a controlador de dominio**

Si estem creant una infraestructura nova, seleccionarem:

**Agregar un nuevo bosque**

i indicarem el nom del domini.

Per exemple:

```text
empresa.test
```

Durant el procés es configuraran diferents components, entre ells:

- Active Directory Domain Services;
- DNS, si s'ha seleccionat;
- base de dades del directori;
- SYSVOL;
- serveis necessaris per al domini.

El servidor es reiniciarà en finalitzar.

### Promoció amb PowerShell

També es pot crear un nou bosc des de PowerShell:

```powershell
Install-ADDSForest `
    -DomainName "empresa.test" `
    -InstallDNS
```

El sistema demanarà la contrasenya de **Directory Services Restore Mode (DSRM)**.

> **Nota:** en un entorn real, el nom del domini i el disseny DNS s'han de planificar abans de desplegar Active Directory. No és una decisió que s'hagi de prendre arbitràriament durant la instal·lació.

---

## 3.3. Comprovar el controlador de domini

Després del reinici, comprova que el servidor funciona com a controlador de domini.

Pots obrir:

**Administrador del servidor → Herramientas → Usuarios y equipos de Active Directory**

També pots consultar informació amb PowerShell:

```powershell
Get-ADDomain
```

i:

```powershell
Get-ADForest
```

Per consultar els controladors de domini:

```powershell
Get-ADDomainController -Filter *
```

---

## 3.4. Comprovar DNS

Obre:

**Administrador del servidor → Herramientas → DNS**

Hauries de trobar la zona corresponent al domini.

Des del mateix servidor pots provar:

```text
nslookup empresa.test
```

Active Directory publica també registres DNS que permeten localitzar els seus serveis.

Una comprovació especialment útil és:

```text
nslookup -type=SRV _ldap._tcp.dc._msdcs.empresa.test
```

Aquesta consulta busca els registres SRV utilitzats per localitzar controladors de domini que proporcionen LDAP.

---

# 4. Administració del directori

## 4.1. Usuarios y equipos de Active Directory

Una de les eines principals d'administració és:

**Usuarios y equipos de Active Directory**  
(*Active Directory Users and Computers — ADUC*)

Des d'aquesta consola podem administrar:

- OU;
- usuaris;
- grups;
- equips;
- propietats dels objectes;
- pertinences a grups.

> **Important:** una estructura d'Active Directory no s'hauria de dissenyar només perquè «quedi ordenada». Les OU tenen una funció administrativa i, entre altres coses, permeten definir l'àmbit d'aplicació de polítiques de grup.

---

## 4.2. Crear unitats organitzatives

Per crear una OU:

1. situa't sobre el domini o sobre una altra OU;
2. botó dret → **Nuevo → Unidad organizativa**;
3. indica el nom.

Amb PowerShell:

```powershell
New-ADOrganizationalUnit `
    -Name "DepartamentA" `
    -Path "DC=empresa,DC=test"
```

Podem crear OU dins d'altres OU:

```powershell
New-ADOrganizationalUnit `
    -Name "Usuaris" `
    -Path "OU=DepartamentA,DC=empresa,DC=test"
```

El resultat seria:

```text
DC=empresa,DC=test
└── OU=DepartamentA
    └── OU=Usuaris
```

---

## 4.3. Crear usuaris

Des d'ADUC:

1. selecciona l'OU corresponent;
2. botó dret → **Nuevo → Usuario**;
3. introdueix les dades de l'usuari;
4. defineix la contrasenya i les opcions corresponents.

Amb PowerShell:

```powershell
New-ADUser `
    -Name "Usuari Exemple" `
    -GivenName "Usuari" `
    -Surname "Exemple" `
    -SamAccountName "uexemple" `
    -UserPrincipalName "uexemple@empresa.test" `
    -Path "OU=Usuaris,OU=DepartamentA,DC=empresa,DC=test" `
    -Enabled $true
```

Si cal definir una contrasenya:

```powershell
$password = Read-Host "Contrasenya" -AsSecureString
```

i utilitzar:

```powershell
-AccountPassword $password
```

> **Recomanació:** després de crear objectes automàticament, comprova sempre que han quedat a l'OU correcta i amb els atributs esperats.

---

## 4.4. Crear grups

Els grups permeten administrar conjuntament diversos usuaris o altres grups.

Des d'ADUC:

**Nuevo → Grupo**

Cal decidir, entre altres aspectes:

- nom;
- tipus;
- àmbit del grup.

Amb PowerShell:

```powershell
New-ADGroup `
    -Name "GRP_DepartamentA" `
    -GroupScope Global `
    -GroupCategory Security `
    -Path "OU=Grups,DC=empresa,DC=test"
```

### Evita assignar permisos usuari per usuari

En general, és preferible:

```text
Usuari → Grup → Permís → Recurs
```

que:

```text
Usuari → Permís → Recurs
```

Així, quan una persona canvia de funció, podem modificar-ne la pertinença als grups sense haver de revisar manualment tots els recursos.

---

## 4.5. Afegir membres a un grup

Des de les propietats d'un grup podem utilitzar la pestanya **Miembros**.

Amb PowerShell:

```powershell
Add-ADGroupMember `
    -Identity "GRP_DepartamentA" `
    -Members "uexemple"
```

Per consultar els membres:

```powershell
Get-ADGroupMember "GRP_DepartamentA"
```

---

## 4.6. Anidament de grups

Un grup pot ser membre d'un altre grup.

Això permet construir models més mantenibles. Per exemple:

```text
Usuaris
   ↓
Grup de departament o rol
   ↓
Grup associat a un recurs
   ↓
Permís sobre el recurs
```

Aquesta separació evita haver de modificar els permisos dels recursos cada vegada que canvia una persona.

> **Important:** l'anidament de grups ha de tenir una finalitat clara. Crear molts grups sense una convenció o funció definida pot dificultar l'administració en lloc de facilitar-la.

Per comprovar la pertinença efectiva d'un usuari des del client:

```text
whoami /groups
```

Recorda que els canvis de pertinença a grups poden requerir **tancar la sessió i tornar-la a iniciar** perquè es generi un nou testimoni d'accés amb les pertinences actualitzades.

---

## 4.7. Moure objectes

Els usuaris i equips es poden moure entre OU des d'ADUC.

També podem utilitzar PowerShell. Primer localitzem l'objecte:

```powershell
Get-ADComputer -Identity "CLIENT01"
```

i després el podem moure:

```powershell
Get-ADComputer "CLIENT01" | Move-ADObject `
    -TargetPath "OU=Equips,DC=empresa,DC=test"
```

Això és especialment important quan volem aplicar GPO diferents segons la ubicació dels objectes.

---

# 5. Integració d'un client Windows al domini

## 5.1. Comprovacions prèvies

Abans d'intentar incorporar el client, comprova:

1. que utilitza una edició de Windows compatible;
2. que té una IP correcta;
3. que pot comunicar-se amb el servidor;
4. que utilitza el DNS del domini;
5. que resol correctament el nom del domini.

Comprova la xarxa:

```text
ipconfig /all
```

Comprova connectivitat:

```text
ping 192.168.100.10
```

Comprova DNS:

```text
nslookup empresa.test
```

I, si cal:

```text
nslookup -type=SRV _ldap._tcp.dc._msdcs.empresa.test
```

> **Si el client no resol el domini correctament, no continuïs intentant unir-lo repetidament. Resol primer el problema de DNS.**

---

## 5.2. Incorporar el client al domini

Des de Windows 11 es pot accedir a la configuració del nom de l'equip i domini des de les opcions avançades del sistema.

Selecciona l'opció per canviar la pertinença de:

**Grupo de trabajo**

a:

**Dominio**

i introdueix el domini corresponent.

El sistema demanarà credencials d'un compte autoritzat per incorporar equips al domini.

Si el procés finalitza correctament, apareixerà un missatge de benvinguda al domini i caldrà reiniciar l'equip.

---

## 5.3. Iniciar sessió amb un usuari del domini

Després del reinici, selecciona **Otro usuario**.

Pots indicar l'usuari amb formats com:

```text
EMPRESA\uexemple
```

o:

```text
uexemple@empresa.test
```

Una vegada iniciada la sessió:

```text
whoami
```

hauria de mostrar una identitat del domini.

Per exemple:

```text
empresa\uexemple
```

---

## 5.4. Verificar els grups

Executa:

```text
whoami /groups
```

La sortida mostra els grups presents al testimoni d'accés de l'usuari.

Aquesta ordre és molt útil quan:

- un usuari no pot accedir a un recurs;
- una GPO filtrada per grup no s'aplica;
- acabem de modificar pertinences a grups;
- volem comprovar l'efecte de l'anidament.

> **Important:** que un usuari aparegui com a membre d'un grup a ADUC no significa necessàriament que una sessió iniciada anteriorment ja incorpori aquesta pertinença. Si has modificat grups, tanca la sessió i torna-la a iniciar abans de diagnosticar el problema.

---

# 6. Recursos compartits i permisos

## 6.1. Crear un recurs compartit

Windows Server permet publicar carpetes mitjançant SMB.

Es poden administrar des de:

**Administrador del servidor → Servicios de archivos y almacenamiento → Recursos compartidos**

Des d'aquí pots crear un nou recurs compartit SMB i indicar:

- carpeta física;
- nom del recurs;
- opcions de compartició;
- permisos.

Si el recurs s'anomena `Dades`, podria ser accessible com:

```text
\\SERVIDOR\Dades
```

---

## 6.2. Permisos de compartició i permisos NTFS

Quan publiquem una carpeta SMB hi intervenen dos nivells diferents:

**Permisos de compartició**, que controlen l'accés a través de la compartició SMB.

**Permisos NTFS**, que controlen l'accés al sistema de fitxers.

Quan un usuari accedeix per xarxa, s'han de tenir en compte **tots dos nivells**.

Per això és important saber en quin nivell estem configurant un permís quan diagnostiquem un problema.

Una estratègia habitual és mantenir una configuració de compartició relativament permissiva i controlar de manera precisa l'accés efectiu mitjançant **permisos NTFS assignats a grups**.

---

## 6.3. Assignar permisos a grups

En lloc de:

```text
Usuari01 → Modify
Usuari02 → Modify
Usuari03 → Modify
```

és preferible:

```text
GRP_RecursA → Modify
```

i gestionar qui pertany a `GRP_RecursA` des d'Active Directory.

Així separem:

**qui és l'usuari i quin rol té**

de:

**quin permís necessita el recurs**.

---

## 6.4. Provar els permisos

No comprovis un recurs únicament amb un usuari administrador.

Fes almenys:

**Prova positiva:** un usuari autoritzat pot accedir al recurs i realitzar les operacions previstes.

**Prova negativa:** un usuari no autoritzat no pot realitzar-les.

Per exemple:

```text
\\SERVIDOR\Dades
```

Intenta crear, modificar o eliminar un fitxer segons els permisos que vulguis validar.

Una prova negativa és especialment important: que un usuari autoritzat pugui entrar no demostra que els permisos estiguin restringint correctament la resta.

---

# 7. Distributed File System (DFS)

## 7.1. Què aporta DFS Namespaces?

Sense DFS, els recursos poden publicar-se amb rutes vinculades directament al servidor:

```text
\\SERVIDOR\Dades
\\SERVIDOR\Projectes
```

Amb un espai de noms DFS basat en domini podem oferir una ruta lògica:

```text
\\empresa.test\Dades
```

Això desacobla, en part, la forma com els usuaris accedeixen als recursos de la ubicació física concreta dels servidors.

---

## 7.2. Instal·lar DFS Namespaces

Des de:

**Administrador del servidor → Agregar roles y características**

localitza els serveis de fitxers i instal·la:

**DFS Namespaces**

També pots utilitzar PowerShell:

```powershell
Install-WindowsFeature FS-DFS-Namespace -IncludeManagementTools
```

Després trobaràs:

**Administrador del servidor → Herramientas → Administración de DFS**

---

## 7.3. Crear un espai de noms

A **Administración de DFS**:

1. selecciona **Espacios de nombres**;
2. crea un espai de noms nou;
3. indica el servidor que l'allotjarà;
4. defineix-ne el nom;
5. si treballes amb Active Directory, pots crear un **espai de noms basat en domini**.

Un exemple de ruta podria ser:

```text
\\empresa.test\Dades
```

---

## 7.4. Afegir carpetes al DFS

Dins de l'espai de noms podem crear carpetes lògiques que apuntin a recursos SMB existents.

Per exemple:

```text
\\empresa.test\Dades\DepartamentA
```

pot apuntar a:

```text
\\SERVIDOR\DepartamentA
```

> **Important:** DFS no substitueix automàticament els permisos NTFS dels recursos. Continua sent necessari configurar correctament qui pot accedir a cada carpeta.

---

## 7.5. Access-Based Enumeration

**Access-Based Enumeration (ABE)** permet ocultar als usuaris determinats recursos als quals no tenen accés.

Això millora l'experiència d'ús, però no s'ha de confondre amb el mecanisme de seguretat.

> **ABE controla principalment què veu l'usuari. Els permisos NTFS continuen sent els que han d'impedir un accés no autoritzat.**

Per tant, no utilitzis «la carpeta no apareix» com a única prova que els permisos estan ben configurats.

---

# 8. Polítiques de grup (GPO)

## 8.1. Group Policy Management

Les polítiques de grup permeten administrar centralitzadament configuracions dels usuaris i equips del domini.

L'eina principal és:

**Administrador del servidor → Herramientas → Administración de directivas de grupo**

(*Group Policy Management*).

Una GPO pot contenir configuracions de:

- **Computer Configuration**: aplicades als equips;
- **User Configuration**: aplicades als usuaris.

Aquesta diferència és fonamental quan decidim **on vincular una GPO**.

---

## 8.2. Crear una GPO

Des de Group Policy Management podem crear una GPO i editar-la.

Crear-la, però, **no és suficient**.

Una GPO necessita un àmbit d'aplicació adequat.

Per això cal distingir:

**crear la GPO** → definir què configura;

**vincular la GPO** → definir sobre quina part del directori pot actuar;

**filtrar la GPO** → determinar quins objectes dins d'aquell àmbit poden aplicar-la.

---

## 8.3. Vincular una GPO

Una GPO es pot vincular a diferents nivells d'Active Directory.

En un laboratori senzill, habitualment treballarem amb el domini i les OU.

Per exemple, si una GPO conté una configuració d'equip i està vinculada a una OU que només conté usuaris, no obtindrem el resultat esperat.

De la mateixa manera, l'estructura d'OU condiciona les possibilitats d'administració mitjançant GPO.

---

## 8.4. Security Filtering

El **Security Filtering** permet limitar quins usuaris, equips o grups dins de l'àmbit de la GPO poden aplicar-la.

Per aplicar una GPO, el destinatari necessita els permisos adequats, entre ells:

- **Read**;
- **Apply Group Policy**.

Això permet, per exemple, vincular una política en un àmbit relativament ampli però restringir-ne l'aplicació a un grup concret.

---

## 8.5. Delegació i permisos de lectura

Aquest és un punt que pot provocar configuracions aparentment correctes que no funcionen.

Els clients necessiten poder **llegir la GPO** per processar-la.

Si modifiques el filtratge de seguretat —per exemple, retirant `Authenticated Users` i aplicant la política només a un grup determinat— comprova també la pestanya **Delegation** i els permisos associats.

En alguns escenaris pot ser necessari mantenir permisos de **Read** per als objectes que necessiten llegir la política, encara que no hagin de tenir **Apply Group Policy**.

> **Important:** «poder llegir la GPO» i «tenir permís per aplicar-la» són conceptes diferents. Quan una GPO no funciona, comprova tots dos.

---

## 8.6. Forçar l'actualització de les polítiques

Les GPO s'actualitzen periòdicament, però durant un laboratori podem forçar-ne l'actualització:

```text
gpupdate /force
```

Es pot requerir tancar sessió o reiniciar l'equip depenent de la configuració modificada.

---

## 8.7. Comprovar les GPO aplicades

Utilitza:

```text
gpresult /r
```

Aquesta ordre permet veure informació sobre les polítiques aplicades a l'usuari i a l'equip.

També pots generar un informe més complet:

```text
gpresult /h informe-gpo.html
```

i obrir-lo amb el navegador.

> **No et limitis a comprovar que l'efecte visual de la política existeix.** `gpresult` permet obtenir evidència de quines polítiques ha processat realment el client.

### Consulta de les GPO aplicades a l'equip

Per visualitzar amb `gpresult /r` les polítiques aplicades a l'equip, inicia una sessió amb un compte administrador i executa l'ordre. No és suficient iniciar una sessió amb un usuari estàndard i obrir posteriorment PowerShell com a administrador: en aquest cas, `gpresult` pot no mostrar la informació de les polítiques d'equip que necessites verificar.

## 8.8 Exemple de política d'equip: gestió de Windows Update

Les GPO poden aplicar configuracions específiques als equips mitjançant **Computer Configuration**. Per exemple, es pot controlar el comportament de les actualitzacions automàtiques de Windows des de les plantilles administratives de Windows Update.

Aquest tipus de polítiques permet definir aspectes com el comportament de la descàrrega, la instal·lació o el reinici dels equips. Segons la política configurada, això no implica necessàriament impedir que un usuari amb permisos pugui iniciar manualment una actualització.

En una infraestructura corporativa, aquestes restriccions poden formar part d'una estratègia d'actualització centralitzada. Per exemple, una organització pot utilitzar Windows Server Update Services (WSUS) per controlar centralment l'aprovació i distribució de les actualitzacions, mentre que les GPO defineixen el comportament dels equips clients.

> **Important**: una GPO configurada a **Computer Configuration** s'aplica als objectes d'equip, no als usuaris que hi inicien sessió. Per tant, cal comprovar on es troba l'objecte de l'equip dins d'Active Directory i on està vinculada la GPO.

---

# 9. Automatització amb PowerShell

L'administració gràfica és adequada per a moltes operacions puntuals, però quan hem de crear o modificar molts objectes és útil automatitzar.

PowerShell permet, entre altres coses:

- crear OU;
- crear usuaris;
- crear grups;
- gestionar pertinences;
- consultar objectes;
- importar informació des d'un CSV.

---

## 9.1. Mòdul ActiveDirectory

Comprova que les ordres d'Active Directory estan disponibles:

```powershell
Get-Command -Module ActiveDirectory
```

Algunes ordres habituals són:

```powershell
Get-ADUser
Get-ADGroup
Get-ADGroupMember
Get-ADComputer
New-ADUser
New-ADGroup
New-ADOrganizationalUnit
Add-ADGroupMember
```

---

## 9.2. Importar dades des d'un CSV

Suposem un fitxer:

```csv
Nom,Cognom,Username,Departament
Anna,Soler,asoler,DepartamentA
Marc,Riera,mriera,DepartamentB
```

Podem llegir-lo amb:

```powershell
$usuaris = Import-Csv ".\usuaris.csv"
```

i recórrer-ne les files:

```powershell
foreach ($usuari in $usuaris) {
    Write-Host $usuari.Username
}
```

A partir d'aquí es poden combinar les dades amb `New-ADUser`, `Add-ADGroupMember` i altres ordres.

> **Important:** automatitzar no significa executar un script i confiar que ha funcionat. Revisa els errors i verifica després els objectes creats.

Per exemple:

```powershell
Get-ADUser -Filter *
```

o:

```powershell
Get-ADGroupMember "GRP_DepartamentA"
```

---

## 9.3. Scripts generats o adaptats amb IA

Una eina d'IA pot ajudar a generar o modificar scripts, però el resultat s'ha de tractar com qualsevol altre codi que no has escrit íntegrament tu.

Abans d'executar-lo:

- revisa què farà;
- comprova els noms de les OU i grups;
- revisa les rutes LDAP;
- identifica les ordres que creen, modifiquen o eliminen objectes;
- executa'l, si és possible, primer sobre un conjunt reduït de dades;
- comprova el resultat.

Has de poder explicar **què fa l'script i verificar què ha modificat**.

---

# 10. Eines de verificació i diagnòstic

Aquestes ordres són especialment útils durant un desplegament.

| Ordre | Utilitat principal |
|---|---|
| `ipconfig /all` | Consultar IP, DNS i configuració de xarxa |
| `ping` | Comprovar connectivitat IP bàsica |
| `nslookup` | Comprovar resolució DNS |
| `whoami` | Comprovar la identitat de la sessió |
| `whoami /groups` | Consultar grups del testimoni de l'usuari |
| `gpupdate /force` | Forçar actualització de GPO |
| `gpresult /r` | Consultar les GPO processades |
| `gpresult /r` <br />(**En una sessió de Admin**) |  Consultar les GPO processades incloent les d'**equip** |
| `gpresult /h fitxer.html` | Generar un informe detallat de GPO |

Una bona diagnosi intenta comprovar els components **per ordre**, en lloc de canviar configuracions aleatòriament.

---

# 11. Resolució de problemes

## 11.1. El client no troba el domini

Si en intentar incorporar el client apareix un error indicant que no es pot contactar amb el domini, comprova:

**1. Configuració IP**

```text
ipconfig /all
```

Verifica que client i servidor es troben a la xarxa esperada.

**2. Connectivitat**

```text
ping <IP-del-DC>
```

**3. DNS configurat al client**

Comprova que el DNS apunta al servidor DNS del domini.

**4. Resolució del domini**

```text
nslookup empresa.test
```

**5. Registres de servei d'Active Directory**

```text
nslookup -type=SRV _ldap._tcp.dc._msdcs.empresa.test
```

Si DNS no funciona, **resol aquest problema abans de continuar amb la incorporació al domini**.

---

## 11.2. No puc incorporar el client al domini

Comprova:

- que Windows és Pro, Enterprise o Education;
- que el nom del domini és correcte;
- que DNS resol el domini;
- que existeix connectivitat amb el DC;
- que les credencials utilitzades tenen permisos suficients;
- que la data i hora dels equips són coherents.

Evita provar variants aleatòries del nom del domini. Utilitza DNS per comprovar primer què està resolent realment el client.

---

## 11.3. No puc iniciar sessió amb un usuari del domini

Comprova primer que no estiguis intentant iniciar una sessió local.

Pots indicar explícitament:

```text
DOMINI\usuari
```

o:

```text
usuari@domini
```

Comprova també:

- que l'usuari existeix;
- que està habilitat;
- que la contrasenya és correcta;
- que el client continua tenint connectivitat amb el domini.

Després d'iniciar sessió:

```text
whoami
```

---

## 11.4. L'usuari no té els grups esperats

Comprova a Active Directory la pertinença als grups.

Després, al client:

```text
whoami /groups
```

Si acabes de modificar els grups, **tanca la sessió i torna-la a iniciar**.

Si utilitzes anidament, comprova també les pertinences entre grups i no només la pertinença directa de l'usuari.

---

## 11.5. No puc accedir a un recurs compartit

Comprova, per ordre:

1. que el recurs existeix;
2. que la ruta UNC és correcta;
3. que el servidor és accessible;
4. que el recurs està compartit;
5. els permisos de compartició;
6. els permisos NTFS;
7. els grups de l'usuari;
8. si els canvis de grup requereixen una nova sessió.

Pots comprovar:

```text
whoami
whoami /groups
```

No resolguis el problema concedint permisos directament a l'usuari només per «veure si funciona» i deixant-los així. Localitza quin nivell de la cadena és incorrecte:

```text
Usuari → Grup → Permís → Recurs
```

---

## 11.6. Una GPO no s'aplica

Evita modificar opcions aleatòriament. Comprova, per ordre:

1. La GPO existeix i està habilitada.
2. Està vinculada a l'àmbit correcte.
3. L'usuari o equip es troba dins d'aquest àmbit.
4. Estàs configurant la part correcta: **User Configuration** o **Computer Configuration**.
5. El filtratge de seguretat inclou els destinataris correctes.
6. Els objectes necessaris poden llegir la GPO.
7. Els destinataris tenen **Apply Group Policy** quan correspon.
8. El client es comunica correctament amb el domini.

Després executa:

```text
gpupdate /force
```

i:

```text
gpresult /r
```

Si necessites més informació:

```text
gpresult /h informe-gpo.html
```

Consulta tant les polítiques aplicades com les que no s'han aplicat i intenta determinar-ne el motiu.

---

## 11.7. La GPO s'aplica a qui no correspon

Comprova:

- on està vinculada;
- quins usuaris o equips hi ha dins de l'àmbit;
- el **Security Filtering**;
- els grups als quals pertany l'usuari o equip;
- els permisos **Read** i **Apply Group Policy**.

No modifiquis el DIT o els grups només per aconseguir que una política funcioni una vegada. Intenta que la solució continuï sent coherent i mantenible.

---

## 11.8. He fet un canvi però el client continua igual

Active Directory és un sistema distribuït i alguns canvis no es reflecteixen necessàriament de manera immediata en una sessió ja iniciada.

Segons el canvi realitzat, pot ser necessari:

**Actualitzar GPO:**

```text
gpupdate /force
```

**Actualitzar el testimoni de l'usuari després d'un canvi de grup:**

tancar sessió i tornar-la a iniciar.

**Configuracions d'equip:**

algunes poden requerir reiniciar el client.

Abans de continuar modificant la configuració, identifica **quin tipus de canvi has fet** i quin mecanisme l'ha d'actualitzar.

---

# 12. Checklist final de verificació

Abans de considerar finalitzat un desplegament d'Active Directory, comprova:

- [ ] El controlador de domini té una adreça IP estàtica.
- [ ] El nom del servidor és el correcte.
- [ ] AD DS està instal·lat.
- [ ] El servidor ha estat promocionat a controlador de domini.
- [ ] DNS funciona i conté la informació necessària del domini.
- [ ] El client utilitza una edició de Windows compatible amb dominis.
- [ ] El client utilitza el DNS del domini.
- [ ] El client resol correctament el domini.
- [ ] El client està incorporat al domini.
- [ ] Es pot iniciar sessió amb un usuari del domini.
- [ ] `whoami` mostra la identitat esperada.
- [ ] `whoami /groups` mostra les pertinences esperades.
- [ ] Les OU permeten administrar adequadament els objectes.
- [ ] Els permisos sobre recursos s'assignen mitjançant grups.
- [ ] S'han realitzat proves positives i negatives d'accés.
- [ ] Els recursos DFS, si n'hi ha, apunten als recursos correctes.
- [ ] Les GPO estan vinculades a l'àmbit adequat.
- [ ] El filtratge i la delegació de les GPO són coherents.
- [ ] S'ha comprovat l'aplicació de les polítiques amb `gpresult`.
- [ ] Les configuracions s'han validat des del client i no únicament des del servidor.

> **Recorda:** que una configuració existeixi no demostra que funcioni. Una administració correcta acaba amb una **verificació del resultat**, i una bona verificació inclou tant els casos que han de funcionar com els que no ho han de fer.