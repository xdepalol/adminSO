# Capítol 2 — Esquema del Servei de Directori

---

# **2. Introducció a l'esquema d'un servei de directori**

En qualsevol servei de directori —ja sigui **Active Directory (AD)** o **OpenLDAP**— l'**esquema** és el component que defineix com s'emmagatzema la informació i quins objectes poden existir. Actua com el **model de dades formal** del directori, igual que l'esquema d'una base de dades relacional.

L'esquema proporciona:

* el conjunt de **classes d'objecte** existents
* els **atributs obligatoris i opcionals** de cada objecte
* les **sintaxis** possibles dels valors
* les **regles de validació**
* les relacions i estructura dins l'arbre del directori (**DIT**)

El directori no és una col·lecció arbitrària d'entrades: és un sistema **estrictament regulat** perquè totes les dades segueixin una forma coherent, interoperable i validable.

---

# **2.1. Atributs i sintaxis de l'esquema**

Cada objecte del directori està format per **atributs**. Un atribut és una peça d'informació definida amb:

* un **nom** (p. ex. `cn`, `sn`, `uid`)
* una **sintaxi** (tipus de dada que pot contenir)
* si és **SINGLE-VALUE** o **MULTI-VALUE**
* opcionalment, un comparador o regles específiques

## **Sintaxis més importants

### **Exemple real de fragment d'esquema d'Active Directory (AD)**

A continuació es mostra un **fragment REAL** de l'esquema d'Active Directory tal com existeix dins: `CN=Schema,CN=Configuration,DC=empresa,DC=local`

Aquest fragment defineix la classe d'objecte **user** dins d'AD:

```
classSchema
    cn: user
    GovernedID: 1.2.840.113556.1.5.9
    objectClassCategory: 1
    subClassOf: organizationalPerson
    mayContain:
        * displayName
        * telephoneNumber
        * physicalDeliveryOfficeName
        * wWWHomePage
        * userPrincipalName
        * sAMAccountName
        * unicodePwd
    mustContain:
        * objectClass
    systemMustContain:
        * objectSid
        * sAMAccountType
    systemMayContain:
        * lastLogon
        * userAccountControl
        * logonHours
        * pwdLastSet
```

**Explicació resumida:**

* **objectClassCategory 1** → és una classe estructural.
* **subClassOf organizationalPerson** → hereta atributs com cn, sn, title…
* **mayContain** → atributs opcionals d'un usuari AD.
* **mustContain** → atributs obligatoris definits per l'esquema.
* **systemMustContain / systemMayContain** → atributs interns que AD afegeix i controla automàticament.

Això és EXACTAMENT el que AD utilitza per validar com ha de ser un objecte *usuari*. A diferència d'OpenLDAP, AD no utilitza fitxers `.schema`, sinó que el seu esquema està emmagatzemat **dins del mateix directori** com a objectes

---

# **2.2. Definició formal d'atributs: attributetype**

Quan un atribut està definit a l'esquema, s'utilitza una estructura com aquesta:
```
attributetype ( 2.5.4.3 NAME 'cn' DESC 'Common Name' SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 SINGLE-VALUE )

```
Això significa:
* **2.5.4.3** → OID oficial del camp `cn`
* **NAME 'cn'** → nom de l'atribut
* **SYNTAX DirectoryString** → accepta text Unicode
**No la crees tu; ve predefinida per l'estàndard LDAP.** És una sintaxi que permet:
   * qualsevol caràcter Unicode
   * accents, caràcters especials, símbols
   * llargades variades
* **SINGLE-VALUE** → només pot tenir un valor

S’utilitza per atributs com `cn`, `sn`, `description`, `displayName`.
Permet emmagatzemar **text lliure, internacional i flexible**, i LDAP valida automàticament que el contingut sigui UTF‑8.

Aquest tipus de definicions **ja venen amb LDAP o AD**, no les crees a classe.  
Modificades incorrectament **poden impedir l'inici del servei** (en OpenLDAP el procés `slapd`).

---

# **2.3. Classes d'objecte (objectClass)**

Les classes d'objecte defineixen **quin tipus d'entrada és** i **quins atributs pot contenir**. N'hi ha de tres tipus:

