# **EXERCICI RA01.2 - Esquema i estructura d’un Servei de Directori AD/LDAP**

<br>
<br>

# 1.Disseny de l'estructura del directori

**TorreTech** és una empresa amb dues seus, **Barcelona** i **Girona**. A totes dues seus hi treballa personal dels departaments d'**Informàtica**, **Administració** i **Comercial**. L'empresa utilitza el domini `torretech.test` i vol organitzar els seus usuaris mitjançant un servei de directori.

### 1.1. Dissenya el DIT

Dissenya un possible DIT per a **TorreTech** que permeti organitzar els usuaris segons la seu i el departament. Representa'l en forma d'arbre.

### 1.2. Afegeix usuari al DIT

Afegeix al DIT anterior l'usuari **Laia Serra**, que treballa al departament d'Informàtica de la seu de Barcelona. El seu identificador d'usuari és `lserra`.

a) Escriu el DN complet de Laia Serra.  
b) Quin és el seu RDN?  
c) Identifica els components `dc` i `ou` que apareixen en el DN.  

### 1.3 Reorganitza el DIT

El DIT es podria haver organitzat de manera diferent. Proposa una **estructura alternativa** que continuï permetent representar les dues seus i els tres departaments.

a) Representa-la en forma d'arbre.  
b) Explica quin criteri has utilitzat per organitzar-la.  
c) Indica un avantatge o inconvenient respecte de la primera estructura.  

<br>
<br>

# 2. Components d'Active Directory

### 2.1 Relació entre els components d'un entorn Active Directory

Relaciona les funcionalitats següents amb LDAP, Kerberos, DNS o GPO, segons correspongui:

* Consulta i gestió d'objectes del directori.
* Autenticació dels usuaris.
* Localització dels serveis i controladors de domini.
* Aplicació de configuracions i polítiques als usuaris i equips.

### 2.2. Explica breument quina funció té LDAP dins d'Active Directory.

<br>
<br>

# 3. Anàlisi de classes d’objecte (objectClass)

Tens la següent definició d’una classe d’objecte de LDAP (capítol 8):

```
objectClass ( 2.16.840.1.113730.3.2.2
  NAME 'inetOrgPerson'
  SUP organizationalPerson
  STRUCTURAL
  MUST ( cn $ sn )
  MAY ( mail $ uid $ telephoneNumber )
)
```

Respon:

a) Quin tipus de classe és? (Estructural / Auxiliar / Abstracta)  
b) Quins atributs **obligatoris** té?  
c) Quins atributs **opcionals** té?  
d) Posa un exemple de valor per a `cn`, `sn` i `mail` d'un usuari de l'empresa TorreTech.  

<br>
<br>

# 4. Definició d’atributs (attributetype)

A partir d’aquesta definició d’atribut del capítol 8:

```
attributetype ( 2.5.4.3
  NAME 'cn'
  DESC 'Common Name'
  SYNTAX 1.3.6.1.4.1.1466.115.121.1.15
  SINGLE-VALUE )
```

Respon:

a) L'OID que apareix després de `SYNTAX` identifica una sintaxi LDAP. Quina és?  
b) Per què és important saber si és `SINGLE-VALUE` o `MULTI-VALUE`?  
c) Posa un exemple de valor vàlid per a `cn`. Podria aquest atribut tenir dos valors diferents al mateix objecte? Justifica la resposta.  
d) L'atribut `cn` també existeix a Active Directory? Justifica la resposta a partir de l'esquema d'AD estudiat.  

<br>
<br>

#  5. Creació d’un objecte d’usuari (mix AD + LDAP)

Et donem la següent informació d’un usuari d’empresa:

* Nom: **Laura Pujol**
* Usuari: **lpujol**
* Departament: **Informàtica**
* Seu: **Girona**
* Rol: **Superusuari Informàtica**
* Correu: **lpujol@torretech.test**

### 5.1. Escriu un DN adequat per a aquest usuari

Escriu un DN adequat per a aquest usuari, mantenint l'estructura del DIT que has dissenyat a l'apartat 1.

### 5.2. Escriu com quedaria l’usuari en un LDIF (només atributs bàsics):

```
dn: ...
objectClass: inetOrgPerson
cn: ...
sn: ...
uid: ...
mail: ...
```

### 5.3. Escriu quins objectes crearies al AD

Explica quines `ou`, usuari i grups crearies a Active Directory del subapartat anterior per representar Laura Pujol dins de TorreTech. Tingues en compte la seva seu, departament i rol.


<br>
<br>

#  **6. Mini cas pràctic de reflexió (AD + LDAP)**

Contesta:

a) `sAMAccountName` i `uid` poden identificar un usuari en diferents esquemes de directori. Explica en quin context podem trobar cadascun d'aquests atributs.  
b) Per què una entrada LDAP pot tenir més d'un valor `objectClass`? Què aporta cada classe a l'objecte?  

