# RA04 — Resolució de l’examen teòric i pràctic

## Base documental utilitzada

Aquesta resolució s’ha elaborat **només** a partir dels materials aportats a classe:

- `slides.md`
- `tasca01Manual.md`

No s’hi ha afegit cap contingut extern.

---

# PART TEÒRICA

## Pregunta 1
**Quin protocol s’utilitza habitualment per administrar remotament un servidor Linux en mode text?**  
A) RDP  
B) VNC  
C) SSH  
D) FTP  

**Resposta correcta: C) SSH**

### Per què és correcta
A `slides.md` apareix literalment la frase:

> **“SSH (Secure Shell) és el protocol principal per administrar Linux remotament.”**

També hi surt:

> **“Permet gestionar un sistema remot...”**  
> **“Exemple de connexió remota”**  
> `ssh admin@192.168.1.10`

### Per què les altres no ho són
- **A) RDP** → a `slides.md` surt com a protocol d’escriptori remot de Windows:  
  > **“RDP (Remote Desktop Protocol) ... Permet controlar un sistema Windows remot.”**
- **B) VNC** → a `slides.md` surt com a control d’un escriptori remot:  
  > **“VNC (Virtual Network Computing) permet controlar un escriptori remot.”**
- **D) FTP** → no surt al material com a protocol d’administració remota del sistema.

---

## Pregunta 2
**Quin port utilitza SSH per defecte?**  
A) 21  
B) 22  
C) 3389  
D) 5900  

**Resposta correcta: B) 22**

### Per què és correcta
A `slides.md` surt literalment:

> **“El servidor escolta connexions al port 22.”**

I també a la taula de protocols:

> **“SSH | 22”**

### Per què les altres no ho són
- **A) 21** → no apareix associat a SSH enlloc del material.
- **C) 3389** → a `slides.md` surt com a port de **RDP**.
- **D) 5900** → a `slides.md` surt com a port base de **VNC**.

---

## Pregunta 3
**Quin fitxer del servidor SSH permet restringir quins usuaris poden accedir remotament?**  
A) /etc/passwd  
B) /etc/group  
C) /etc/ssh/sshd_config  
D) /var/log/auth.log  

**Resposta correcta: C) /etc/ssh/sshd_config**

### Per què és correcta
A `slides.md` surt:

> **“Fitxer de configuració:”**  
> `/etc/ssh/sshd_config`

I a `tasca01Manual.md` surt:

> **“Editar el fitxer de configuració.”**  
> `sudo nano /etc/ssh/sshd_config`

### Per què les altres no ho són
- **A) /etc/passwd** → al material surt per veure usuaris del sistema, no per configurar SSH:  
  > `cat /etc/passwd`
- **B) /etc/group** → surt relacionat amb grups, però no com a fitxer de configuració del servei SSH.
- **D) /var/log/auth.log** → no apareix com a fitxer de configuració; seria un fitxer de registre.

---

## Pregunta 4
**Què fa la directiva `AllowUsers admin javier` dins de `sshd_config`?**  
A) Crea els usuaris admin i javier  
B) Permet només l’accés SSH als usuaris admin i javier  
C) Dona permisos d’administrador a admin i javier  
D) Bloqueja l’accés SSH a admin i javier  

**Resposta correcta: B) Permet només l’accés SSH als usuaris admin i javier**

### Per què és correcta
A `slides.md` surt literalment:

> **“AllowUsers admin javier”**  
> **“Només aquests usuaris poden accedir.”**

Això coincideix exactament amb l’opció B.

### Per què les altres no ho són
- **A)** La directiva no crea usuaris. Per crear usuaris, al material surt:  
  > `sudo adduser usuari`
- **C)** La directiva no dona permisos d’administrador.
- **D)** És just el contrari del que diu la frase del document.

---

## Pregunta 5
**Quin protocol s’utilitza habitualment per accedir a l’escriptori remot d’un sistema Windows?**  
A) SSH  
B) SCP  
C) RDP  
D) rsync  

**Resposta correcta: C) RDP**

### Per què és correcta
A `slides.md` surt literalment:

