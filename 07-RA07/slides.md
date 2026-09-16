---
marp: true
paginate: true
theme: default

header: "RA07 - Llenguatges de guions en sistemes operatius"
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

# RA07  
## Llenguatges de guions en sistemes operatius

Administració de Sistemes Operatius  
ASIX02

---

# Definició

Un llenguatge de guions és un llenguatge pensat per escriure ordres en forma de fitxer perquè el sistema les executi una darrere l’altra

---

# Context dels llenguatges de guions

La idea central és convertir tasques habituals en:

- seqüències repetibles
- ordres controlables
- execucions automàtiques
- administració més eficient

---

# Dos entorns generals

- **GNU/Linux** amb **Bash** o scripts de **shell**
- **Windows** amb **PowerShell**

En aquesta RA, el desenvolupament pràctic es pot centrar sobretot en **GNU/Linux**

---

# Què aporten els guions a l'administració

Permeten:

- gestionar **usuaris**
- controlar **processos**
- administrar **serveis**
- reutilitzar seqüències d’ordres
- reduir tasques manuals repetitives

---

# 7.1 Context dels llenguatges de guions

Els guions queden lligats a aquestes idees bàsiques:

- administració des de línia d’ordres
- combinació de comandes per a tasques del sistema
- reutilització d’ordres dins d’un mateix script
- execució controlada o programada quan convingui

---

# 7.2 Bash com a entorn de guions

En GNU/Linux, el llenguatge de guions més habitual és **Bash**

La primera línia habitual d’un script és:

```bash
#!/bin/bash
```

Això indica quin intèrpret ha d’executar el fitxer

---

# Exemple mínim de script Bash

```bash
#!/bin/bash
echo "Iniciant comprovació del sistema"
whoami
date
```

Aquí es veu que diverses ordres es poden reunir dins d’un sol fitxer executable

---

# 7.3 Estructures bàsiques del llenguatge

- ordres amb **nom + paràmetres**
```bash
ls -l /home
# ls és la comanda
# -l és un paràmetre
# /home és la ruta sobre la qual actua
```

- comandes combinades
```bash
ps aux | grep ssh
# ps aux mostra processos
# grep ssh filtra els que contenen ssh
```

---

# 7.3 Estructures bàsiques del llenguatge

- filtres i selecció de resultats
```bash
cat /etc/passwd | grep javier
# cat /etc/passwd mostra el contingut del fitxer
# grep javier selecciona només les línies on surt javier
```

- variables
```bash
USUARI="javier"
echo $USUARI
# USUARI guarda un valor
# echo $USUARI mostra el contingut de la variable
```

---

# 7.3 Estructures bàsiques del llenguatge

- condicionals `if`
```bash
if id "$USUARI" >/dev/null 2>&1; then
# if inicia una estructura condicional
# id "$USUARI" intenta comprovar si l'usuari existeix al sistema
# "$USUARI" agafa el valor de la variable USUARI
# >/dev/null fa que la sortida normal no es mostri per pantalla
# 2>&1 fa que els errors tampoc es mostrin per pantalla
# then indica que aquí comença el bloc que s'executarà si la comanda ha anat bé

    echo "L'usuari existeix" # mostra aquest missatge si l'usuari existeix

else # else indica el bloc alternatiu, és a dir, què s'ha de fer si la condició no es compleix

    echo "L'usuari no existeix" # mostra aquest missatge si l'usuari no existeix

fi
# fi tanca l'estructura if
```

---

# 7.3 Estructures bàsiques del llenguatge

- bucles `for` o `while`
```bash
COMPTADOR=1
# crea la variable COMPTADOR i li dona el valor inicial 1

while [ $COMPTADOR -le 3 ]; do
# while vol dir "mentre"
# [ $COMPTADOR -le 3 ] comprova si COMPTADOR és menor o igual que 3
# do indica que aquí comença el bloc que es repetirà

    echo $COMPTADOR
    # mostra per pantalla el valor actual de la variable COMPTADOR

    COMPTADOR=$((COMPTADOR+1))
    # suma 1 al valor actual de COMPTADOR. El resultat es guarda de nou a la mateixa variable

done
# done tanca el bucle while. Quan arriba aquí, Bash torna a comprovar la condició del while
```

