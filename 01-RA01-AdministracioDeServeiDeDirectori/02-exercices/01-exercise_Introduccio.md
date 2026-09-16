# **01- Exercicis d’introducció als serveis de directori**

## **Exercici 1 - Per què existeixen els serveis de directori?**

Respon:

1. Explica dues situacions reals on una organització podria tenir problemes **per no tenir un servei de directori**.
2. Quins avantatges aporta un servei de directori quan hi ha:
   * molts usuaris,
   * molts equips,
   * diferents permisos?
3. Quina diferència hi ha entre administrar usuaris **individualment en cada PC** i fer-ho **des d’un directori centralitzat**?

---

## **Exercici 2 - Definició de servei de directori**

1. Defineix amb les teves paraules què és un **servei de directori**.
2. Explica per què un servei de directori NO és el mateix que una base de dades relacional.
3. Completa la taula:

| Característica | Base de dades SQL | Servei de directori |
| -------------- | ----------------- | ------------------- |
| Estructura     |                   |                     |
| Tipus de dades |                   |                     |
| Optimització   |                   |                     |
| Exemple        |                   |                     |

---

## **Exercici 3 - Evolució i tecnologies associades**

Ordena cronològicament aquestes tecnologies i explica el paper de cadascuna:
* Active Directory
* X.500
* LDAP

I respon:
1. Quina tecnologia va simplificar X.500 per fer-la usable a Internet?
2. Quina implementació combina LDAP + Kerberos + DNS?
3. Quin avantatge principal té LDAP respecte a X.500?

---

## **Exercici 4 - Components essencials d’un servei de directori**

### **4.1 Objectes**

Digues si cada exemple és un **objecte d’usuari**, **d’equip**, **de grup** o **una OU**:

| Exemple           | Categoria |
| ----------------- | --------- |
| cn=Javier de Palol |           |
| cn=PC-A201        |           |
| ou=Professors     |           |
| cn=AlumnesA2      |           |

### **4.2 Atributs**

Indica quin tipus d’informació són aquests atributs:

* `cn`
* `sn`
* `mail`
* `uid`
* `objectClass`

### **4.3 Esquema**

Respon:

1. Què defineix exactament un **esquema**?
2. Quina diferència hi ha entre un atribut **obligatori (MUST)** i un **opcional (MAY)**?

---

## **Exercici 5 - El DIT i la jerarquia del directori**

Observa aquest DIT:

```
dc=empresa,dc=local
 ├── ou=Professors
 │     └── cn=Montse Grau
 ├── ou=Alumnes
 │     ├── cn=Joel Font
 │     └── cn=Paula Ruiz
 └── ou=Equips
       └── cn=PC-A202
```

Respon:
1. Quin és el domini complet?
2. Quants objectes hi ha en total?
3. Quin és el DN complet de “PC-A202”?
4. Les OU poden contenir altres objectes? Quins?
5. Per què la jerarquia del directori facilita l’organització d’una empresa?

---

## **Exercici 6 - DN, RDN i estructura bàsica LDAP**

Analitza aquesta DN:

```
cn=Laura Serra,ou=Alumnes,dc=empresa,dc=local
```

I respon:
1. Quin és el **RDN**?
2. Quina és l’**OU**?
3. Quin és el domini?
4. Què representa aquesta entrada?
5. Per què cada objecte té un DN únic?

---

## **Exercici 7 - Arquitectura general d’un servei de directori**

1. Quin és el paper del **client** en un servei de directori?
2. Quin és el paper del **servidor**?
3. Quina funció compleix **LDAP**?
4. Quina funció compleix **Kerberos**?
5. Per què DNS és necessari en entorns d’Active Directory?

