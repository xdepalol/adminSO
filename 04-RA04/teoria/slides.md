---
marp: true
paginate: true
theme: default

header: "RA04 - Administració remota del sistema"
footer: "Mòdul 0374 - Administració de Sistemes Operatius"

style: |
    header, footer {
        display: block;
        width: 92vw;
        font-size: .45rem;
        color: #bbbbbcff;
        z-index: 10;
    }
    header { text-align: right !important; padding-right: 0 !important; }
    section {
        display: flex !important;
        flex-direction: column !important;
        justify-content: flex-start !important;
    }

---

# RA04  
## Administració remota del sistema

Administració de Sistemes Operatius  
ASIX02

---

# Context de l'administració remota

Els sistemes sovint no estan físicament a prop de l’administrador.

Exemples:

- servidors en **CPD**
- equips en altres edificis
- sistemes en cloud
- laboratoris o xarxes grans

Per això és necessari **administrar-los remotament**.

---

# Què és un CPD

**CPD = Centre de Processament de Dades**

És una instal·lació on s’allotgen:

- servidors
- sistemes d’emmagatzematge
- equips de xarxa
- sistemes de seguretat

Normalment inclou:

- control de temperatura
- alimentació redundant
- sistemes antiincendi
- accés físic restringit

---

# 4.1 Administració remota

Permet gestionar un sistema remot per:

- configurar serveis
- administrar usuaris
- instal·lar programes
- solucionar incidències

Sense accedir físicament al servidor.

---

# Model client-servidor

```

Administrador (client)
│
│ connexió de xarxa
│
Servidor (servei remot)

````

El servidor executa un **servei d'accés remot**.

---

# Exemple de connexió remota

Connexió a un servidor Linux amb SSH:

```bash
ssh admin@192.168.1.10
````

El sistema demanarà la contrasenya i obrirà la terminal remota.

---

# 4.2 Terminal en mode text

Una **terminal** permet interactuar amb el sistema mitjançant ordres.

Exemple de prompt:

```bash
javier@server:~$
```

El sistema espera una comanda.

---

# Informació del sistema

Exemple:

```bash
uname -a
```

Mostra informació del sistema operatiu i del kernel

Exemple de sortida:
```bash
Linux servidor 6.5.0-21-generic #21-Ubuntu SMP x86_64 GNU/Linux
```

---

# Avantatges de la terminal

* molt ràpida
* poc consum de recursos
* fàcil automatització
* ideal per servidors

Per això **molts servidors no tenen entorn gràfic**.

---

# 4.3 SSH

**SSH (Secure Shell)** és el protocol principal per administrar Linux remotament.

Característiques:

* connexió xifrada
* autenticació segura
* execució de comandes remotes

A Windows també s'utilitza encara que històricament no el portava integrat i es feien servir altres eines

---

# Funcionament d’SSH

```
Client                         Servidor
------                         --------

ssh   ───── connexió SSH ───►  sshd
```

El servidor escolta connexions al **port 22**

SSH és el programa que utilitza l’usuari per iniciar una connexió remota

SSHD és el programa que funciona al servidor i que espera connexions entrants

---

# Exemple de connexió SSH

```bash
ssh admin@192.168.1.20
```
Sessió típica:

```
javier@pc:~$ ssh admin@192.168.1.20
admin@192.168.1.20's password:

admin@server:~$
```

---

# Connexió a un port diferent

Si el servidor utilitza un altre port:

**~/.ssh/config** > podem canviar port per connectar-se des de client

ó

```bash
ssh -p 2222 admin@192.168.1.20
```

---

# 4.4 Escriptori remot

Permet controlar el sistema remot **mitjançant interfície gràfica**.

L’usuari pot:

* veure la pantalla
* utilitzar el ratolí
* executar aplicacions

---

# Funcionament

```
Client
(teclat i ratolí)
      │
      │ xarxa
      │
Servidor
(escriptori remot)
```

El servidor envia la pantalla al client.

---

# Protocols d'escriptori remot

| Protocol | Sistema         |
| -------- | --------------- |
| SSH      | Linux           |
| RDP      | Windows         |
| VNC      | multiplataforma |

---

# Seguretat

| Protocol | Tipus d’accés    | Xifrat / Encriptació | Autenticació                    | Nivell de seguretat |
| -------- | ---------------- | -------------------- | ------------------------------- | ------------------- |
| SSH      | Terminal remota  | AES, ChaCha20        | Password o clau pública/privada | Molt alt            |
| RDP      | Escriptori remot | TLS (AES)            | Credencials Windows             | Alt                 |
| VNC      | Escriptori remot | Limitat o opcional   | Password simple                 | Mitjà / baix        |

---

# Seguretat

