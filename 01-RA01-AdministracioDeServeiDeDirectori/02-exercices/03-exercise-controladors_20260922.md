# **EXERCICI CAPÍTOL 3: Controladors de domini i arquitectures AD/LDAP**

Exercici d'avaluació i comprensió dels conceptes teòrics del capítol 3:

* Què és un controlador de domini   
* Quina arquitectura defineix AD  
* Quina arquitectura defineix LDAP  
* Com s'organitzen els dominis, arbres i boscos  
* Com es representa la informació en un DIT  
* Com es relacionen AD i LDAP a nivell conceptual  

<br>
<br>

# **PART 1: Preguntes conceptuals (resposta curta)**

### **1. Defineix Controlador de Domini (Domain Controller)**

Descriu quin paper té dins d'un servei de directori i quina informació manté.

---

### **2. Quin és el paper de *Kerberos* en l'arquitectura d'un controlador de domini AD?**

Explica per què és necessari i què aporta.

---

### **3. Explica la diferència entre:**

* **Domini**
* **Arbre**
* **Bosc**

Fes servir un exemple jeràrquic senzill.

---

### **4. Què és un *Global Catalog*?**

Quina funció té dins l'arquitectura AD i per què és important en entorns amb múltiples dominis?

---

### **5. En LDAP, què és un *DIT (Directory Information Tree)*?**

Descriu-lo amb una frase clara i un exemple sintètic.

---

### **6. Quina diferència conceptual hi ha entre AD i LDAP pel que fa a:**

* estructura del directori,
* autenticació,
* rols del servidor.

No parlis d'instal·lació, només d'arquitectura.

---

### **7. Defineix *DN* i *RDN* en LDAP.**

Dóna un exemple de cadascun.

---

### **8. Per què els controladors de domini han d'utilitzar replicació multimestre?**

Quin problema resol?

<br>
<br>

# **PART 2: Exercicis d'anàlisi d'arquitectura (teoria aplicada)**

### **9. Dibuixa (en text) l'arquitectura mínima d'un entorn AD amb:**

* Un domini `ins.local`
* Un controlador principal de domini
* Un controlador de domini replicat
* Dos clients

Representa el flux conceptual d'autenticació
*(Sense instal·lar res.)*

---

### **10. En un entorn 100% Linux, explica com funcionaria l'autenticació utilitzant OpenLDAP.**

Descriu el flux general: client → servidor → validació d'atributs

---

### **11. Analitza aquest DN i explica cada component:**

```
uid=mpuig,ou=Professorat,dc=ins-torreroja,dc=cat
```

Indica:

* Quin és el RDN
* Quines són les OUs
* Quin és el domini
* On se situaria l'objecte dins del DIT

---

### **12. Un institut té dos edificis diferents però vol una administració centralitzada.**

Sense parlar d'instal·lacions, indica:

* Quants dominis necessitaria
* Com s'organitzaria l'arquitectura AD (domini/arbres/bosc)
* Com circularia la informació entre controladors de domini

<br>
<br>

# **PART 3: Exercici de comparació (AD vs LDAP)**

### **13. Completa aquesta taula comparativa segons la teoria del Capítol 3**

| Aspecte                | Active Directory (AD) | LDAP (OpenLDAP) |
| ---------------------- | --------------------- | --------------- |
| Naturalesa             | ?                     | ?               |
| Organització           | ?                     | ?               |
| Autenticació principal | ?                     | ?               |
| Servei de directori    | ?                     | ?               |
| Rol del servidor       | ?                     | ?               |
| Model de replicació    | ?                     | ?               |

(Cada cel·la s'ha d'omplir segons la teoria)

<br>
<br>

# **PART 4: Exercici de disseny conceptual**

### **14. Dissenya un model d'arquitectura AD + LDAP per un centre educatiu que:**

* Tingui professors i alumnes
* Necessiti control centralitzat d'identitat
* Utilitzi AD per Windows i LDAP com a font externa d'aplicacions (Moodle o Nextcloud)

Sense parlar d'instal·lació, indica:

* Com es relacionen conceptualment els dos serveis
* Quin rol fa cadascun
* Quin servidor actua com a font d'identitat
* Quin manté el DIT i quin manté el domini


