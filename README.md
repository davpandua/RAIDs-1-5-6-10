# RAIDs

* Els RAIDs proporcionen fiabilitat (lectura/escritura) i velocitat de disc (en redundancia)


| Nivells | Mín.Disc | Pot fallar |Capacitat | Lectura | Escritura |
| :------- | :------: | :-----:   | :-----:  | :--:    |---:
| RAID 0   | 2     	  | 0		  | 100%	 |Excelent |Excelent |
| RAID 1   | 2     	  | 1         | 5%		 |Molt bona | Bona	|
| RAID 5   |  3       | 1         |			 |Molt bona | Bona
|RAID 6	   | 4		  | 2		  |50%-88%	 |Bona		| Bona
|RAID 10   | 4		  | 1/mirror  | 


#### RAID per Hardware

* RAID configurat desde la BIOS. D'aquesta manera es pot instal·ar a continuació un S.O a sobre del RAID.

#### RAID per Software

* Linux proporciona una eina per crear diferents tipus de raid: **mdadm**. Pero abans crearem els discos per al RAID.

1. Arranquem una màquina Linux (si es posible Fedora) amb el Virt-Manager.
2. Per crear el RAID 1 necessitem 2 discos: obrim el visor de detalls (la bombeta).
3. Fem clic dret i afegim un disc (Add Hardware). Per fer les proves escollirem VirtIO al Bus Type.
4. Anem al terminal del S.O i comprovem que s'han afegit els discos: **lsblk**
5. El nom dels discos seràn, per exemple, **/dev/vda** i **/dev/vdb**.

#### Creant **RAID 1** amb **mdadm**

* **mdadm --create /dev/md0 --level=1 --raid-device=2 /dev/vda /dev/vdb**
* Es crea un sistema RAID de disc **md0** (*--create /dev/md0*) de tipus 1 (*--level=1*), escollim cuants discos a fer servir (*--raid-device=2*) i quins d'ells son (*/dev/vda /dev/vdb*).
* Comprobem que s'ha creat: **lsblk**. Veurem **md0** per sota dels discos **vda** y **vdb** amb el tipus **raid1**.
* Confirmem el RAID: **--yes**
* Comprovem informació del RAID:
   **cat /proc/mdstat**
* Per poder utilitzar el RAID, hem de formatejar-lo:
   **mkfs.ext4 /dev/mdo**
* I l'hem de montar:
  **mount /dev/mdo /mnt**
* Mirem si està montat:
  **df -h**
