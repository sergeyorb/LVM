# Домашнее задание LVM
<ol> 
  <li> Создать сденд для выполнения домашнего задания
  <li> Создать PV 
  <li> Создать VG 
  <li> Создать LV 
  <li> Создать файловую систему и смонтировать её
  <li> Скопировать все данные с / раздела в /mnt
  <li> Переконфигурируем grub
</ol>  

# 1. Создать сденд для выполнения домашнего задания
<ul>
  <p> Развернута VM на базе CentOS-7-x86_64
</ul>

# 2. Создать PV
<ul>
  <p> pvcreate /dev/sdb
</ul>  

# 3. Создать VG
<ul>
  <p> vgcreate vg_root /dev/sdb
</ul>

# 4. Создать LV
<ul>
  <p>  lvcreate -n lv_root -l +100%FREE /dev/vg_root
</ul>

# 5. Создать файловую систему и смонтировать её
<ul>
  <p> mkfs.xfs /dev/vg_root/lv_root
  <p> mount /dev/vg_root/lv_root /mnt  
</ul>

# 6. Скопировать все данные с / раздела в /mnt
<ul>
  <p> xfsdump -J - /dev/VolGroup00/LogVol00 | xfsrestore -J - /mnt
</ul>

# 7. Переконфигурируем grub
<ul>
  <p> for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done
  <p> chroot /mnt/
  <p> grub2-mkconfig -o /boot/grub2/grub.cfg
  <p> cd /boot ; for i in `ls initramfs-*img`; do dracut -v $i `echo $i|sed "s/initramfs-//g; s/.img//g"` --force; done
  <p> в файле /boot/grub2/grub.cfg заменил rd.lvm.lv=VolGroup00/LogVol00 на rd.lvm.lv=vg_root/lv_root
</ul>

Данные действия привели к тому, что VM повисла и больше не запустилась