## **1) Estructurals (Structural)**
Defineixen el "tipus real" d'un objecte. Són obligatòries i no poden canviar un cop creat l'objecte.

### Exemples:
* **inetOrgPerson** — representa una persona real
* **posixAccount** — afegeix atributs per login Unix
* **organizationalUnit** — contenidor del DIT
* **device** — objectes que representen equips o dispositius


## **2) Auxiliars (Auxiliary)**
Afegeixen atributs addicionals a un objecte que ja té una classe estructural.

### Exemples:
* **shadowAccount** — atributs de contrasenya Unix (`shadowLastChange`, etc.)
* **sambaSamAccount** — atributs per compatibilitat amb Windows (SID de Windows, contrasenya NTLM…)

### Sobre “SID de Windows” i “contrasenya NT”
* **SID** = “Security Identifier”, identificador únic del sistema Windows. Funciona com un “DNI digital”.  
* **Contrasenya NT (NTLM Hash)** = hash MD4 de la contrasenya Unicode. No és segur, però encara es fa servir per compatibilitat.


## **3) Abstractes (Abstract)**
No es poden instanciar directament. Serveixen com a base perquè altres classes hi heretin.

### Exemple:
* **top** — totes les altres classes deriven d'aquesta

`top` és equivalent a `Object` en Java:  

> No es crea mai un objecte *top*, però tots els objectes LDAP contenen `objectClass: top` perquè hereten d'ella.

---

# **2.4. Exemple real d'una definició d'objectClass**

```
objectclass ( 2.16.840.1.113730.3.2.2 NAME 'inetOrgPerson' SUP organizationalPerson STRUCTURAL MUST ( cn $ sn ) MAY  ( userPassword $ mail $ telephoneNumber $ uid $ givenName $ displayName ) )

```
Explicació:
* Hereta de `organizationalPerson`
* És una classe **STRUCTURAL**
* Atributs obligatoris → `cn`, `sn`
* Atributs opcionals → `mail`, `uid`, `telephoneNumber`, etc.
* El símbol `$` separa atributs múltiples dins la mateixa categoria

---

# **2.5. Combinació de classes: un objecte amb múltiples rols**
LDAP permet que un objecte tingui **diverses objectClass**, la qual cosa permet donar-li múltiples funcions.

Exemple:
```
objectClass: inetOrgPerson objectClass: posixAccount objectClass: shadowAccount

```
Això vol dir que un mateix usuari pot:
* autenticar-se al directori (inetOrgPerson)
* iniciar sessió a servidors Linux (posixAccount → UID/GID, homeDirectory, loginShell)
* tenir polítiques de contrasenya Unix (shadowAccount)

---

# **2.6. El DIT (Directory Information Tree)**

És la representació jeràrquica del directori aproximadament com un sistema de carpetes.

Exemple:
```
dc=empresa,dc=local 
├── ou=Usuaris 
│     ├── uid=aribas 
│     └── uid=mpuig 
├── ou=Grups 
│     ├── cn=professors 
│     └── cn=administradors 
└── ou=Equips 
      ├── cn=PC01 
      └── cn=PC02
```
Un bon disseny del DIT facilita:
* permisos
* delegació d'administració
* aplicació de polítiques
* automatització amb scripts

Un DIT mal dissenyat (massa profund i caòtic) és difícil de mantenir i impossible de delegar correctament.

---

# **2.7. Nomenclatura del DN: RDN, OU i DC**

El **DN (Distinguished Name)** és l'adreça completa d'un objecte.  
Els seus components:

* **RDN (Relative Distinguished Name)** → identificador immediat (`uid=mcarbo`)
* **Contenidor (OU)** → unitat organitzativa (`ou=Usuaris`)
* **Arrel del domini** → `dc=empresa,dc=local`

Un DN és la suma jeràrquica de tots ells.

```
uid=mpuig,ou=Professors,ou=Departament,dc=ins-torreroja,dc=cat
```

---

# **2.8. Grups POSIX**

Un **posixGroup** és un grup compatible amb Unix/Linux.  

Els seus atributs essencials:
* `cn` — nom del grup
* `gidNumber` — ID numèric del grup
* `memberUid` — membres del grup

