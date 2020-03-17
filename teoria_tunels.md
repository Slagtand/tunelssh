# Tunels SSH

## Explicació i objectiu

Un túnel ssh directe és aprofitar una connexió SSH segura per obrir un port en el **host local** que connecta amb un port d'un servei en un **host remot**, servei al que no es tindria accés directament.

És a dir, tindrem un port al host local (host de casa, per exemple) que connecta **directament** amb el port d'un servei remot (AMI d'Amazon sense mapejar) al qual no podriem accedir directament ja que es troba en una xarxa privada.

## Topologia

![alt text](./aux/topologia_ssh.png)

### Elements de la topologia

* `host-local`: host des del qual **establim** el túnel ssh. Per exemple, l'ordinador de casa o de classe.

* `host-destí`: host al que ens **connectem**. Necessitem un *user@desti* i que tingui el servei sshd engegat. Per exemple, la màquina AMI que hem obert a Amazon (assegurar que accepta connexions SSH)

* `host-remot`: qualsevol host que es troba en una **xarxa local privada** del **host-destí**. Des del **host-local** és impossible accedir-hi directament, hem de passar pel destí. Per exemple, el servei que tenim en un contàiner a la màquina AMI d'Amazon sense mapejar.

## Tipus de túnels

Hi han diferents tipus de túnels:

* `Túnel directe a host-destí`: és el tipus més usual i consisteix en accedir, des del *host-local*, a un **servei** d'un *host-destí* que no està publicat de cara a l'exterior.
  
  En aquest model obrim una porta local (al *host-local*) que permet accedir al port del **servei** del *host-destí*. Aquest és un port al que no podriem accedir directamente perquè no és públic.
  
  El túnel **s'estableix entre** *host-local* i *host-destí*, sent en el destí que **rebota** al **localhost al port del servei** al que es vol accedir.
  
  Els exemples més típics són per accedir a serveis del *host-destí* que no permeten connexions segures (un postgres, http-80, ldap-389...) i que no es publiquen a l'exterior per evitar-ne l'accés, només són accessibles des del propi *host-destí*.
  
  En aquest cas el **rebot** és al **propi host-destí**, indicat com a **localhost**.

* `Túnel directe a host-destí per accedir a host-remot-destí`: en aquest model volem accedir, des del *host-local*, a un servei que està en un *host-remot* al qual **no es té accés**.