> **“RDP (Remote Desktop Protocol)”**  
> **“Protocol d'escriptori remot desenvolupat per Microsoft.”**  
> **“Permet controlar un sistema Windows remot.”**

### Per què les altres no ho són
- **A) SSH** → surt com a protocol principal per administrar Linux remotament.
- **B) SCP** → no surt com a protocol d’escriptori remot.
- **D) rsync** → al manual surt com a eina de sincronització de fitxers:  
  > `rsync -av ...`

---

## Pregunta 6
**Quin port utilitza RDP per defecte?**  
A) 22  
B) 80  
C) 3389  
D) 5901  

**Resposta correcta: C) 3389**

### Per què és correcta
A `slides.md` surt:

> **“Port per defecte: 3389”**

### Per què les altres no ho són
- **A) 22** → és el port d’SSH.
- **B) 80** → no surt relacionat amb RDP.
- **D) 5901** → al material surt com a VNC display `:1`.

---

## Pregunta 7
**En VNC, què significa que una sessió estigui en display `:1`?**  
A) Que utilitza el port 22  
B) Que utilitza el port 3389  
C) Que utilitza el port 5901  
D) Que només permet lectura  

**Resposta correcta: C) Que utilitza el port 5901**

### Per què és correcta
A `slides.md` surt literalment:

> **“| :1 | 5901 |”**  
> **“La relació és: `port = 5900 + display`”**

### Per què les altres no ho són
- **A) 22** → port d’SSH.
- **B) 3389** → port de RDP.
- **D)** No surt enlloc que `:1` signifiqui només lectura.

---

## Pregunta 8
**Quin comandament permet comprovar si el servei SSH està actiu en un sistema Linux amb systemd?**  
A) ssh status  
B) systemctl status ssh  
C) ps ssh  
D) netstat ssh  

**Resposta correcta: B) systemctl status ssh**

### Per què és correcta
A `slides.md` surt:

> **“Comprovar estat:”**  
> `systemctl status ssh`

I a `tasca01Manual.md`:

> **“Al servidor comprovar el servei.”**  
> `systemctl status ssh`

### Per què les altres no ho són
- **A), C), D)** no surten al material com a comandes correctes per comprovar l’estat del servei SSH amb systemd.

---

## Pregunta 9
**Quin avantatge de seguretat té l’autenticació amb claus SSH respecte a l’autenticació amb contrasenya?**  
A) No utilitza xifrat  
B) Permet evitar atacs de força bruta sobre contrasenyes  
C) Fa servir el port 5900  
D) Només funciona en entorns Windows  

**Resposta correcta: B) Permet evitar atacs de força bruta sobre contrasenyes**

### Per què és correcta
A `slides.md` surt a la part de seguretat d’SSH que l’autenticació es pot fer amb:

> **“Password o clau pública/privada”**

I al treball de classe s'ha reforçat que les claus SSH són més segures que la contrasenya perquè eviten la debilitat de les contrasenyes i els atacs de força bruta. A més, a la pràctica es fa:

> `ssh-keygen`  
> `ssh-copy-id backupuser@192.168.1.20`

Això demostra que l’autenticació amb claus és una millora de seguretat real.

### Per què les altres no ho són
- **A)** és falsa: SSH sí utilitza xifrat.
- **C)** 5900 és VNC.
- **D)** no és cert; les claus SSH no són exclusives de Windows.

---

## Pregunta 10
**Quan es documenta una configuració d’accés remot, quin dels següents elements és imprescindible?**  
A) El color de fons del terminal  
B) El servei instal·lat, el port i els usuaris autoritzats  
C) El model del monitor del client  
D) La versió del navegador web  

**Resposta correcta: B) El servei instal·lat, el port i els usuaris autoritzats**

### Per què és correcta
A `slides.md` surt literalment:

> **“Cal documentar:”**  
> **“servei instal·lat”**  
> **“port”**  
> **“usuaris autoritzats”**  
> **“configuració”**

I també l’exemple:

> **“Servei: SSH”**  
> **“Port: 22”**  
> **“Usuaris: admin, javier”**