Exemple:
```
objectClass: posixGroup cn: professors gidNumber: 5020 memberUid: aribas memberUid: mpuig

```

---

# **2.9. LDIF (LDAP Data Interchange Format)**

LDIF és un format estàndard (RFC 2849) utilitzat per **importar, modificar o exportar** objectes en qualsevol directori LDAP

No és documentació: **LDAP interpreta LDIF com ordres administratives**

---

## **Exemple complet d’un fitxer LDIF (alta d’un usuari)**

```
dn: uid=jroca,ou=Usuaris,dc=empresa,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
cn: Jordi Roca
sn: Roca
uid: jroca
mail: jroca@empresa.local
uidNumber: 1201
gidNumber: 1201
homeDirectory: /home/jroca
loginShell: /bin/bash
userPassword: {SSHA}kjsdf9832jkfhsdf9832f==
```

### **Interpretació del fitxer**

* **dn:** ubicació exacta dins del DIT on es crearà l’objecte.
* **objectClass:** rol(s) de l’objecte (usuari LDAP + usuari Unix + atributs de contrasenya).
* **cn, sn, uid, mail:** informació personal.
* **uidNumber / gidNumber:** identificadors Unix.
* **homeDirectory / loginShell:** configuració per login Unix.
* **userPassword:** contrasenya en format hash SSHA.

---

# **Com s’executa un LDIF segons el tipus de directori**

## **1. OpenLDAP (Linux/Unix)**

En sistemes basats en Linux, LDIF s’executa amb les eines pròpies d’OpenLDAP, com:

```
ldapadd -x -D "cn=admin,dc=empresa,dc=local" -W -f alta-usuari.ldif
```

* **ldapadd** → importa noves entrades
* **ldapmodify** → modifica entrades existents
* **ldapdelete** → elimina entrades

Aquest és el cas més habitual en entorns formatius o de laboratori.

---

## **🟦 2. Active Directory (Windows)**

AD també utilitza LDIF, però amb l’eina pròpia **ldifde.exe**, que s’executa des de:

* el servidor Windows del Directori Actiu
* CMD
* PowerShell

Exemple:

```
ldifde -i -f alta-usuari.ldif
```

* **ldifde -i** → mode importació
* **-f** → fitxer LDIF

---

# **On s’executa realment un LDIF?**

| Directori            | SO                 | Eina d’execució de LDIF               |
| -------------------- | ------------------ | ------------------------------------- |
| **OpenLDAP**         | Linux/Unix         | `ldapadd`, `ldapmodify`, `ldapdelete` |
| **Active Directory** | Windows            | `ldifde.exe`                          |
| **Altres LDAP**      | Solaris, AIX, BSD… | Eines pròpies + compatibilitat LDIF   |

---

# **Què passa internament quan s’executa un LDIF**

LDAP fa:

1. Llegeix el fitxer línia per línia.
2. Valida la sintaxi del DN.
3. Comprova que les objectClass existeixen a l’esquema.
4. Valida la sintaxi dels atributs.
5. Crea o modifica l’entrada.
6. Actualitza la base de dades del directori.

Si és correcte retorna el següent missatge:

```
adding new entry "uid=jroca,ou=Usuaris,dc=empresa,dc=local"
```

Si hi ha errors:

* `ObjectClassViolation`
* `Invalid DN Syntax`
* `Entry Already Exists`

---

# **Representació visual del resultat dins del DIT**

**Abans:**

```
dc=empresa,dc=local
 └── ou=Usuaris
```

**Després d’executar el LDIF:**

Estructura de contingut:
```
dc=empresa,dc=local
 └── ou=Usuaris
       └── uid=jroca
```

A Unix-Linux-Windows:
```
ldapsearch -x -b "uid=jroca,ou=Usuaris,dc=empresa,dc=local"

dn: uid=jroca,ou=Usuaris,dc=empresa,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
cn: Jordi Roca
sn: Roca
uid: jroca
mail: jroca@empresa.local
uidNumber: 1201
gidNumber: 1201
homeDirectory: /home/jroca
loginShell: /bin/bash
userPassword:: e1NTSEF9a2pzZGY5ODMyamtmaHNkZjk4MzJmPT0=
shadowLastChange: 20000
shadowMax: 99999
shadowWarning: 7
```


