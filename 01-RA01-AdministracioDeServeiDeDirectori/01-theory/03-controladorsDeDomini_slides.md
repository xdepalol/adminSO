---
marp: true
paginate: true
theme: default

header: "RA01 – Servei de Directori · Capítol 3 – Controladors de Domini i Arquitectures AD/LDAP"
footer: "Mòdul 0374 – Administració de Sistemes Operatius"

style: |
    header, footer {
        display: block;
        width: 100vw;
        font-size: .45rem;
        color: #bbbbbcff;
        z-index: 10;
    }

    header {
        text-align: right !important;   
        padding-right: 50px;           
    }
---

<!-- Slide 1 -->
# RA01: Capítol 3
## Controladors de Domini i Arquitectures AD/LDAP

**ASIX**       - Administració de Sistemes i Xarxes 
**Mòdul 0374** - Administració de Sistemes Operatius
**Professor**  -  Javire de Palol

---

<!-- Slide 2 -->
# 🔸2.0. Objectius del capítol

- Entendre què és un **Controlador de Domini (DC)**  
- Comprendre l’arquitectura d'**Active Directory**  
- Distingir domini, arbre i bosc  
- Explicar el concepte de **Global Catalog**  
- Entendre la **replicació AD**  
- Conèixer els principis bàsics de **LDAP / OpenLDAP**  
- Comparar AD i LDAP a nivell arquitectònic

---

<!-- Slide 3 -->
# 🔸3.0. Què és un Controlador de Domini?

Un **DC (Domain Controller)** és el servidor que:

- Emmagatzema la base del directori (NTDS.dit)  
- Valida usuaris i equips  
- Emet tickets Kerberos  
- Replica la informació  
- Aplica polítiques de seguretat

És el **nucli del servei de directori** a Windows

---

<!-- Slide 4 -->
# 🔸4.0. Funcions d’un DC

- Autenticació (Kerberos)  
- Autorització  
- Replicació entre DCs  
- Gestió del domini  
- Global Catalog  
- Validació de grups i permisos

---

<!-- Slide 4.1 -->

# 🔸4.1. Què és la replicació entre controladors de domini?

* **Cada DC té la seva pròpia còpia de la base NTDS.dit**
No existeix un servidor central del directori.  
Tots els DC mantenen una còpia completa i operativa.

* **Alta disponibilitat i consistència**
Si un DC falla, un altre continua autenticant.  
Els canvis es propaguen perquè tots mantinguin la mateixa informació.

* **Intra-site i inter-site**
    * **Intra-site:** ràpida i freqüent, dins la mateixa seu.  
    * **Inter-site:** optimitzada i comprimida per connexions entre seus diferents.

---

<!-- Slide 4.1 -->

# 🔸4.1. Què és la replicació entre controladors de domini?

* **Replicació = sincronització de canvis**
    Els DCs s’envien automàticament:
    * Altes i baixes d’usuaris
    * Canvis de contrasenya
    * Modificacions d’OUs
    * Canvis en grups o polítiques
    * Esborrats d’objectes

---

<!-- Slide 4.2 -->

# 🔸4.2 Per què es té més d’un controlador de domini?

* **Alta disponibilitat**
Si un DC falla, un altre continua autenticant usuaris i equips.

* **Redundància**
Cada DC té una còpia pròpia de la base NTDS.dit.  
La pèrdua d’un DC no afecta el servei.

* **Reducció de latència**
Es col·loquen DCs a cada seu per evitar dependència de la WAN i millorar la velocitat d’inici de sessió.

---

<!-- Slide 4.2 -->

# 🔸4.2 Per què es té més d’un controlador de domini?

* **Balanceig de càrrega**
Molts usuaris autenticant-se alhora es reparteixen entre diversos DCs.

* **Escalabilitat i tolerància a fallades**
Els canvis es repliquen entre DCs.  
El sistema és més robust, segur i fàcil de mantenir.

---

<!-- Slide 5 -->

# 🔸5.0 Arquitectura AD: Domain - Tree - Forest

*Domini - Arbre - Bosc*

