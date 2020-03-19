# tunelssh

Repositori per tunels ssh de l'assignatura M11 d' ASIX

# Exercicis

## 15. ldap-remot i phpldapadmin-local

Desplegem dins d’un container Docker (host-remot) en una AMI 
(host-destí) el servei ldap amb el firewall de la AMI només obrint el 
port 22. Localment al host de l’aula (host-local) desplegem un container
 amb phpldapadmin. Aquest container ha de poder accedir a les dades 
ldap. des del host de l’aula volem poder visualitzar el phpldapadmin.

**Desplegar el servei ldap**

- en el host-remot AMI AWS EC2 engegar un container ldap sense fer map dels ports.
- en la ami cal obrir únicament el port 22
- També cal configurar el /etc/hosts de la AMI per poder accedir al container ldap per nom de host (preferentment).
- verificar que des del host de l’aula (host-local) podem fer consultes ldap.

**Desplegar el servei phpldapadmin**

- engegar en el host de l’aula (host-local) un container docker amb el
   servei phpldapadmin fent map del seu port 8080 al host-local (o no).
- crear el túnel directe ssh des del host de l’aula (host-local) al 
  servei ldap (host-remot) connectant via SSH al host AMI (host-destí).
- configurar el phpldapadmin per que trobi la base de dades ldap 
  accedint al host de l’aula al port acabat de crear amb el túnel directe 
  ssh.
- Ara ja podem visualitzar des del host de l’aula el servei phpldapadmin, accedint al port 8080 del container phpldapadmin o al port que hem fet map del host de l’aula (si és que ho hem fet).

### Desplegar el ldap remot

Accedim a la màquina AMI.

```bash
[marc@localhost kerberos19]$ ssh -i swarm19_1.pem fedora@54.158.136.77
```

Arrenquem el servidor ldap amb dades

```bash
[fedora@ip-172-31-35-196 ~]$ sudo docker run --rm --name ldap.edt.org -h ldap.edt.org -d marcgc/ldapserver19 initdbedt
```

Comprovem que sol tenim obert el port del ssh a l'AMI

```bash
PORT   STATE SERVICE
22/tcp open  ssh
```

Arreglem la resolució de noms pel contàiner

```bash
[fedora@ip-172-31-35-196 ~]$ sudo vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.17.0.2 ldap.edt.org
```

Obrim el túnel per accedir des del host-local

```bash
[marc@localhost kerberos19]$ ssh -i swarm19_1.pem -L 50000:ldap.edt.org:389 fedora@54.158.136.77
```

Comprovem que podem veure les dades preguntant a localhost al port del túnel

```bash
[marc@localhost kerberos19]$ ldapsearch -x -LLL -h localhost -p 5000 -b 'dc=edt,dc=org' dn
dn: dc=edt,dc=org
dn: ou=usuaris,dc=edt,dc=org
dn: cn=Pau Pou,ou=usuaris,dc=edt,dc=org
dn: cn=Pere Pou,ou=usuaris,dc=edt,dc=org
dn: cn=Anna Pou,ou=usuaris,dc=edt,dc=org
dn: cn=Marta Mas,ou=usuaris,dc=edt,dc=org
dn: cn=Jordi Mas,ou=usuaris,dc=edt,dc=org
dn: cn=Admin System,ou=usuaris,dc=edt,dc=org
dn: cn=user01,ou=usuaris,dc=edt,dc=org
dn: cn=user02,ou=usuaris,dc=edt,dc=org
dn: cn=user03,ou=usuaris,dc=edt,dc=org
dn: cn=user04,ou=usuaris,dc=edt,dc=org
dn: cn=user05,ou=usuaris,dc=edt,dc=org
dn: cn=user06,ou=usuaris,dc=edt,dc=org
dn: cn=user07,ou=usuaris,dc=edt,dc=org
dn: cn=user08,ou=usuaris,dc=edt,dc=org
dn: cn=user09,ou=usuaris,dc=edt,dc=org
dn: cn=user10,ou=usuaris,dc=edt,dc=org
```

### Desplegar el servei phpldapadmin local

Primer obrirem el túnel des de l'interfície ldapnet (local) amb el servidor ldap remot, rebotant al host-destí

```bash
[marc@localhost kerberos19]$ ssh -i swarm19_1.pem -L 172.18.0.1:50000:ldap.edt.org:389 fedora@54.158.136.77
```

Editem la configuració per a que vagi pel túnel que hem creat i arrenquem el servei

```bash
$servers->setValue('server','host','172.18.0.1');
$servers->setValue('server','port',50000);
```

Accedim a la base de dades amb el phpldapadmin des del navegador del host local

![alt text](./aux/conex_local.png)

![alt text](./aux/autenticacio_ldap.png)

Comprovem que podem veure els users del server ldap remot

![alt text](./aux/users_ldap.png)
