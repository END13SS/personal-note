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
