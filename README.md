# RAIDs

* Els RAIDs proporcionen **fiabilitat** (lectura/escritura) i **velocitat** de disc (en redundancia)


| Nivells | Mín.Disc | Pot fallar |Capacitat | Lectura | Escritura |
| :------- | :------: | :-----:   | :-----:  | :--:    |---:
| RAID 0   | 2     	  | 0		  | 100%	 |Excelent |Excelent |
| RAID 1   | 2     	  | 1         | 5%		 |Molt bona | Bona	|
| RAID 5   |  3       | 1         |			 |Molt bona | Bona
|RAID 6	   | 4		  | 2		  |50%-88%	 |Bona		| Bona
|RAID 10   | 4		  | 1/mirror  | 50% | Molt bona | Bona


#### RAID per Hardware

* RAID configurat desde la BIOS. D'aquesta manera es pot instal·ar a continuació un S.O a sobre del RAID.

#### RAID per Software

* Linux proporciona una eina per crear diferents tipus de raid: **mdadm**. Pero abans crearem els discos per al RAID.

1. Arranquem una màquina virutal Linux (si es posible Fedora) amb el Virt-Manager.
2. Per crear el RAID 1 necessitem 2 discos: obrim el visor de detalls (la bombeta).
3. Fem clic dret i afegim un disc (**Add Hardware**). Per fer les proves escollirem **VirtIO** al **Bus Type**.
4. Anem al terminal del S.O i comprovem que s'han afegit els discos:  
**lsblk**
5. El nom dels discos seràn, per exemple, **/dev/vda** i **/dev/vdb**.

#### Creant RAID 1 amb mdadm

* **mdadm --create /dev/md0 --level=1 --raid-device=2 /dev/vda /dev/vdb**
* Es crea un sistema RAID de disc **md0** (__*--create /dev/md0*__) de tipus 1 (__*--level=1*__), escollim cuants discos a fer servir (__*--raid-device=2*__) i quins d'ells son (__*/dev/vda /dev/vdb*__).
* Comprobem que s'ha creat:  
**lsblk**. Veurem **md0** per sota dels discos **vda** y **vdb** amb el tipus **raid1**.
* Confirmem el RAID:  
**yes**
* Comprovem informació del RAID:  
**cat /proc/mdstat**
* Per poder utilitzar el RAID, hem de formatejar-lo:  
**mkfs.ext4 /dev/md0**
* I l'hem de montar:  
**mount /dev/md0 /mnt**
* Mirem si està montat:  
**df -h**
  
#### Fent proves amb el RAID
* Entrem a la partició montada del RAID  
**cd /mnt**  
* Creem un disc de tipus **/dev/zero** amb **dd**:  
**dd if=/dev/zero of=test.img bs=4K count=1000**
* Comprovem:  
**ll -lh**  
**lsblk**  
**cat /proc/mdstat** (veure informació del RAID)  
* Comprovem l'estat del RAID:  
**mdadm --detail /dev/md0**  
* Si un dels discos del RAID, en algun moment està defectuos, podem eliminar aquest disc defectuos:  
**mdadm /dev/md0 --remove /dev/vda**  
* Podem afegir algun disc que tinguem de reserva (*spare*):  
**mdadm /dev/md0 --add /dev/vdc**  
* Si veiem que un dels discos dona molts problemes (no s'esborra, dona erros, etc), el podem eliminar d'una manera dràstica:  
**mdadm --zero-superblock /dev/vda**  
#### Creació RAID 5  
* Fem el mateix procedimen que l'anterior pero amb 3 discos:  
* **mdadm --create /dev/md0 --level=5 --raid-device=3 /dev/vda /dev/vdb /dev/vdb**  
#### Creació RAID 10
* **mdamdm --create /dev/md0 --level=10 --raid-devices=4 /dev/vda /dev/vdb /dev/vdc /dev/vdd**