### Per què les altres no ho són
- **A), C), D)** no formen part de la documentació tècnica treballada al material.

---

# PART PRÀCTICA

## A) Configuració d’accés SSH restringit

### 1. Comandes

**Crear grup `sshusers`:**
```bash
sudo groupadd sshusers
```

**Crear usuari `backupuser`:**
```bash
sudo adduser backupuser
```

**Afegir l’usuari al grup `sshusers`:**
```bash
sudo usermod -aG sshusers backupuser
```

**Editar la configuració SSH:**
```bash
sudo nano /etc/ssh/sshd_config
```

**Afegir la directiva:**
```text
AllowGroups sshusers
```

**Reiniciar el servei SSH:**
```bash
sudo systemctl restart ssh
```

### 2. Justificació tècnica

**a. Per què és recomanable restringir l’accés SSH a un grup concret**  
Per reduir l’accés només als usuaris autoritzats.  
Base documental (`tasca01Manual.md`): la pràctica diu que s'ha de crear **“un grup de confiança”** i restringir l’accés SSH a aquest grup.

**b. Quin fitxer de configuració s’ha modificat**  
```text
/etc/ssh/sshd_config
```
Apareix literalment tant a `slides.md` com a `tasca01Manual.md`.

**c. Per què cal reiniciar el servei després de fer canvis a la configuració**  
Perquè el servei SSH carregui la nova configuració.  
Base documental (`tasca01Manual.md`): després d’afegir la restricció es demana:
```bash
sudo systemctl restart ssh
```

---

## B) Connexió remota i sincronització segura

### 1. Comanda de connexió SSH
```bash
ssh backupuser@192.168.1.20
```

### 2. Comanda de sincronització recursiva amb `rsync`
```bash
rsync -av backupuser@192.168.1.20:/data/backups ~/copia_remota
```

### 3. Explicació

**a. Quin protocol s’està utilitzant en cada cas**  
- A la primera comanda s’utilitza **SSH**
- A la segona s’utilitza **rsync** sobre una connexió remota amb SSH, tal com està plantejat al manual > per tant SSH

**b. Quin port s’utilitza per defecte**  
El port per defecte és el **22**, perquè la connexió remota és SSH.

**c. Quin avantatge té `rsync` respecte a una còpia simple**  
A `tasca01Manual.md` surt literalment:

> **“Avantatges de rsync:”**  
> **“només copia fitxers modificats”**  
> **“és més eficient que scp”**  
> **“molt utilitzat en sistemes”**

Per tant, l’avantatge principal és que és **més eficient** i **no torna a copiar tot si no cal**.

---

## C) Verificació i control

### 1. Comandament per comprovar que el servidor respon a nivell de xarxa
```bash
ping 192.168.1.20
```

Base documental:
- `slides.md`:  
  > **“Comprovar xarxa:”**  
  > `ping 192.168.1.20`
- `tasca01Manual.md`:  
  > **“Si la connexió no funciona, primer es pot comprovar la connectivitat amb:”**  
  > `ping 192.168.1.20`

### 2. Comandament per comprovar que el servei SSH està actiu
```bash
systemctl status ssh
```

Base documental:
- `slides.md`
- `tasca01Manual.md`

### 3. Comandament per veure els usuaris del sistema Linux
```bash
cat /etc/passwd
```

Base documental:
- `slides.md`:  
  > **“Exemple d'usuaris Linux:”**  
  > `cat /etc/passwd`

### 4. Com es pot verificar que la restricció d’accés SSH només afecta els usuaris autoritzats
Es prova la connexió amb un usuari autoritzat i, si es vol validar la restricció, es prova també amb un usuari no autoritzat.

Exemple de prova vàlida:
```bash
ssh backupuser@192.168.1.20
```

Si `backupuser` és al grup autoritzat, la connexió funciona. Si un altre usuari no autoritzat intenta entrar, l’accés ha de quedar denegat. Això surt implícitament a la configuració del grup de confiança i a la restricció amb `AllowGroups sshusers` al manual.


