# Лабораторная работа №2
## Задание 1 (Установка ОС и настройка LVM, RAID)
1. Создание новой виртуальной машины, выдав ей следующие характеристики:
* 1 gb ram
* 1 cpu
* 2 hdd (назвав их ssd1, ssd2 и назначил равный размер, поставив галочки hot swap и ssd)
* SATA контроллер настроен на 4 порта
!(не было своего скриншота)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/Screenshot_1.png)
2. Начало установки Linux:
![alt-текст](https://github.com/Kindface/Linux-labs/blob/master/lab2/images/VirtualBox_Raid_26_03_2019_16_55_33.png)
* Настройка отдельного раздела под /boot: Выбрав первый диск, создал на нем новую таблицу разделов
+ Partition size: 512M
+ Mount point: /boot
+ Повторил настройки для второго диска, выбрав mount point:none
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_10_53_27.png)
* Настройка RAID
+ Выбрал свободное место на первом диске и настроил в качестве типа раздела physical volume for RAID
+ Выбрал "Done setting up the partition"
+ Повторил настройку для второго диска
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_11_01_28.png)
* Выбрал пункт "Configure software RAID"
+ Create MD device
+ Software RAID device type: Выберал зеркальный массив
+ Active devices for the RAID XXXX array: Выбрал оба диска
+ Spare devices: Оставил 0 по умолчанию
+ Active devices for the RAID XX array: Выбрал разделы, которые создавал под raid
+ Finish
* В итоге получил: 
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_11_02_11.png)
* Настройка LVM: Выбрал Configure the Logical Volume Manager
+ Keep current partition layout and configure LVM: Yes
+ Create volume group
+ Volume group name: system
+ Devices for the new volume group: Выбрал созданный RAID
+ Create logical volume
+ logical volume name: root
+ logical volume size: 2\5 от размера диска
+ Create logical volume
+ logical volume name: var
+ logical volume size: 2\5 от размера диска
+ Create logical volume
+ logical volume name: log
+ logical volume size: 1\5 от размера диска
+ Завершив настройку LVM увидел следующее:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_11_09_24.png)
* Разметка разделов: по-очереди выбрал каждый созданный в LVM том и разметил их, например, для root так:
+ Use as: ext4
+ mount point: /
+ повторил операцию разметки для var и log выбрав соответствующие точки монтирования (/var и /var/log), получив следующий результат:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_11_16_33.png)
3. Закончил установку ОС, поставив grub на первое устройство (sda) и загрузил систему.
4. Выполнил копирование содержимого раздела /boot с диска sda (ssd1) на диск sdb (ssd2)
5. Выполнил установку grub на второе устройство:
+ sda - ssd1
+ sdb -ssd2
* Посмотрел информацию о текущем raid командой cat /proc/mdstat (не было своего скриншота):
![alt-текст](https://raw.githubusercontent.com/Kindface/Linux-labs/master/lab2/images/VirtualBox_Raid_26_03_2019_17_35_39.png)
Увидел, что активны два raid1 sda2[0] и sdb2[1]
* Выводы команд: pvs, vgs, lvs, mount:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_1/VirtualBox_pojiloi_raid_06_04_2019_11_29_29.png)

* С помощью этих команд увидел информацию об physical volumes, volume groups, logical volumes, примонтированных устройств.
## Вывод
В этом задании научился устанавливать ОС Linux, настраивать LVM и RAID, а также ознакомился с командами:
* lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
* fdisk -l
* pvs,lvs,vgs
* cat /proc/mdstat
* mount
* dd if=/dev/xxx of=/dev/yyy
* grub-install /dev/XXX 
* В результате получил виртуальную машину с дисками ssd1, ssd2.
# Задание 2 (Эмуляция отказа одного из дисков)
1. Удаление диска ssd1 в свойствах машины. 
2. Проверка работоспособности виртуальной машины.
3. Проведена перезагрузка.
4. Проверка статуса RAID-массива командой cat /proc/mdstat. 
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_2/VirtualBox_Клон_pojiloi_raid_11_05_2019_14_13_23.png)
5. Добавление в интерфейсе VM нового диска такого же размера с названием ssd3.
6. Выполнение операций:
* Просмотр нового диска, что он приехал в систему командой fdisk -l
* Копирование таблиц разделов со старого диска на новый: sfdisk -d /dev/XXXX | sfdisk /dev/YYY
* Добавление в рейд массив нового диска: mdadm --manage /dev/md0 --add /dev/YYY
* Результат:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_2/VirtualBox_pojiloi_raid_2_06_04_2019_12_01_54.png)
7. Выполение синхронизации разделов, не входящих в RAID
8. Установка grub на новый диск
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_2/VirtualBox_Клон_pojiloi_raid_11_05_2019_14_25_43.png)
9. Перезагрузка ВМ и проверка, что все работает
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_2/VirtualBox_Клон_pojiloi_raid_11_05_2019_14_27_15.png)
## Вывод
В этом задании научился:
* Удалять диск ssd1
* Проверять статус RAID-массива
* Копировать таблицу разделов со старого диска на новый
* Добавлять в рейд массив новый диск
* Выполнять синхронизацию разделов, не входящих в RAID

