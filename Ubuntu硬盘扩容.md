安装ubuntu系统时若选择系统自动分区，系统在使用Volume Group挂载根目录时只会使用硬盘其中一半的空间。
此次给该服务器多加了一块50G的硬盘，因此对于根目录的扩容，理论上最多能够增加近100G。
![image](https://user-images.githubusercontent.com/89510761/227770222-b04a5a4e-f8df-4be7-8537-7b0c49eec42c.png)

由于硬盘2是新增加的硬盘，必须新建分区才可以正常使用

```
fdisk /dev/sdb1
```

```
1. To Create new partition Press n.
2. Choose primary partition use p.
3. Choose which number of partition to be selected to create the primary partition.
4. Press 1 if any other disk available.
5. Change the type using t.
6. Type 8e to change the partition type to Linux LVM.
7. Use p to print the create partition ( here we have not used the option).
8. Press w to write the changes.
```

给 Volume Group扩容
ubuntu根目录的默认 volume Group是ubuntu-vg
```
pv create /dev/sdb1
vgextend ubuntu-vg /dev/sdb1
```

扩容前查看挂载信息，根目录只挂载了48G，已使用33G
```
df -h
```
![image](https://user-images.githubusercontent.com/89510761/227770457-5639b0bd-7e37-4e72-a540-f49e0855fc5f.png)
查看未被挂载的硬盘
```
lsblk
```
发现有两块硬盘sda（原100G的硬盘，只挂载了49G）、sdb（新增的50G硬盘），并未完全使用及挂载。
![image](https://user-images.githubusercontent.com/89510761/227770473-36993264-3494-4e89-a245-bbf0f3ba0c1c.png)

显示卷组信息
```
vgdisplay
```
![image](https://user-images.githubusercontent.com/89510761/227770485-6d6d3a02-caf3-4529-9c53-4983af3bb0be.png)

其中Free PE / Size 25343 / < 99.00 GiB 即还可以扩充的大小
开始使用命令进行磁盘扩容
```
#增加至140G
lvextend -L 140G /dev/mapper/ubuntu--vg-ubuntu--lv
```
执行调整（生效）
```
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```
```
df -h
```
成功扩容
![image](https://user-images.githubusercontent.com/89510761/227770508-19f1f0bf-03e6-43c8-ae40-1b98ed70155e.png)