---

# 7.4 Administració de comptes d'usuari amb Bash

Ordres útils:

```bash
id alumnera07
# comprova si l'usuari alumnera07 existeix
# si existeix, mostra informació com el UID, el GID i els grups als quals pertany
# si no existeix, retorna un error
getent passwd alumnera07
# consulta la base de dades d'usuaris del sistema
# si l'usuari alumnera07 existeix, mostra la seva línia del registre passwd
# aquesta línia sol incloure nom d'usuari, UID, GID, directori personal i shell
cat /etc/passwd
# mostra tot el contingut del fitxer /etc/passwd
# aquest fitxer conté el llistat d'usuaris del sistema
# és útil per veure tots els comptes, no només un en concret
```

Aquestes ordres permeten comprovar i consultar comptes del sistema

---

# Exemple de comprovació d'usuari

```bash
USUARI="alumnera07"
# crea la variable USUARI i li dona el valor "alumnera07"

if id "$USUARI" >/dev/null 2>&1; then
# if inicia una estructura condicional
# id "$USUARI" intenta comprovar si l'usuari existeix al sistema
# "$USUARI" fa servir el valor guardat a la variable
# >/dev/null fa que la sortida normal no es mostri per pantalla
# 2>&1 fa que els errors tampoc es mostrin per pantalla
# then indica que aquí comença el bloc que s'executarà si la comanda ha anat bé

    echo "L'usuari $USUARI existeix" # mostra un missatge indicant que l'usuari existeix

    getent passwd "$USUARI" # mostra la línia de l'usuari dins de la base de dades d'usuaris del sistema. 
    # normalment hi apareix el nom, UID, GID, directori personal i shell

else # else indica què s'ha de fer si l'usuari no existeix

    echo "L'usuari $USUARI no existeix" # mostra un missatge indicant que l'usuari no existeix

fi
# fi tanca l'estructura if
```

---

# 7.5 Administració de processos amb Bash

Ordres habituals:

```bash
ps aux
# mostra tots els processos del sistema
# a indica que es mostren processos de tots els usuaris
# u mostra la informació en format orientat a usuari
# x inclou també processos que no depenen directament d'un terminal

pgrep bash
# busca processos que tinguin el nom "bash"
# si en troba, mostra el PID de cada procés trobat
# si no en troba cap, no mostra res i retorna error

ps -ef | grep ssh
# ps -ef mostra tots els processos del sistema en format complet
# | envia la sortida de ps a la comanda següent
# grep ssh filtra només les línies que contenen el text "ssh"
# és útil per localitzar processos relacionats amb ssh
```

Això permet consultar, filtrar i comprovar processos des del terminal

---

# Exemple de comprovació de procés

```bash
PROCES="bash"
# crea la variable PROCES i li dona el valor "bash"

if pgrep "$PROCES" >/dev/null 2>&1; then
# if inicia una estructura condicional
# pgrep "$PROCES" busca processos que tinguin aquest nom
# "$PROCES" fa servir el valor guardat a la variable
# >/dev/null fa que la sortida normal no es mostri per pantalla
# 2>&1 fa que els errors tampoc es mostrin per pantalla
# then indica que aquí comença el bloc que s'executarà si la comanda ha anat bé

    echo "El procés $PROCES està en execució"
    # mostra aquest missatge si s'ha trobat algun procés amb aquest nom

else
# else indica què s'ha de fer si no s'ha trobat cap procés amb aquest nom

    echo "El procés $PROCES no està en execució"
    # mostra aquest missatge si el procés no existeix o no està actiu

fi
# fi tanca l'estructura if
```

