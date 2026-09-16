# RA04 - Instal·lació, configuració i ús de serveis d’accés i administració remota

## Index
- [4.1 Concepte d’administració remota](#41-concepte-dadministracio-remota)
- [4.2 Terminals en mode text](#42-terminals-en-mode-text)
- [4.3 Accés remot amb SSH](#43-accés-remot-a-linux-amb-ssh)
- [4.4 Escriptori remot](#44-escriptori-remot)
- [4.5 VNC i eines gràfiques](#45-vnc-i-eines-gràfiques-dadministració-remota)
- [4.6 Protocols d’accés remot i ports](#46-protocols-daccés-remot-i-ports-implicats)
- [4.7 Serveis d’accés remot del sistema operatiu](#47-serveis-daccés-remot-del-sistema-operatiu)
- [4.8 Comparació accés textual vs gràfic](#48-comparació-entre-accés-textual-i-accés-gràfic)
- [4.9 Gestió d’usuaris per accés remot](#49-gestió-dusuaris-per-a-laccés-remot)
- [4.10 Proves de connexió remota](#410-proves-de-connexió-remota)
- [4.11 Documentació de la configuració](#411-documentació-de-la-configuració)

## 4.1 Concepte d’administració remota

### Definició

L’**administració remota** consisteix en la possibilitat de **gestionar un sistema informàtic des d’un altre equip connectat a la xarxa**, sense necessitat d’estar físicament davant del dispositiu.

Mitjançant aquest mecanisme, un administrador pot accedir a un servidor o ordinador remot per configurar-lo, mantenir-lo o solucionar incidències.

### Funcionament general

En una administració remota intervenen dos elements principals:

* **Client** → equip des del qual es realitza la connexió
* **Servidor** → equip que rep la connexió remota

El servidor executa un **servei d’accés remot** que escolta connexions a través d’un port de xarxa.

Quan el client inicia la connexió, el sistema comprova les credencials de l’usuari i, si són correctes, permet l’accés al sistema remot.

Esquema bàsic:

```text
Administrador (client)
        │
        │ connexió de xarxa
        │
Servidor (servei d'accés remot)
```

### Tasques que es poden realitzar remotament

Un cop establerta la connexió remota, l’administrador pot realitzar moltes operacions habituals d’administració de sistemes:

* consultar l’estat del sistema
* gestionar fitxers i directoris
* instal·lar o configurar serveis
* administrar usuaris i permisos
* executar ordres del sistema

Aquestes operacions es poden realitzar mitjançant **terminal (mode text)** o mitjançant **escriptori remot (mode gràfic)**.


### Avantatges de l’administració remota

L’ús de l’administració remota aporta diversos beneficis en la gestió dels sistemes informàtics:

* permet administrar servidors sense desplaçar-se
* facilita la gestió de múltiples equips
* redueix el temps de resolució d’incidències
* permet administrar sistemes situats en centres de dades o en altres ubicacions

Per aquest motiu, l’administració remota és una eina essencial en la gestió moderna d’infraestructures informàtiques.


### Exemple real

Un administrador pot connectar-se des del seu ordinador a un servidor Linux situat en una altra sala o en un centre de dades per executar ordres d’administració.

Per exemple:

```bash
ssh admin@192.168.1.10
```

Aquesta comanda estableix una connexió remota amb el servidor i permet administrar el sistema des de la terminal.


## 4.2 Terminals en mode text

### Definició

Una **terminal en mode text** és una interfície que permet interactuar amb el sistema operatiu mitjançant **ordres escrites** en lloc d’utilitzar una interfície gràfica.

Aquest tipus d’interfície és molt habitual en l’administració de sistemes, especialment en servidors Linux, on moltes tasques es realitzen mitjançant la línia d’ordres.


### Funcionament de la terminal

Quan un usuari obre una terminal, el sistema mostra un **prompt**, que indica que el sistema està preparat per rebre una comanda.

L’usuari escriu una ordre i el sistema operatiu l’executa.

Exemple de prompt:

```bash
javier@server:~$
```

A partir d’aquest moment es poden executar diferents ordres del sistema.


### Exemples d’ordres bàsiques

Algunes ordres habituals que es poden executar des d’una terminal són:

Consultar el directori actual:

```bash
pwd
```

Llistar els fitxers d’un directori:

```bash
ls
```

Canviar de directori:

```bash
cd /etc
```

Consultar informació del sistema:

```bash
uname -a
```

Aquestes ordres permeten gestionar el sistema de manera ràpida i eficient.


### Avantatges de la terminal

L’ús de terminals en mode text presenta diversos avantatges en l’administració de sistemes:

* consumeix menys recursos que una interfície gràfica
* permet executar tasques de manera ràpida
* facilita l’automatització mitjançant scripts
* és ideal per administrar servidors sense entorn gràfic

Per aquest motiu, moltes infraestructures de servidors es gestionen exclusivament mitjançant la terminal.


### Relació amb l’administració remota

Les terminals en mode text també són la base de molts sistemes d’administració remota.

Protocols com **SSH** permeten obrir una **terminal remota** en un servidor Linux i executar ordres des d’un altre equip de la xarxa.

Això significa que l’administrador pot treballar en un sistema remot com si estigués davant del seu terminal local.


## 4.3 Accés remot a Linux amb SSH

### Definició

**SSH (Secure Shell)** és un protocol que permet **connectar-se de forma segura a un sistema remot** a través de la xarxa.

Mitjançant SSH, un administrador pot obrir una **terminal remota** en un servidor Linux i executar ordres com si estigués treballant directament davant del sistema.

SSH utilitza **xifratge** per protegir la comunicació entre el client i el servidor.


### Funcionament

SSH funciona amb un model **client-servidor**:

* **Servidor SSH** → servei que s’executa al sistema remot
* **Client SSH** → aplicació utilitzada per connectar-se al servidor

El servidor SSH escolta connexions per defecte al:

```text
port 22
```

Quan un usuari inicia la connexió, el sistema sol·licita **usuari i contrasenya** o bé utilitza **claus d’autenticació**.


### Comanda bàsica de connexió

La forma més habitual de connectar-se a un servidor és:

```bash
ssh usuari@host
```

Exemple:

```bash
ssh admin@192.168.1.20
```

Si la connexió és correcta, el sistema demanarà la contrasenya:

```text
admin@192.168.1.20's password:
```

Un cop autenticat, s’obre la terminal del sistema remot.


### Exemple de sessió SSH

```bash
javier@pc:~$ ssh admin@192.168.1.20
admin@192.168.1.20's password:

admin@server:~$
```

A partir d’aquest moment es poden executar ordres al servidor remot.

Per exemple:

```bash
ls
```

o bé:

```bash
sudo systemctl status ssh
```

### Connexió a un port diferent

Si el servidor utilitza un port diferent del 22, cal indicar-lo amb l’opció **-p**:

```bash
ssh -p 2222 usuari@host
```

Exemple:

```bash
ssh -p 2222 admin@192.168.1.20
```

### Avantatges d’SSH

SSH és el sistema d’administració remota més utilitzat en entorns Linux perquè:

* la connexió està xifrada
* és molt lleuger
* permet executar ordres remotament
* permet transferir fitxers de manera segura

Per aquest motiu, SSH és una eina fonamental per als administradors de sistemes.


## 4.4 Escriptori remot

### Definició

L’**escriptori remot** és una tecnologia que permet **controlar l’escriptori gràfic d’un sistema remot** a través de la xarxa.

Mitjançant aquest mecanisme, l’usuari pot veure la pantalla del sistema remot i interactuar amb ell com si estigués treballant directament davant de l’ordinador.

A diferència de l’administració mitjançant terminal, l’escriptori remot permet utilitzar **aplicacions amb interfície gràfica**.


### Funcionament

Quan es realitza una connexió d’escriptori remot, el sistema remot envia la **imatge de la pantalla** al client.

Al mateix temps, el client envia al servidor les **accions de l’usuari**, com ara:

* moviments del ratolí
* clics
* pulsacions de teclat

Esquema simplificat:

```text
Client
(teclat i ratolí)
      │
      │ connexió de xarxa
      │
Servidor
(escriptori remot)
```

Això permet que l’usuari pugui treballar amb el sistema remot igual que si estigués davant del dispositiu.


### Protocols d’escriptori remot

Hi ha diversos protocols utilitzats per accedir a escriptoris remots. Alguns dels més coneguts són:

| Protocol       | Sistema habitual |
| -- | - |
| RDP            | Windows          |
| VNC            | multiplataforma  |
| X11 forwarding | Linux            |

Cada protocol utilitza un **port de xarxa diferent** per establir la connexió.


### Exemple d’ús en Windows (RDP)

En sistemes Windows és habitual utilitzar **Remote Desktop Protocol (RDP)**.

El port utilitzat per defecte és:

```text
3389
```

El client d’escriptori remot permet introduir l’adreça IP o el nom del servidor per establir la connexió.

Exemple:

```text
Servidor: 192.168.1.50
Usuari: administrador
```

Un cop autenticat, s’obre l’escriptori del sistema remot.


### Avantatges de l’escriptori remot

L’escriptori remot és especialment útil quan:

* cal administrar sistemes amb interfície gràfica
* s’ha de proporcionar suport tècnic a usuaris
* es necessita accedir a aplicacions gràfiques instal·lades en un servidor

Tot i això, aquest tipus de connexió consumeix **més recursos de xarxa** que una terminal remota.

Per aquest motiu, en entorns d’administració de servidors sovint es prefereix utilitzar **SSH**, reservant l’escriptori remot per a situacions específiques.

## 4.4.1 Accés remot en Windows amb RDP

### Definició

**RDP (Remote Desktop Protocol)** és un protocol desenvolupat per Microsoft que permet **connectar-se remotament a un sistema Windows i controlar el seu escriptori gràfic**.

Mitjançant RDP, l’usuari pot veure la pantalla del sistema remot i interactuar amb ell utilitzant el teclat i el ratolí, com si estigués treballant directament davant de l’ordinador.

Aquest protocol és molt utilitzat en entorns Windows per a tasques d’administració remota i suport tècnic.

---

### Funcionament

RDP utilitza un model **client-servidor**:

* **Servidor RDP** → sistema Windows que permet connexions remotes
* **Client RDP** → aplicació utilitzada per connectar-se al sistema remot

Quan un usuari inicia una connexió, el servidor envia al client **la imatge de l’escriptori remot**, mentre que el client envia al servidor **les accions de l’usuari** (teclat i ratolí).

Esquema simplificat:

```text
Client RDP
(teclat i ratolí)
        │
        │ connexió de xarxa
        │
Servidor RDP
(escriptori remot Windows)
```

---

### Port utilitzat

RDP utilitza per defecte el port:

```text
3389
```

Aquest port pot ser controlat mitjançant **firewalls** o modificat per millorar la seguretat del sistema.

---

### Exemple de connexió

En sistemes Windows es pot utilitzar l’aplicació **Remote Desktop Connection**.

L’usuari introdueix l’adreça del servidor:

```text
192.168.1.50
```

Després d’introduir les credencials, el sistema obre **l’escriptori remot del servidor**.

---

### Avantatges de RDP

RDP és molt utilitzat perquè:

* permet controlar completament l’escriptori remot
* està integrat en sistemes Windows
* facilita el suport tècnic i l’administració remota
* permet executar aplicacions gràfiques instal·lades en el servidor

Per aquest motiu és una eina habitual en la gestió de servidors i estacions de treball Windows.

## 4.5 VNC i eines gràfiques d’administració remota

### Definició

**VNC (Virtual Network Computing)** és una tecnologia que permet **controlar l’escriptori d’un sistema remot a través de la xarxa**.

A diferència d’altres protocols, VNC és **multiplataforma**, és a dir, es pot utilitzar entre diferents sistemes operatius com Linux, Windows o macOS.

Mitjançant VNC, un usuari pot veure la pantalla d’un altre ordinador i interactuar amb ell utilitzant el teclat i el ratolí.


### Funcionament

El funcionament de VNC també segueix el model **client-servidor**:

* **Servidor VNC** → programa instal·lat al sistema remot
* **Client VNC** → aplicació utilitzada per connectar-se al servidor

El servidor captura la pantalla del sistema remot i envia la imatge al client. El client envia les accions de l’usuari (ratolí i teclat) al servidor.

Esquema:

```text
Client VNC
(teclat i ratolí)
        │
        │ connexió de xarxa
        │
Servidor VNC
(escriptori remot)
```


### Port utilitzat

VNC utilitza habitualment el port:

```text
5900
```

Cada sessió VNC pot utilitzar un port diferent a partir d’aquest.

Per exemple:

```text
5900 → display 0
5901 → display 1
```


### Exemple de connexió

Per connectar-se a un servidor VNC s’utilitza un client VNC indicant l’adreça IP del servidor.

Exemple:

```text
192.168.1.20:5900
```

Un cop establerta la connexió, l’usuari pot veure i controlar l’escriptori del sistema remot.


### Avantatges de VNC

VNC és una eina molt utilitzada perquè:

* funciona entre diferents sistemes operatius
* permet controlar l’escriptori remot complet
* és útil per assistència tècnica o administració gràfica

Tot i això, la transmissió de la pantalla pot consumir més amplada de banda que altres mètodes d’administració remota.

Per aquest motiu, en entorns de servidors sovint es prefereix utilitzar **SSH** quan és possible.


# 4.6 Protocols d’accés remot i ports implicats

### Definició

Els serveis d’administració remota utilitzen **protocols de comunicació de xarxa** per establir connexions entre el client i el servidor.

Cada protocol utilitza **un port de xarxa específic** per identificar el servei que està escoltant les connexions al sistema.

Un **port** és un número lògic que permet al sistema operatiu dirigir les connexions de xarxa cap al servei correcte.

Quan un client intenta connectar-se a un servidor, ho fa indicant:

* l’adreça **IP o nom del servidor**
* el **port del servei**


### Protocols més utilitzats en administració remota

Els protocols d’accés remot més habituals són:

| Protocol | Funció                           | Port per defecte |
| -- | -- | - |
| SSH      | terminal remota segura           | 22               |
| RDP      | escriptori remot Windows         | 3389             |
| VNC      | escriptori remot multiplataforma | 5900             |


### Exemple de connexió SSH

Quan un usuari executa la comanda:

```bash
ssh admin@192.168.1.20
```

El client SSH intenta connectar-se al servidor a través del:

```text
port 22
```

Si el servidor SSH està actiu i el port està obert, la connexió es pot establir.


### Exemple de connexió RDP

En sistemes Windows, el client d’escriptori remot utilitza el protocol **RDP**.

Aquest protocol es connecta al servidor a través del port:

```text
3389
```

L’usuari introdueix l’adreça del servidor per iniciar la connexió.


### Importància dels ports en administració de sistemes

Els ports són un element clau en la configuració dels serveis d’accés remot perquè:

* permeten identificar el servei de xarxa
* es poden controlar mitjançant **firewalls**
* permeten millorar la seguretat del sistema

Per exemple, alguns administradors configuren el servei SSH en un port diferent del 22 per reduir intents d’accés no autoritzat.

Exemple:

```bash
ssh -p 2222 admin@192.168.1.20
```

## 4.7 Serveis d’accés remot del sistema operatiu

### Definició

Els **serveis d’accés remot** són programes que s’executen en un sistema operatiu i permeten que altres equips de la xarxa es connectin al sistema.

Aquests serveis funcionen en **segon pla** i escolten connexions entrants a través d’un port de xarxa determinat.

Quan un client intenta connectar-se, el servei comprova les credencials de l’usuari i, si són correctes, permet l’accés al sistema.


### Model client-servidor

Els serveis d’administració remota funcionen amb un model **client-servidor**.

* **Servidor** → sistema que ofereix el servei remot
* **Client** → aplicació que utilitza l’usuari per connectar-se

Esquema simplificat:

```text
Client
(ssh, rdp, vnc)
      │
      │ connexió de xarxa
      │
Servidor
(servei d'accés remot)
```

### Exemple en Linux: servei SSH

En sistemes Linux, el servei d’accés remot més habitual és **SSH**.

Aquest servei s’executa normalment com a:

```text
sshd
```

Es pot comprovar si el servei està actiu amb la comanda:

```bash
systemctl status ssh
```

Si el servei no està actiu, es pot iniciar amb:

```bash
sudo systemctl start ssh
```

### Exemple en Windows: servei RDP

En sistemes Windows, el servei d’escriptori remot es basa en el protocol **RDP**.

Aquest servei permet connectar-se al sistema utilitzant un client d’escriptori remot.

Per utilitzar-lo, cal:

* activar l’escriptori remot al sistema
* permetre connexions remotes
* configurar els usuaris autoritzats


### Importància dels serveis d’accés remot

Els serveis d’accés remot són essencials en la gestió de sistemes perquè permeten:

* administrar servidors sense accés físic
* gestionar equips situats en altres ubicacions
* realitzar manteniment o diagnòstic remot

Tanmateix, és important configurar-los correctament i aplicar mesures de seguretat adequades per evitar accessos no autoritzats.


## 4.8 Comparació entre accés textual i accés gràfic

### Tipus d’administració remota

Els sistemes informàtics es poden administrar remotament de dues maneres principals:

* **accés textual (terminal)**
* **accés gràfic (escriptori remot)**

Cada mètode presenta característiques diferents i s’utilitza en funció de les necessitats de l’administració del sistema.

### Accés textual

L’accés textual permet administrar el sistema mitjançant **una terminal de comandes**.

Aquest mètode és molt habitual en sistemes Linux i en la gestió de servidors.

Normalment s’utilitza el protocol **SSH**.

Exemple de connexió:

```bash
ssh admin@192.168.1.20
```

Avantatges de l’accés textual:

* consumeix pocs recursos de xarxa
* és molt ràpid
* permet automatitzar tasques mitjançant scripts
* és ideal per administrar servidors sense entorn gràfic

### Accés gràfic

L’accés gràfic permet controlar el sistema remot mitjançant **la seva interfície gràfica**, veient la pantalla completa del sistema.

Aquest tipus d’accés utilitza protocols com:

* **RDP**
* **VNC**

L’usuari pot interactuar amb finestres, aplicacions i configuracions del sistema.

Exemple de connexió:

```text
Client RDP → 192.168.1.50
```

### Comparació dels dos mètodes

| Característica        | Accés textual                     | Accés gràfic |
|  |  |  |
| Recursos de xarxa     | baixos                            | més elevats  |
| Velocitat             | molt alta                         | menor        |
| Facilitat d’ús        | requereix coneixement de comandes | més intuïtiu |
| Aplicacions gràfiques | no                                | sí           |
| Protocol habitual     | SSH                               | RDP / VNC    |


### Quan utilitzar cada mètode

En entorns professionals és habitual combinar els dos tipus d’accés.

Normalment:

* **SSH** s’utilitza per administrar servidors Linux
* **RDP o VNC** s’utilitzen quan cal treballar amb aplicacions gràfiques o proporcionar suport tècnic

Per aquest motiu, un administrador de sistemes ha de saber utilitzar **tant terminals remotes com escriptoris remots**.


## 4.9 Gestió d’usuaris per a l’accés remot

### Definició

Quan es configura un servei d’administració remota, és necessari controlar **quins usuaris poden connectar-se al sistema**.

La gestió d’usuaris permet **limitar l’accés al sistema remot només a les persones autoritzades**, millorant la seguretat del servidor.

### Usuaris del sistema

Els serveis d’accés remot utilitzen **els comptes d’usuari del sistema operatiu** per autenticar les connexions.

Per exemple, quan un usuari es connecta mitjançant SSH:

```bash
ssh admin@192.168.1.20
```

El servidor comprova si:

* l’usuari **admin** existeix
* la contrasenya és correcta
* l’usuari té permís per accedir remotament

### Exemple en Linux

Per veure els usuaris existents al sistema es pot consultar el fitxer:

```bash
cat /etc/passwd
```

També es pot crear un usuari nou amb:

```bash
sudo adduser usuari
```

Un cop creat, aquest usuari podrà connectar-se al sistema mitjançant SSH si el servei està actiu.


### Restricció d’accés

Per millorar la seguretat, és habitual restringir quins usuaris poden accedir remotament.

En SSH això es pot configurar en el fitxer:

```text
/etc/ssh/sshd_config
```

Per exemple:

```text
AllowUsers admin javier
```

Aquesta configuració permet que només aquests usuaris puguin connectar-se al servidor.


### Bones pràctiques de seguretat

En l’administració remota és recomanable seguir algunes bones pràctiques:

* limitar els usuaris amb accés remot
* utilitzar contrasenyes robustes
* evitar comptes compartits
* registrar les connexions al sistema

Aquestes mesures ajuden a protegir el sistema davant accessos no autoritzats.


## 4.10 Proves de connexió remota

### Objectiu

Després d’instal·lar i configurar un servei d’accés remot, és necessari **comprovar que la connexió funciona correctament**.

Això es fa realitzant **proves de connexió des d’un altre equip de la xarxa**.

Aquest procés permet verificar que:

* el servei està actiu
* el port està obert
* els usuaris poden autenticar-se correctament


### Verificar que el servei està actiu

Abans de provar la connexió, cal comprovar que el servei està funcionant.

En Linux es pot utilitzar la comanda:

```bash id="p6jv1f"
systemctl status ssh
```

Si el servei està actiu, apareixerà com:

```text id="y8r1ju"
active (running)
```

### Provar la connexió SSH

Des d’un altre equip de la xarxa es pot intentar establir una connexió SSH.

Exemple:

```bash id="jfnj4p"
ssh admin@192.168.1.20
```

Si la configuració és correcta, el sistema demanarà la contrasenya de l’usuari.


### Comprovar la connectivitat de xarxa

Si la connexió no funciona, primer es pot comprovar la connectivitat amb:

```bash id="m4ww5v"
ping 192.168.1.20
```

Això permet verificar que els dos equips poden comunicar-se a través de la xarxa.


### Comprovar el port del servei

També es pot verificar si el port del servei està obert.

Per exemple, el port SSH és el **22**.

Algunes eines permeten comprovar si el port està accessible des de la xarxa.

Si el port està bloquejat per un **firewall**, la connexió no es podrà establir.


### Importància de les proves

Les proves de connexió són una part fonamental del procés d’administració de sistemes perquè permeten:

* detectar errors de configuració
* comprovar que el servei funciona correctament
* verificar la connectivitat entre equips

Aquest procés és especialment important abans de posar un sistema en producció.


## 4.11 Documentació de la configuració

### Importància de la documentació

Quan es configura un servei d’administració remota és molt important **documentar totes les configuracions realitzades**.

La documentació permet que altres administradors puguin entendre com està configurat el sistema i facilita el manteniment o la resolució d’incidències.

En entorns professionals és habitual mantenir **registres tècnics de les configuracions dels servidors**.


### Informació que s’ha de documentar

Quan es configura un servei d’accés remot, és recomanable documentar almenys els següents elements:

* servei instal·lat
* protocol utilitzat
* port de connexió
* usuaris autoritzats
* configuracions de seguretat aplicades

Aquesta informació permet reconstruir la configuració del sistema en cas de problema o migració.


### Exemple de documentació

Un exemple simple de documentació d’un servidor SSH podria ser:

```text
Servei: SSH
Servidor: 192.168.1.20
Port: 22
Usuaris autoritzats: admin, javier
Fitxer de configuració: /etc/ssh/sshd_config
```

Aquest tipus d’informació pot guardar-se en documents tècnics o sistemes de gestió d’infraestructura.


### Avantatges de documentar la configuració

La documentació d’un sistema ofereix diversos avantatges:

* facilita el manteniment dels servidors
* permet reproduir la configuració en altres sistemes
* ajuda a diagnosticar incidències
* millora la gestió dels sistemes en equips d’administradors

Per aquest motiu, la documentació és una pràctica essencial dins de l’administració de sistemes.