- **Domain**: unitat bàsica d’administració  
- **Tree** : conjunt de dominis amb espai de noms comú  
- **Forest**  : conjunt d’arbres que comparteixen esquema i GC

---

<!-- Slide 6 -->

# 🔸5.0 Exemple de tree i forest

```
empresa.local
├── barcelona.empresa.local
└── madrid.empresa.local

institut.cat
├── premia.institut.cat
└── viladecans.institut.cat
```
Dos arbres → **un bosc** si comparteixen:  
✔ **Esquema**  
✔ **Global Catalog**  
✔ **Confiances internes**: Tots els dominis del bosc confien automàticament entre ells. Es reconeixen mútuament com a legítims i poden autenticar usuaris els uns dels altres sense intervenció de l'administrador   

---

<!-- Slide 7 -->

# 🔸6.0 Global Catalog (GC)

Servei especialitzat que manté:

- Una còpia **completa** del domini local  
- Una còpia **parcial** dels altres dominis  
- Índexs per a cerques ràpides  
- Validació de grups universals: conté informació parcial de tots els dominis del bosc i pot construir el token d'autenticació complet 
- Suport a autenticacions interdominis: conté informació d'identitat i grups de tots els dominis del forest

És **l'índex global del bosc**

---

<!-- Slide 8 -->

# 🔸7.0 Autenticació Kerberos

Quan un usuari inicia sessió:

1. S'envia credencials al **KDC** (Key Distribution Center)  
2. Es genera el **Ticket-Granting Ticket (TGT)**  
3. S'entrega tickets per accedir a serveis

Comprovació:

```bash
klist
```

```
Client: joan@empresa.local
Server: krbtgt/empresa.local
Start Time: 10:05
End Time:   20:05
```
---

<!-- Slide 9 -->

# 🔸7.0 Autenticació Kerberos

   1. L’usuari introdueix la contrasenya al seu equip
      La contrasenya no surt mai del client.
   2. El client genera un hash de la contrasenya
      Aquest hash és la clau secreta compartida entre l’usuari i el KDC.
   3. El client envia una petició al KDC
      Conté:
      - nom d’usuari
      - petició xifrada amb el hash de la contrasenya
   4. El KDC valida la petició
      Utilitza el hash emmagatzemat a AD
      Si pot desxifrar-la, l'usuari és autèntic

---

<!-- Slide 10 -->

# 🔸7.1 Kerberos vs RSA (Simètric vs Asimètric)

## Kerberos: criptografia simètrica
- No utilitza claus pública/privada.
- Xifra i desxifra amb **la mateixa clau**: el hash de la contrasenya
- El KDC utilitza el mateix hash per validar la petició
- El ticket (TGT) s'emet i es gestionat amb claus internes del KDC
- No requereix certificats ni parell de claus

---

<!-- Slide 11 -->

# 🔸7.1 Kerberos vs RSA (Simètric vs Asimètric)

## RSA: criptografia asimètrica
- Utilitza **dues claus diferents**: pública i privada  
- La clau pública xifra; la privada desxifra  
- Necessita certificats i infraestructura de clau pública (PKI)  
- Es fa servir en HTTPS, SSH, signatures digitals i PGP  

## Diferència essencial
- **Kerberos autentica** mitjançant claus simètriques i un servidor central (KDC)  
- **RSA xifra/signa** mitjançant un parell de claus diferent per a cada part  

---

<!-- Slide 12 -->

# 🔸8.0 Replicació AD

### **Intra-site**

* ràpida
* no comprimida
* intervals curts (segons/minuts)

### **Inter-site**

* optimitzada
* comprimida
* intervals llargs (minuts/hores)

---

<!-- Slide 13 -->

# 🔸9.0 Eines de diagnosi en AD

* `dcdiag`                → estat del DC
* `repadmin /replsummary` → replicació
* `netdom query fsmo`     → rols FSMO
* `dsa.msc`               → gestió d’usuaris/equips
* `gpmc.msc`              → polítiques (GPO)

---

<!-- Slide 14 -->

# 🔸10.0 Introducció a LDAP