> SSH utilitza criptografia asimètrica per establir la sessió i per a l’autenticació, 
però després utilitza xifrat simètric per protegir la comunicació

* SSH amb Password 
  * el client SSH es connecta al servidor
  * el servidor demana la contrasenya de l’usuari  
  * si és correcta, es permet l’accés

```
ssh admin@192.168.1.10
admin@192.168.1.10's password:
```
---

* SSH amb clau pública/privada
  * es generen totes dues claus
  * la clau privada es queda al client  
  * la clau pública es copia al servidor  
  * quan el client es connecta:
    * el servidor veu la clau pública autoritzada del client   
    * el servidor envia un repte criptogràfic (challenge)  
    * el client utilitza la seva clau privada per signar aquest repte  
    * el servidor utilitza la clau pública que té guardada per comprovar la signatura  

---

# RDP (Remote Desktop Protocol)

* Protocol d'escriptori remot desenvolupat per Microsoft.
* Permet controlar un sistema Windows remot.

---

# Port utilitzat

Port per defecte: 3389

El client introdueix l'adreça del servidor

Exemple: 192.168.1.50

---

# 4.5 VNC

**VNC (Virtual Network Computing)** permet controlar un escriptori remot  

Funciona entre diferents sistemes operatius  

Cada **sessió gràfica (display)** utilitza un port diferent i cada display és un escriptori remot diferent

---

Relació:

| Display | Port |
|-------|------|
| :0 | 5900 |
| :1 | 5901 |
| :2 | 5902 |


La relació és: `port = 5900 + display`

---

# Funcionament VNC

```
Administrador (client)
        │
        │ connexió TCP (protocol VNC / RFB)
        │
Servidor (VNC Server)
        │
        └─ comparteix l'escriptori gràfic
```
El VNC Server s’instal·la a l’ordinador que vols controlar, i el VNC Client a l’ordinador des d’on vols veure i controlar aquell escriptori

---

# Ports VNC

Port habitual:

```
5900
```

Sessions:

```
5900 → display 0
5901 → display 1
```

---

# Exemple de connexió

```
192.168.1.20:5900
```

El client mostra la pantalla del servidor

---

# 4.6 Protocols i ports

Els serveis remots utilitzen ports predefinits

| Protocol | Port |
| -------- | ---- |
| SSH      | 22   |
| RDP      | 3389 |
| VNC      | 5900 |

---

# 4.7 Servei SSH en Linux

El servei SSH s'executa com:

```
sshd
```
El programa que implementa el servidor SSH al sistema s'anomena sshd i és el procés que s'executa en segon pla

Comprovar estat:

```bash
systemctl status ssh
```

---

# Iniciar el servei

```bash
sudo systemctl start ssh
```

Ara el servidor acceptarà connexions remotes

---

# 4.8 Accés textual vs gràfic

Accés textual:

* molt ràpid
* poc consum de xarxa
* ideal per servidors

---

# Accés gràfic

Avantatges:

* més intuïtiu
* permet aplicacions gràfiques
* útil per suport tècnic

---

# 4.9 Gestió d'usuaris

Exemple d'usuaris Linux:

```bash
cat /etc/passwd
# javier:x:1000:1000:Javier de Palol:/home/javier:/bin/bash
```

Crear usuari:

```bash
sudo adduser usuari
# el sistema et demanarà que assignis una contrasenya a l’usuari durant el procés de creació.
```

---

# Restricció d'usuaris SSH

Fitxer de configuració:

```
/etc/ssh/sshd_config
```

Exemple:

```
AllowUsers admin javier
```

Només aquests usuaris poden accedir

---

# 4.10 Proves de connexió

Comprovar xarxa:

```bash
ping 192.168.1.20
```

Provar SSH:

```bash
ssh admin@192.168.1.20
```

---

# Comprovar servei  

```bash
systemctl status ssh
```

Resultat correcte:

```
active (running)
```

---

# 4.11 Resum Protocols d'administració remota

| Protocol | Tipus | Port |
|---|---|---|
SSH | Terminal remota | 22 |
RDP | Escriptori remot Windows | 3389 |
VNC | Escriptori remot multiplataforma | 5900+ |

---

# Tipus d'accés remot

Administració remota

- Accés textual
  - SSH

- Accés gràfic
  - RDP
  - VNC

---

# 4.12 Documentació

Cal documentar:

* servei instal·lat
* port
* usuaris autoritzats
* configuració

---

# Exemple de documentació

```
Servei: SSH
Servidor: 192.168.1.20
Port: 22
Usuaris: admin, javier
Config: /etc/ssh/sshd_config
```

---

# Idea final

L’administració remota és essencial perquè permet:

* gestionar servidors
* solucionar incidències
* administrar sistemes distribuïts

Sense accés físic al servidor.