---

# 7.6 Administració de serveis amb Bash

Ordres habituals:

```bash
systemctl status ssh
systemctl is-active ssh
systemctl start ssh
systemctl stop ssh
systemctl restart ssh
```

---

# Exemple de comprovació de servei

```bash
SERVEI="ssh"
# crea la variable SERVEI i li dona el valor "ssh"

if systemctl is-active "$SERVEI" >/dev/null 2>&1; then
# if inicia una estructura condicional
# systemctl is-active "$SERVEI" comprova si el servei indicat està actiu
# "$SERVEI" fa servir el valor guardat a la variable
# >/dev/null fa que la sortida normal no es mostri per pantalla
# 2>&1 fa que els errors tampoc es mostrin per pantalla
# then indica que aquí comença el bloc que s'executarà si la comanda ha anat bé

    echo "El servei $SERVEI està actiu"
    # mostra aquest missatge si el servei està actiu

else
# else indica què s'ha de fer si el servei no està actiu o no existeix

    echo "El servei $SERVEI no està actiu"
    # mostra aquest missatge si el servei no està actiu

fi
# fi tanca l'estructura if
```

---

# 7.7 Consulta d'ordres i ajuda a GNU/Linux

Eines útils abans d’escriure un guió:

```bash
man ps
# mostra el manual de la comanda ps
# serveix per consultar què fa la comanda, quins paràmetres admet i com s'utilitza

man systemctl
# mostra el manual de la comanda systemctl
# és útil per veure com consultar, iniciar, aturar o reiniciar serveis

help
# mostra ajuda sobre ordres internes del shell
# no serveix per a totes les comandes del sistema, sinó sobretot per a les pròpies de Bash

which bash
# indica on es troba l'executable de bash dins del sistema
# per exemple, pot mostrar una ruta com /usr/bin/bash o /bin/bash

type echo
# indica quin tipus d'ordre és "echo"
# permet saber si és una ordre interna del shell, un executable extern, un àlies o una funció
```

Això permet consultar què fa cada ordre i reutilitzar-la dins del script

---

# 7.8 Documentació dels guions

Exemple d’inici documentat:

```bash
#!/bin/bash
# Pràctica RA07
# Autor: Nom Cognoms
# Objectiu: Comprovar usuaris, processos i serveis
```

Els comentaris amb `#` ajuden a entendre el guió després

---

# Comprovació i depuració bàsica

Accions bàsiques:

- revisar sintaxi i ordres
- comprovar valors de variables
- verificar condicions `if`
- detectar errors de permisos
- provar l’execució real del fitxer

---

# Donar permisos i executar

```bash
chmod +x ra07_admin_linux.sh
./ra07_admin_linux.sh
```

Aquest és el pas necessari perquè el guió es pugui executar com a fitxer

---

# 7.9 Execució programada de guions

Com a aplicació complementària, un guió també es pot deixar preparat per executar-se més endavant

Eines habituals:

- **cron**
- **crontab**
- **anacron**
- **at**

---

# Cron i crontab

```bash
service crond status
apt-get install cron
crontab -l
crontab -e
```

Exemple de programació:

```bash
30 0 * * * root find /tmp -type f -empty -delete
```

---

# Anacron i at

```bash
apt-get install anacron
apt-get install at
at 12am tomorrow < script.sh
```

Aquestes eines permeten llançar scripts en moments concrets

---

# 7.11 Orientació pràctica de la RA07

Bloc principal de la unitat:

- **GNU/Linux** com a entorn de pràctica
- **Bash** com a llenguatge de guions principal
- scripts sobre **usuaris**, **processos** i **serveis**
- PowerShell com a context complementari

---

# Idea final

Si les pràctiques i l’examen s’han de centrar en Linux, la RA07 es pot desplegar de manera molt coherent al voltant de:

- **Bash**
- ordres del sistema
- scripts d’administració
- comprovació i documentació del guió