LDAP = **Lightweight Directory Access Protocol**

### LDAP s’utilitza tant en Windows com en Linux

### En Windows (Active Directory)
- AD utiliza LDAP com a protocol de consulta del directori
- El DC exposa LDAP per defecte en el port 389 (i 636 amb TLS)
- Permet consultar usuaris, grups, OUs i atributs d’AD
- Suporta extensions pròpies de Microsoft (esquema AD)

---

<!-- Slide 15 -->

# 🔸10.0 Introducció a LDAP

### En Linux (OpenLDAP)
- S'executa en el servei **slapd**
- Organitza la informació en un **DIT**
- Utilitza fitxers **LDIF** i esquema flexible

---

<!-- Slide 16 -->

# 🔸10.0 Introducció a LDAP

### Slapd
És el procés que fa de servidor LDAP
Rep peticions, guarda objectes i aplica consultes

---

<!-- Slide 17 -->

# 🔸11.0 DN, RDN i DIT

### **DN (Distinguished Name)**

Identificador complet:

```
uid=mpuig,ou=Professorat,dc=ins,dc=cat
```

### **RDN (Relative Distinguished Name)**

```
uid=mpuig
```

---

<!-- Slide 17 -->

# 🔸11.0 DN, RDN i DIT

### **DIT (Directory Information Tree)**

Tree jeràrquic d'aquests d’objectes
```
dc=ins,dc=cat
└── ou=Professorat
├── uid=mpuig
└── uid=jroca
```

---

<!-- Slide 18 -->

# 🔸12.0 AD vs LDAP

| Aspecte      | AD                    | LDAP                    |
| ------------ | --------------------- | ----------------------- |
| Arquitectura | Domain / Tree / Forest | DIT                     |
| Autenticació | Kerberos + NTLM       | Simple bind (OpenLDAP) / SASL (Kerberos)      |
| Replicació   | Multimàster + FSMO    | syncrepl / MMR / mirror |
| Gestió       | GUI + RSAT            | CLI + phpldapadmin      |

---

<!-- Slide 19 -->

# 🔸13.0 Replicació LDAP

### **Syncrepl**

* client → servidor
* refresca i persisteix

### **Multi-Master Replication**

* diversos servidors poden escriure

### **Mirror Mode**

* servidor actiu + servidor mirall

---

<!-- Slide 20 -->

# 🔸14.0 Exemple syncrepl

```bash
syncrepl rid=001
  provider=ldap://ldap1.empresa.local
  type=refreshAndPersist
  searchbase="dc=empresa,dc=local"
```

---

<!-- Slide 16 -->

# 🔸15.0 Autenticació LDAP

### Simple bind

* Autenticació bàsica, directa
* Envia DN + contrasenya > Envia contrasenya en text pla
* Necessita TLS/SSL (túnel xifrat)

```
DN = uid=jordi,ou=IT,dc=empresa,dc=local
Password = ********
```

---

<!-- Slide 17 -->

# 🔸15.0 Autenticació LDAP

### SASL (GSSAPI)

* **SASL**: 
    - Simple Authentication and Security Layer
    - La **capa** d'autenticació (framework)
* **GSSAPI**: 
    - Generic Security Services Application Programming Interface
    - Un **connector** dins d’aquesta capa
* **Kerberos**
    - Autentificació avançada
    - El protocol real que fa el treball (SASL + GSSAPI)
* Més segur
* No cal enviar contrasenya

---

<!-- Slide 18 -->

# 🔸16.0 Eines LDAP

* `ldapsearch`             → consultes
* `ldapadd` / `ldapmodify` → gestió
* `slapcat` / `slapadd`    → export/import
* `phpldapadmin`           → gestió web

---

<!-- Slide 19 -->

# 🔸17.0 Resum final

* Un DC és el servidor central del domini
* AD s’organitza en dominis, arbres i boscos
* El GC accelera cerques i autenticació
* Kerberos és el nucli del logon (Sistema login de Windows, Kerberos, AD)
* LDAP proporciona un directori flexible
* AD i LDAP comparteixen conceptes però tenen rols diferents

