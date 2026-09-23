# **EXERCICI CAPÍTOL 3: Controladors de domini i arquitectures AD/LDAP**

## Context

**TorreTech** és una empresa amb dues seus, **Barcelona** i **Girona**. A totes dues seus hi treballa personal dels departaments d'**Informàtica**, **Administració** i **Comercial**.

L'empresa utilitza el domini:

```
torretech.test
```

En els exercicis anteriors has dissenyat l'estructura lògica del directori i has treballat amb els objectes i atributs que permeten representar-ne la informació.

Ara **TorreTech** vol desplegar una infraestructura basada en **Active Directory** per centralitzar la gestió d'usuaris, equips i recursos.

L'empresa necessita que:

* els usuaris puguin iniciar sessió amb un compte corporatiu
* els equips Windows formin part del domini
* els administradors puguin gestionar de manera centralitzada usuaris, grups i equips
* les dues seus puguin continuar treballant davant d'una incidència temporal en les comunicacions
* la informació del directori es mantingui sincronitzada entre els servidors

> En aquest exercici **no cal instal·lar ni configurar cap servidor**. L'objectiu és analitzar i dissenyar l'arquitectura que posteriorment implementarem.

---

## 1. Del directori al controlador de domini

### 1.1. El controlador de domini

TorreTech necessita un servidor que permeti posar en funcionament el directori que has dissenyat.

Explica amb les teves paraules què és un **controlador de domini (DC)** i quines funcions realitzaria dins de TorreTech.

En la resposta pots tenir en compte la gestió d'usuaris, grups i equips, l'autenticació, l'autorització i l'emmagatzematge de la informació del directori.

### 1.2. Del disseny lògic al servidor

En el capítol anterior vas dissenyar una estructura per representar les seus, departaments i usuaris de TorreTech.

Explica la diferència entre:

* **l'estructura lògica del directori**, i
* **el controlador de domini que permet gestionar-la i oferir el servei als clients**.

Indica també alguns dels objectes del teu disseny anterior que gestionaria el controlador de domini.

---

## 2. Domini, arbre i bosc

### 2.1. El domini de TorreTech

TorreTech vol utilitzar un únic domini per gestionar inicialment les dues seus.

a) Quin seria el nom DNS del domini d'Active Directory?

b) Què representa aquest domini dins de l'arquitectura?

c) Barcelona i Girona necessiten ser dos dominis diferents? Justifica la resposta.

d) Explica com podries representar les dues seus i els seus departaments **dins del mateix domini**, aprofitant els conceptes treballats als exercicis anteriors.

### 2.2. Arbre i bosc

Tenint en compte l'arquitectura inicial de TorreTech, respon:

a) Quants dominis té inicialment?

b) Quants arbres?

c) Quants boscos?

d) Explica amb les teves paraules la relació que hi ha entre **domini, arbre i bosc** en aquest cas.

### 2.3. TorreTech creix

Uns anys després, TorreTech incorpora una altra empresa que utilitza el domini:

```text
northwind.example
```

Suposa que l'empresa decideix incorporar aquest domini **dins del mateix bosc d'Active Directory** que TorreTech.

Respon:

a) `torretech.test` i `northwind.example` formarien part del mateix arbre o d'arbres diferents? Justifica-ho a partir dels seus noms DNS.

b) Quants dominis, arbres i boscos tindria ara l'arquitectura?

c) Què compartirien els dos arbres pel fet de pertànyer al mateix bosc?

---

## 3. Serveis que fan funcionar Active Directory

### 3.1. Relaciona cada necessitat amb el component principal

Indica quin component intervé principalment en cada situació: **LDAP, Kerberos, DNS o GPO**.

| Necessitat                                           | Component |
| ---------------------------------------------------- | --------- |
| Consultar usuaris, grups i equips del directori      |           |
| Autenticar un usuari que inicia sessió               |           |
| Localitzar un controlador de domini                  |           |
| Aplicar una configuració als equips d'un departament |           |
| Consultar objectes del directori des d'una aplicació |           |

> Recupera els conceptes treballats al capítol 2 i utilitza’ls després per explicar els següents escenaris

### 3.2. Inici de sessió al domini

La **Laura Pujol** arriba a la seu de Girona i inicia sessió en un ordinador que forma part del domini `torretech.test`.

Explica de manera conceptual què passa des que introdueix les seves credencials fins que el domini valida la seva identitat.

