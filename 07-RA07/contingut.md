# RA07 - Llenguatges de guions en sistemes operatius

## Índex
- [7.1 Context dels llenguatges de guions en l’administració de sistemes](#71-context-dels-llenguatges-de-guions-en-ladministració-de-sistemes)
- [7.2 Bash com a entorn de guions en GNU/Linux](#72-bash-com-a-entorn-de-guions-en-gnulinux)
- [7.3 Estructures bàsiques del llenguatge en Bash](#73-estructures-bàsiques-del-llenguatge-en-bash)
- [7.4 Administració de comptes d’usuari amb Bash](#74-administració-de-comptes-dusuari-amb-bash)
- [7.5 Administració de processos amb Bash](#75-administració-de-processos-amb-bash)
- [7.6 Administració de serveis amb Bash](#76-administració-de-serveis-amb-bash)
- [7.7 Consulta d’ordres, ajuda i funcions disponibles a GNU/Linux](#77-consulta-dordres-ajuda-i-funcions-disponibles-a-gnulinux)
- [7.8 Documentació, comprovació i depuració bàsica dels guions](#78-documentació-comprovació-i-depuració-bàsica-dels-guions)
- [7.9 Execució programada de guions a GNU/Linux](#79-execució-programada-de-guions-a-gnulinux)
- [7.10 Entorn complementari en sistemes propietaris: PowerShell](#710-entorn-complementari-en-sistemes-propietaris-powershell)
- [7.11 Implantació en sistemes lliures i propietaris i orientació pràctica de la RA07](#711-implantació-en-sistemes-lliures-i-propietaris-i-orientació-pràctica-de-la-ra07)

## 7.1 Context dels llenguatges de guions en l’administració de sistemes

Un llenguatge de guions és un llenguatge pensat per escriure ordres en forma de fitxer perquè el sistema les executi una darrere l’altra. En administració de sistemes, això permet convertir tasques habituals en seqüències repetibles, controlables i més eficients.

La idea central d’aquesta RA és que moltes accions del sistema no s’han de fer sempre manualment. Consultar usuaris, revisar processos, comprovar serveis, llançar ordres de manteniment o repetir comprovacions es pot resoldre millor amb guions.

Dins d’aquesta unitat apareixen dos entorns generals:

- **GNU/Linux**, on el treball amb **Bash** i amb scripts de shell encaixa directament amb l’administració del sistema
- **Windows**, on **PowerShell** permet una lògica semblant dins d’un entorn propietari

Ara bé, en el desenvolupament pràctic d’aquesta RA el pes principal recau en **GNU/Linux**. Això vol dir que la major part d’exemples, pràctiques i aplicacions administratives es poden centrar en **Bash**, en l’ús d’ordres del sistema i en la construcció de scripts orientats a tasques reals.

Per tant, el paper dels llenguatges de guions queda lligat a aquestes idees bàsiques:

- administració del sistema des de línia d’ordres
- combinació de comandes per gestionar usuaris, processos i serveis
- reutilització d’ordres dins d’un mateix script
- execució controlada o programada de guions quan convingui

Això dona sentit a la RA07: no es tracta només de memoritzar ordres soltes, sinó d’entendre com es poden combinar dins d’un guió per fer l’administració més estable, més clara i més repetible.

## 7.2 Bash com a entorn de guions en GNU/Linux

En GNU/Linux, el llenguatge de guions més habitual és **Bash**. Quan es treballa des del terminal, moltes ordres es poden executar una per una, però també es poden escriure dins d’un fitxer de text i executar-se com un bloc únic. Aquest fitxer és un **script de shell**.

Un script de Bash no és més que una seqüència d’ordres, variables i estructures bàsiques de control que el sistema interpreta i executa pas a pas. Això permet automatitzar comprovacions, reutilitzar procediments i deixar preparades tasques d’administració que es poden tornar a executar sempre que faci falta.

La forma més habitual de començar un script és aquesta:

```bash
#!/bin/bash
```

Aquesta primera línia indica quin intèrpret ha d’executar el fitxer. A partir d’aquí, el guió pot incorporar ordres del sistema, comentaris, variables i estructures de control.

Exemple mínim:

```bash
#!/bin/bash
echo "Iniciant comprovació del sistema"
whoami
date
```

Aquest exemple mostra una idea molt important: Bash no és només una consola interactiva, sinó també un entorn de guions on es poden reunir diverses ordres dins d’un sol fitxer executable.

Per això, dins de la RA07, Bash encaixa molt bé com a base per treballar:

- scripts de comprovació d’usuaris
- scripts de consulta de processos
- scripts de revisió de serveis
- guions de manteniment i control bàsic del sistema

## 7.3 Estructures bàsiques del llenguatge en Bash

En Bash, les estructures bàsiques del llenguatge es veuen sobretot en la manera d’escriure ordres, guardar valors i controlar el flux del guió.

### Ordres amb nom i paràmetres

```bash
ls -l /home
# ls és la comanda
# -l és un paràmetre
# /home és la ruta sobre la qual actua
```

### Comandes combinades

```bash
ps aux | grep ssh
# ps aux mostra processos
# grep ssh filtra els que contenen ssh
```

### Filtres i selecció de resultats

```bash
cat /etc/passwd | grep javier
# cat /etc/passwd mostra el contingut del fitxer
# grep javier selecciona només les línies on surt javier
```

### Variables

```bash
USUARI="javier"
echo $USUARI
# USUARI guarda un valor
# echo $USUARI mostra el contingut de la variable
```

### Condicionals `if`

```bash
if id "$USUARI" >/dev/null 2>&1; then
    echo "L'usuari existeix"
else
    echo "L'usuari no existeix"
fi
# if comprova una condició
# then executa el bloc si es compleix
# else executa el bloc alternatiu
# fi tanca l'estructura
```

### Bucles `for`

```bash
for FITXER in *.txt; do
    echo $FITXER
done
# for recorre una llista d'elements
# do inicia el bloc
# done tanca el bucle
```

### Bucles `while`

```bash
COMPTADOR=1
while [ $COMPTADOR -le 3 ]; do
    echo $COMPTADOR
    COMPTADOR=$((COMPTADOR+1))
done
# while repeteix el bloc mentre es compleixi la condició
```

Aquestes estructures són suficients per començar a construir scripts útils d’administració. Amb elles es poden fer comprovacions, repetir accions, filtrar informació i prendre decisions segons el resultat de les ordres.

## 7.4 Administració de comptes d’usuari amb Bash

Un dels usos més naturals d’un guió en GNU/Linux és la comprovació i gestió bàsica de comptes d’usuari. No cal que un script creï usuaris de manera automàtica per ser útil. També pot comprovar si existeixen, mostrar informació rellevant o validar dades abans de fer una acció administrativa.

### Comprovar si un usuari existeix

```bash
id alumnera07
```

Aquesta ordre retorna informació de l’usuari si existeix. Si no existeix, dona error. Això permet fer comprovacions dins d’un `if`.

### Consultar la base local d’usuaris

```bash
getent passwd alumnera07
```

Aquesta ordre consulta l’entrada corresponent a l’usuari dins de la base de comptes del sistema.

### Veure el fitxer de comptes

```bash
cat /etc/passwd
```

Aquesta ordre mostra el contingut del fitxer on apareixen els comptes locals.

### Exemple de comprovació dins d’un guió

```bash
USUARI="alumnera07"

if id "$USUARI" >/dev/null 2>&1; then
    echo "L'usuari $USUARI existeix"
    getent passwd "$USUARI"
else
    echo "L'usuari $USUARI no existeix"
fi
```

Aquest bloc ja representa molt bé la lògica de la RA07: una ordre no es fa servir sola, sinó integrada dins d’un guió que comprova, decideix i mostra resultats.

En una versió més avançada, també es poden emprar ordres com `useradd`, `usermod` o `userdel`, però fins i tot sense arribar-hi ja es pot treballar el criteri curricular de construir guions útils per a l’administració de comptes.

## 7.5 Administració de processos amb Bash

Els processos també es presten molt bé al treball amb scripts. En lloc d’obrir eines gràfiques, es poden consultar, filtrar i tractar directament des del terminal.

### Consultar processos

```bash
ps aux
```

Mostra els processos actius del sistema.

### Buscar un procés concret

```bash
pgrep bash
```

Permet localitzar processos pel seu nom.

### Filtrar amb `ps` i `grep`

```bash
ps -ef | grep ssh
```

Aquesta combinació mostra com una informació general es pot filtrar per obtenir només allò que interessa.

### Exemple de comprovació dins d’un script

```bash
PROCES="bash"

if pgrep "$PROCES" >/dev/null 2>&1; then
    echo "El procés $PROCES està en execució"
else
    echo "El procés $PROCES no està en execució"
fi
```

Això permet construir guions senzills de supervisió o verificació. En una línia semblant, també es podrien fer servir ordres com `kill` o `pkill` quan el guió hagi d’actuar sobre processos, però el nucli formatiu és entendre la lògica de consulta, filtratge i decisió.

## 7.6 Administració de serveis amb Bash

Una altra aplicació molt clara dels guions és la gestió de serveis del sistema. En entorns GNU/Linux actuals, això es fa habitualment amb `systemctl`.

### Consultar l’estat d’un servei

```bash
systemctl status ssh
```

Aquesta ordre mostra informació completa del servei.

### Comprovar si està actiu

```bash
systemctl is-active ssh
```

Aquesta ordre simplifica la comprovació i és especialment útil dins d’un script.

### Exemple de comprovació dins d’un guió

```bash
SERVEI="ssh"

if systemctl is-active "$SERVEI" >/dev/null 2>&1; then
    echo "El servei $SERVEI està actiu"
else
    echo "El servei $SERVEI no està actiu"
fi
```

### Altres accions habituals

```bash
systemctl start ssh
systemctl stop ssh
systemctl restart ssh
```

Aquest bloc connecta directament amb un dels objectius més clars de la RA07: construir guions per a l’administració de serveis del sistema operatiu.

## 7.7 Consulta d’ordres, ajuda i funcions disponibles a GNU/Linux

Abans d’escriure o adaptar un guió, cal saber quines ordres estan disponibles i com funcionen. En GNU/Linux això es resol, sobretot, amb eines de consulta i ajuda.

### Consultar el manual d’una ordre

```bash
man ps
man systemctl
man id
```

### Veure ajuda interna de Bash

```bash
help
help echo
```

### Saber on és una ordre

```bash
which bash
which systemctl
```

### Saber de quin tipus és una ordre

```bash
type echo
type cd
type ls
```

Aquestes ordres són molt importants dins de la RA07 perquè mostren que un guió no s’escriu des de zero sense referències. Sovint es construeix consultant l’entorn, reutilitzant ordres existents i combinant-les de manera controlada.

Si la pràctica de la unitat es focalitza en Linux, aquest apartat guanya encara més pes, perquè ajuda l’alumne a entendre d’on surten les ordres que després incorporarà als seus scripts.

## 7.8 Documentació, comprovació i depuració bàsica dels guions

Un guió no s’hauria de deixar sense documentar. Fins i tot en scripts senzills, convé identificar-ne la finalitat i separar-ne bé els blocs.

Exemple d’inici documentat:

```bash
#!/bin/bash
# Pràctica RA07
# Autor: Nom Cognoms
# Objectiu: Comprovar usuaris, processos i serveis
```

Els comentaris amb `#` permeten explicar què fa cada part del guió i faciliten molt la lectura posterior.

A més de documentar, també cal comprovar i depurar. En aquesta RA això no s’ha d’entendre com una teoria molt avançada, sinó com un conjunt d’accions bàsiques:

- revisar si la sintaxi està ben escrita
- verificar si una ordre retorna el resultat esperat
- comprovar si les condicions `if` entren al bloc correcte
- veure si una variable té el valor previst
- detectar errors de permisos o d’ordres inexistents

Exemple útil de depuració bàsica:

```bash
echo $USUARI
```

Això permet veure si la variable conté realment el valor que esperàvem.

També és important donar permís d’execució al fitxer:

```bash
chmod +x ra07_admin_linux.sh
```

I executar-lo així:

```bash
./ra07_admin_linux.sh
```

Per tant, documentar i depurar no són parts separades del treball amb scripts, sinó una part normal del seu ús real.

## 7.9 Execució programada de guions a GNU/Linux

Tot i que el nucli de la RA07 queda millor representat per Bash i pels scripts d’administració, a GNU/Linux també existeixen eines que permeten executar aquests guions en moments concrets.

### Cron

`cron` és el servei que comprova si hi ha ordres o scripts que s’hagin d’executar segons una programació establerta.

```bash
service crond status
apt-get install cron
```

### Crontab

`crontab` permet editar o consultar la programació de tasques d’un usuari.

```bash
crontab -l
crontab -e
```

Exemple de línia programada:

```bash
30 0 * * * root find /tmp -type f -empty -delete
```

### Anacron

`anacron` serveix per executar tasques que no s’han pogut llançar en el moment previst perquè el sistema estava apagat.

```bash
apt-get install anacron
```

### At

`at` permet llançar una ordre o un script una sola vegada en un moment determinat.

```bash
apt-get install at
at 12am tomorrow < script.sh
```

Aquest bloc és útil perquè mostra una aplicació complementària dels guions en GNU/Linux: un script no només es pot executar manualment, sinó també deixar-se preparat per a una execució posterior.

## 7.10 Entorn complementari en sistemes propietaris: PowerShell

Encara que el focus pràctic de la unitat es desplaci cap a GNU/Linux, continua sent útil mantenir una referència complementària a **PowerShell** com a entorn de guions en Windows.

PowerShell és una línia de comandes i també un entorn basat en scripts. En aquest cas, l’ordre bàsica pròpia és el **cmdlet**, i moltes ordres segueixen el format **Verb-Noun**.

Exemple:

```powershell
Get-Command -CommandType cmdlet | Measure-Object
# Get-Command mostra les comandes disponibles a PowerShell
# -CommandType cmdlet filtra perquè només surtin els cmdlets
# Measure-Object compta els elements rebuts
```

A PowerShell també es poden treballar tasques semblants a les de GNU/Linux:

- consultar usuaris amb `Get-LocalUser`
- consultar processos amb `Get-Process`
- consultar serveis amb `Get-Service`

## 7.11 Implantació en sistemes lliures i propietaris i orientació pràctica de la RA07

El bloc principal queda així:

- **GNU/Linux** com a entorn principal de pràctica
- **Bash** com a llenguatge de guions principal
- scripts centrats en **usuaris**, **processos** i **serveis**
- execució programada de guions com a aplicació complementària

I el bloc complementari queda així:

- **Windows** com a entorn propietari de referència
- **PowerShell** com a exemple paral·lel d’administració per scripts



