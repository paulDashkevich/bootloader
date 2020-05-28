# Работа с загрузчиком #
1.Установил CentOS на LVM
2.При загрузке оси прописал rd.break (первый способ, второй - в той же строке заменить init=/sysroot/bin/sh ), потом нажал ctrl+x и вошёл в rescue mode с правами root'a

3.Переименование VG:
*   3.1 # vgdisplay
```sh
--- Volume group ---
VG Name centos
System ID
Format lvm2
Metadata Areas 1
Metadata Sequence No 3
VG Access read/write
VG Status resizable
MAX LV 0
Cur LV 2
Open LV 2
Max PV 0
Cur PV 1
Act PV 1
VG Size <59.00 GiB
PE Size 4.00 MiB
Total PE 15103
Alloc PE / Size 15103 / <59.00 GiB
Free PE / Size 0 / 0
VG UUID dlrixq-3JSz-GbiS-NGiI-Uuut-Jrmu-zg3tc3
```
3.2. Переименуем # vgrename centos CentOS
```sh
vgrename centos CentOS
# Volume group "centos" successfully renamed to "CentOS"
```
3.3 Пропишем новое название VG в следующих файлах: 
- ***[root@centos paul]# vi /etc/fstab***
- ***[root@centos paul]# vi /etc/fstab***
- ***[root@centos paul]# vi /etc/default/grub***
- ***[root@centos paul]# vi /boot/grub2/grub.cfg***
3.4 Перепишем загрузчик:
```sh

mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
```
```
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img' done ***
```
3.6 Ребутим машину и проверяем новое название VG
```
[root@centos paul]# vgdisplay
--- Volume group ---
VG Name CentOS
System ID
Format lvm2
Metadata Areas 1
Metadata Sequence No 4
VG Access read/write
VG Status resizable
MAX LV 0
Cur LV 2
Open LV 2
Max PV 0
Cur PV 1
Act PV 1
VG Size <59.00 GiB
PE Size 4.00 MiB
Total PE 15103
Alloc PE / Size 15103 / <59.00 GiB
Free PE / Size 0 / 0
VG UUID dlrixq-3JSz-GbiS-NGiI-Uuut-Jrmu-zg3tc3
```
4.Добавить модуль в init.rd
```
mkdir /usr/lib/dracut/modules.d/test
```
4.1. Пересобираем образ
```
mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
dracut -f -v
```
4.2. Проверяем загрузку модуля
```
lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img' done ***
```
4.3. Проверяем наличие установленного модуля
```
[root@centos paul]# lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
```
***test***

4.4. После перезагрузки проверяем результат