En l'explicació han d'aparèixer:

* el paper de **DNS** per localitzar un controlador de domini
* el paper de **Kerberos** en l'autenticació
* el paper d'**Active Directory** en la validació de la identitat i la pertinença a grups

> No cal explicar els algoritmes criptogràfics utilitzats per Kerberos.

### 3.3. Una aplicació consulta el directori

Una aplicació web interna de TorreTech necessita consultar els usuaris i grups emmagatzemats al directori.

a) Quin protocol podria utilitzar per fer aquestes consultes?

b) Quina informació podria obtenir del directori?

c) L'aplicació necessita convertir-se en un controlador de domini per consultar aquesta informació? Justifica-ho.

d) Explica la diferència entre **consultar el directori** i **actuar com a controlador de domini**.

---

## 4. Dos controladors de domini i replicació

TorreTech vol millorar la disponibilitat del servei instal·lant un controlador de domini a cada seu:

```text
Barcelona → DC-BC01
Girona    → DC-GI01
```

Tots dos formen part del domini:

```text
torretech.test
```

### 4.1. Representa l'arquitectura

Completa el diagrama següent incorporant:

* `DC-BC01`
* `DC-GI01`
* un client de Barcelona
* un client de Girona
* el domini `torretech.test`
* la replicació entre els dos controladors

```text
                 torretech.test

           DC-BC01           DC-GI01
              |                  |
         Client BC          Client GI
```

Indica amb fletxes com es produeix la replicació de la informació.

### 4.2. Per què dos controladors?

Explica **dos avantatges** de disposar d'un controlador de domini a Barcelona i un altre a Girona.

Relaciona la resposta amb les necessitats de TorreTech.

### 4.3. Cau la comunicació entre les seus

Una avaria interromp durant una hora la comunicació entre Barcelona i Girona. Els dos controladors de domini continuen funcionant correctament.

Respon:

a) Podrien continuar autenticant-se els usuaris de Barcelona? Per què?

b) Podrien continuar autenticant-se els usuaris de Girona? Per què?

c) Es podrien replicar els canvis entre `DC-BC01` i `DC-GI01` durant l'avaria?

d) Què hauria de passar amb els canvis quan es recuperés la comunicació?

e) Què podria passar si durant la interrupció es modifiqués el mateix objecte des dels dos controladors?

> Considera que cada seu disposa d’un controlador de domini operatiu i que els clients poden resoldre correctament els serveis locals.

### 4.4. Replicació multimàster

A partir del cas anterior, explica què significa que Active Directory utilitzi un model de **replicació multimàster**.

En la resposta indica:

* si els dos controladors poden rebre modificacions
* per què aquest model millora la disponibilitat
* per què cal sincronitzar posteriorment els canvis
* per què poden ser necessaris mecanismes de resolució de conflictes

---

## 5. Global Catalog

Suposa ara que TorreTech ha incorporat Northwind i que els dos dominis formen part del mateix bosc d’Active Directory:

```text
Bosc TorreTech
│
├── torretech.test
│
└── northwind.example
```

Un administrador necessita poder localitzar objectes del bosc sense haver de saber prèviament en quin domini es troben.

### 5.1. Cerca dins del bosc

Explica què és el **Global Catalog** i per què resulta útil en aquesta arquitectura.

En la resposta diferencia entre:

* la informació completa del domini propi
* la informació parcial procedent dels altres dominis del bosc

### 5.2. I si només hi hagués un domini?

Torna a la situació inicial, en què TorreTech només tenia:

```text
torretech.test
```

El Global Catalog continuaria existint i tenint utilitat?

Justifica breument la resposta tenint en compte que ja no caldria cercar objectes en altres dominis.

---

## 6. Síntesi de l'arquitectura

Torna a la situació inicial de TorreTech, abans de la incorporació de **Northwind**.

Representa en **un únic diagrama** l'arquitectura que proposaries per donar servei a Barcelona i Girona.

El diagrama ha d'incloure com a mínim:

* el domini `torretech.test`
* les dues seus
* `DC-BC01` i `DC-GI01`
* clients de les dues seus
* la relació entre clients i controladors de domini
* DNS
* Kerberos
* la replicació entre els controladors

Acompanya el diagrama d'una **breu justificació** de les decisions preses.

> La justificació ha de ser breu (màxim 10–12 línies) i ha d’explicar per què has decidit aquest nombre de dominis, controladors i serveis.