Изучил новые команды:
* sfdisk -d /dev/XXXX | sfdisk /dev/YYY
* mdadm --manage /dev/md0 --add /dev/YYY

Результат: Удален диск ssd1, добавлен диск ssd3, ssd2 сохранили
# Задание 3 (Добавление новых дисков и перенос раздела)
1. Эмулирую отказа диска ssd2, удалив из свойств ВМ диск и перезагрузившись
2. Текущее состояние дисков и RAID:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_14_38_42.png)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_14_39_08.png)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_14_39_42.png)
3. Мне повезло.
4. Добавляю ssd4
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_14_44_43.png)
5. Перенос данных с помощью LVM.
* Копирование файловую таблицу со старого диска на новый
* Копирование данных /boot на новый диск  
* Перемонтировака /boot на живой диск
* Установка grub на новый диск
Grub устанавливаем, чтобы могли загрузить ОС с этого диска
* Создание нового RAID-массива с включением туда только одного нового ssd диска:
* Результат
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_14_58_51.png)
Появился /dev/md63
6. Настройка LVM
* Выполнение команды pvs для просмотра информации о текущих физических томах
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_15_00_11.png)
* Создание нового физического тома, включив в него ранее созданный RAID массив:
* После выполнения команд lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT и pvs яувидел, что к md63 добавился FSTYPE - LVM2_member, так же dev/md63 добавился к результату команды pvs.
* Увеличение размера Volume Group system
* Выполнение команд
```
vgdisplay system -v
pvs
vgs
lvs -a -o+devices
```
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_15_05_10.png)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_15_06_48.png)
LV var,log,root находятся на /dev/md0
* Перемещение данных со старого диска на новый
* Изменение VG, удалив из него диск старого raid.
* Выполнение команд:
```
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
pvs
vgs
```
[alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.1_pojiloi_raid_11_05_2019_15_16_04.png)

В выводе команды pvs у /dev/md0 исчезли VG и Attr. 
В выводе команды vgs #PV - уменьшилось на 1, VSize, VFree - стали меньше

7. Удаление ssd3 и добавление ssd5,hdd1,hdd2.
8. Полсе добавления дисков:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_08_55_12.png)

9. Восстановление работы RAID массива:
* Копирование таблицы разделов:
10. Копирование загрузочного раздела /boot с диска ssd4 на ssd5
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_08_58_40.png)
11. Установка grub на ssd5
!
[alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_08_59_38.png)
12. Изменение размера второго раздела диска ssd5
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_03_04.png)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_01_57.png)
13. Перечитывание таблицы разделов
* Добавление нового диска к текущему raid массиву
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_12_23.png)
* Расширение количество дисков в массиве до 2-х штук:
14. Увеличение размера раздела на диске ssd4
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_18_42.png)
15. Перечитаем таблицу разделов
16. Расширение размера raid
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_26_36.png)

Размер md127 изменился, теперь он равен 5,8G.

17. Расширим размер PV.
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_28_08.png)
18. Добавление вновь появившееся место VG var,root.
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_33_25.png)
15. Создание LVM на новых hdd. 
* Имена новых hhd дисков:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_06_02.png)
* Создание RAID массива
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_36_49.png)
* Создание нового PV на рейде из больших дисков
* Создание в этом PV группу с названием data
* Создание логического тома с размером всего свободного пространства и присвоением ему имени var_log
* Отформатирование созданного раздела в ext4
* Результат:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_40_10.png)
20. Перенос данных логов со старого раздела на новый
* Примонтирование временно нового хранилище логов
* Выполнение синхронизации разделов
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_51_21.png)
* Процессы работающие с /var/log
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_55_07.png)
* Остановка этих процессов
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_55_50.png)
* Выполнение финальной синхронизации разделов
* Поменяем местами разделы
* Результат:
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_09_59_05.png)
21. Правим /etc/fstab.
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_10_03_30.png)
23. Финальный аккорд
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_10_05_54.png)
![alt-текст](https://github.com/Teasty/admin_labs/blob/master/lab_2/screenshots_task_3/VirtualBox_Клон_3.8_pojiloi_raid_clone_23_05_2019_10_06_24.png)
