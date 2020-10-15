## LVM. CREACIÓ I MERGE D'UN SNAPSHOT
![Procés de creació  i merge d'un snapshot LVM2](image.png?raw=true "LVM2 Snapshot")

 1. Declarar sdb com a PV
 >     pvcreate /dev/sdb

 3. Crear un VG
>     vgcreate vg1 /dev/sdb

 3. Crear un LV, el formatejaré i l'ompliré de dades. L'objectiu és demostrar que puc retornar a aquest LV original
 
>     lvcreate vg1 -L 0.6G -n lv1
> 
(amb lvscan obtinc ruta lvm- si intento montar sense formatejar em dona error)
>     mkfs.ext4 /dev/vg1/lv1
>     mount /dev/vg1/lv1 /mnt/original

4. Crearé un snapshot d'aquest LV original

>     lvcreate -L 100M -n lv1_snap -s /dev/vg1/lv1
>     lvdisplay /dev/vg1/lv1_snap
>     lvdisplay /dev/vg1/lv1  (---source of lv1_snap)

5. Munto lv1_snap per observar què hi ha...
>     mkdir /mnt/snap

6. Modificaré els fitxers del LV original (afegint fitxers, esborrant-ne,...)
#script.sh
>     #!/bin/bash
>     for i in `seq -w 1 100`
>     do
>       touch `date +%Y%m%d`-$i.txt
>     done
>     wget http://ipv4.download.thinkbroadband.com/50MB.zip

Observo els canvis:

>     lvdisplay /dev/vg1/lv1_snap
>     lvs vg1

7. Reestablirem el LV original a partir de l'snapshot (que automàticament desapareix)
#Desmuntem els dos volums

>     umount /mnt/{original,snap}
>     lsof | grep /mnt/original  <-- si busy target

#Reestablirem el lv original a partir de l'snap

>     lvconvert --merge /dev/vg1/lv1_snap

#Comprovem que el lv_snap ha desaparegut

>     lvs vg1

#Tornem a muntar el lv per comprovar que hi ha els nostres fitxers del començament

>     mount /dev/vg1/lv1 /mnt/original
>     ls /mnt/original
```
![](https://www.youtube.com/watch?v=WPO3KiJtG1A&ab_channel=theurbanpenguin)
```
```
```
> The secret to creativity is knowing how to hide your sources. 
> -- <cite>[Albert Einstein][1]</cite>

[1]: http://www.quotedb.com/quotes/2112
```