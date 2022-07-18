- [1. Tìm hiểu tổng quan LVM](#1-tìm-hiểu-tổng-quan-lvm)
  - [1.1. LVM là gì?](#11-lvm-là-gì)
  - [1.2 LVM dùng để làm gì?](#12-lvm-dùng-để-làm-gì)
  - [1.3. Các thành phần của LVM](#13-các-thành-phần-của-lvm)
- [2. Lab LVM](#2-lab-lvm)
  - [2.1 Tạo LVM](#21-tạo-lvm)
    - [2.1.1 Chuẩn bị](#211-chuẩn-bị)
    - [2.1.2 Tạo Partition](#212-tạo-partition)
    - [2.1.3 Tạo Physical Volume](#213-tạo-physical-volume)
    - [2.1.4 Tạo Volume Group](#214-tạo-volume-group)
    - [2.1.5 Tạo Logical Volume](#215-tạo-logical-volume)
    - [2.1.6 Định dạng Logical Volume](#216-định-dạng-logical-volume)
    - [2.1.7 Mount và sử dụng](#217-mount-và-sử-dụng)
  - [2.2 Các thao tác trên LVM](#22-các-thao-tác-trên-lvm)
    - [2.2.1 Thay đổi dung lượng Logical Volume trên LVM](#221-thay-đổi-dung-lượng-logical-volume-trên-lvm)
    - [2.2.2 Thay đổi dung lượng Volume Group trên LVM](#222-thay-đổi-dung-lượng-volume-group-trên-lvm)
    - [2.2.3 Xóa Logical Volume, Volume Group, Physical Volume](#223-xóa-logical-volume-volume-group-physical-volume)
  - [3.  Snapshot của Logical Volume](#3--snapshot-của-logical-volume)
    - [1. Giới thiệu](#1-giới-thiệu)
    - [2.Tạo snapshot LVM](#2tạo-snapshot-lvm)
- [4. Kích hoạt file Log cho LVM](#4-kích-hoạt-file-log-cho-lvm)
- [5 Kiểm chứng 2 kiểu lưu trữ Linear và Striped](#5-kiểm-chứng-2-kiểu-lưu-trữ-linear-và-striped)
  - [1.Chuẩn bị:](#1chuẩn-bị)
  - [2. Thực hiện](#2-thực-hiện)

# 1. Tìm hiểu tổng quan LVM

## 1.1. LVM là gì?
- Logical Volume Manager (LVM) Là phương pháp cho phép ấn định không gian đĩa cứng thành những logical Volume khiến cho việc thay đổi kích thước trở nên dễ dàng hơn (so với partition).

- Với kỹ thuật Logical Volume Manager (LVM) bạn có thể thay đổi kích thước mà không cần phải sửa lại table của OS.

- Những trường hợp bạn đã sử dụng hết phần bộ nhớ còn trống của partition và muốn mở rộng dung lượng của nó.

## 1.2 LVM dùng để làm gì?
- LVM là kỹ thuật quản lý việc thay đổi kích thước lưu trữ của ổ cứng.

- Không để hệ thống bị gián đoạn hoạt động
- Không làm hỏng dịch vụ.
- Có thể kết hợp Hot Swapping (thao tác thay thế nóng các thành phần bên trong máy tính).


## 1.3. Các thành phần của LVM

![](image/0000.png)


- Hard drives - Drives: Thiết bị lưu trữ dữ liệu, ví dụ như là các loại ổ cứng HDD, SSD, USB...

- Partition: Partitions là các phân vùng của Hard drives, mỗi Hard drives thường có 4 partition, trong đó partition bao gồm 2 loại là primary partition và extended partition

    - Primary partition:

        - Phân vùng chính, có thể khởi động

        - Mỗi đĩa cứng có thể có tối đa 4 phân vùng này

    - Extended partition: Phân vùng mở rộng, có thể tạo những vùng luận lý (vùng logic).

- Physical Volumes:

    - Là những đại vật lý hoặc phân vùng đĩa của bạn chẳng hạn như /dev/hda hoặc /dev/sdb1. Khi muốn sử dụng chỉ cần mount vào là được

    - Là một cách gọi khác của partition trong kỹ thuật LVM, nó là những thành phần cơ bản được sử dụng bởi LVM. Một Physical Volume không thể mở rộng ra ngoài phạm vi một ổ đĩa.

    - Chúng ta có thể kết hợp nhiều Physical Volume lại thành Volume Groups để dễ dàng trong việc quản lý và phân chia.

- Volume Group: Là một nhóm bao gồm nhiều Physical Volume trên những ổ đĩa khác nhau được kết hợp lại thành một Volume Group

    - Volume Group được sử dụng để tạo ra các Logical Volume, trong đó người dùng có thể tạo, thay đổi kích thước, lưu trữ, gỡ bỏ và sử dụng.

    - Một điểm cần lưu ý là boot loader không thể đọc /boot khi nó nằm trên Logical Volume Group. Do đó không thể sử dụng kỹ thuật LVM với /boot mount point

- Logical Volume: Volume Group được chia nhỏ thành nhiều Logical Volume, mỗi Logical Volume có ý nghĩa tương tự như partition. Nó được dùng cho các mount point và được format với những định dạng khác nhau như ext2, ext3, ext4, …


  - Khi dung lượng của Logical Volume được sử dụng hết ta có thể đưa thêm ổ đĩa mới bổ sung cho Volume Group và do đó tăng được dung lượng của Logical Volume.(Giống như việc gộp ổ đĩa trống vào 1 ổ đĩa khác )

- File Systems:

    - Tổ chức và kiểm soát các tập tin.
    - Được lưu trữ trên ổ đĩa cho phép truy cập nhanh chóng và an toàn.

    - Sắp xếp dữ liệu trên đĩa cứng máy tính.

    - Quản lý vị trí vật lý của mọi thành phần dữ liệu.
# 2. Lab LVM

## 2.1 Tạo LVM

 ### 2.1.1 Chuẩn bị
Tạo máy ảo trên vmware Workstation cài hệ điều hành CentOS 7
Add thêm một số ổ cứng vào máy ảo
![](image/000.png)

Kiểm tra ổ đĩa trên Linux bằng lệnh: lsblk

Kết quả:

```
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   39G  0 part
  ├─centos-root 253:0    0   37G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    3G  0 disk
sdc               8:32   0    3G  0 disk
sdd               8:48   0    3G  0 disk
sr0              11:0    1 1024M  0 rom
```
Ảnh minh hoạ:
![](image/001.png)


### 2.1.2 Tạo Partition
– Tạo các partition cho các ổ mới , bắt đầu từ sdb với lệnh : fdisk /dev/sdb
![](image/002.png)


Trong đó:

- Chọn ``n`` để bắt đầu tạo partition
- Chọn ``p`` để tạo partition primary
- Chọn ``1`` để tạo partition primary 1
- Tại First sector (2048-20971519, default 2048) để mặc định
- Tại Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519) Nhập +1G để partition tạo ra có dung lượng 1G
- Chọn w để lưu lại và thoát.

Tiếp theo bạn thay đổi định dạng của partition vừa mới tạo thành LVM

![](image/003.png)



- Chọn t để thay đổi định dạng partition
- Chọn 8e để đổi thành LVM
- 
Tương tự, bạn tạo thêm các partition primary từ sdb và tạo các partition primary từ sdc bằng lệnh fdisk /dev/sdc



### 2.1.3 Tạo Physical Volume
Tạo các Physical Volume là /dev/sdb1 và /dev/sdc1 bằng các lệnh sau:

```
pvcreate /dev/sdb1
pvcreate /dev/sdc1
```
![](image/005.png)

![](image/006.png)


Kiểm tra bằng lệnh ``pvs`` hoặc ``pvdisplay`` xem các physical volume đã được tạo chưa :

### 2.1.4 Tạo Volume Group
Tiếp theo, nhóm các Physical Volume thành 1 Volume Group bằng cách sử dụng câu lệnh sau:

``` 
vgcreate vg-tuan /dev/sdb1 /dev/sdc1
```
![](image/007.png)


Trong đó ``vg-tuan`` là tên của Volume Group

Có thể sử dụng câu lệnh sau để kiểm tra lại các Volume Group đã tạo ``vgs`` va `` vgdisplay``



Kiểm tra:
```
[root@localhost ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <39.00 GiB
  PE Size               4.00 MiB
  Total PE              9983
  Alloc PE / Size       9982 / 38.99 GiB
  Free  PE / Size       1 / 4.00 MiB
  VG UUID               5EycWR-ubHY-VMS8-NvIv-IGSC-7M6E-gvxPop

  --- Volume group ---
  VG Name               vg-tuan
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               1.99 GiB
  PE Size               4.00 MiB
  Total PE              510
  Alloc PE / Size       0 / 0
  Free  PE / Size       510 / 1.99 GiB
  VG UUID               X2XzVq-jRof-fFCD-5S4A-veiE-9RJy-dt77BW
  ```
Hoặc
```
[root@localhost ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  centos     1   2   0 wz--n- <39.00g 4.00m
  vg-tuan   2   0   0 wz--n-   1.99g 1.99g
  ```

### 2.1.5 Tạo Logical Volume
Từ một Volume Group, Có thể tạo ra các Logical Volume bằng cách sử dụng lệnh sau:

```lvcreate -L 1G -n lv-tuan tuannguyen```

![](image/006.1.png)

Trong đó

- L: Chỉ ra dung lượng của logical volume
- n: Chỉ ra tên của logical volume
- tuannguyen là tên Logical Volume
vg-tuan1 là Volume Group vừa tạo ở bước trước
Lưu ý: có thể tạo nhiều Logical Volume từ 1 Volume Group


```

[root@localhost ~]# lvcreate -L 1G -n  vg-tuannguyen
  Logical volume "tuannguyen" created.
[root@localhost ~]# lvs
  LV       VG       Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root     centos   -wi-ao---- 36.99g
  swap     centos   -wi-ao----  2.00g
  tuannguyen vg-tuan -wi-a-----  1.00g
```

### 2.1.6 Định dạng Logical Volume
Để format các Logical Volume thành các định dạng như ext2, ext3, ext4, có thể làm như sau:

```
mkfs -t ext3 /dev/vg-tuan/tuannguyen
```
Minh hoạ:

![](image/010.png)

```
[root@localhost ~]# mkfs -t ext3 /dev/vg-tuan/tuannguyen
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
65536 inodes, 262144 blocks
13107 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```


### 2.1.7 Mount và sử dụng
Tạo ra một thư mục để mount Logical Volume đã tạo vào thư mục đó

``` mkdir Save```



Tiến hành mount logical volume lv-cong1 vào thư mục Luutru như sau:

```mount /dev/vg-tuan/tuannguyen Save ```


Kiểm tra lại dung lượng của thư mục đã được mount:

``df -h``

![](image/011.png)


Các thao tác trên LVM
## 2.2 Các thao tác trên LVM
### 2.2.1 Thay đổi dung lượng Logical Volume trên LVM

Logical Volume này thuộc Volume Group vg-tuan, để tăng kích thước, bước đầu tiên phải kiểm tra xem Volume Group còn dư dung lượng để kéo giãn Logical Volume không. Logical Volume thuộc 1 Volume Group nhất định, Volume Group đã cấp phát hết thì Logical Volume cũng không thể tăng dung lượng được. Để kiểm tra, ta dùng lệnh sau:

`` vgdisplay``

![](image/012.png)



Để tăng kích thước Logical Volume ta sử dụng câu lệnh sau:

```lvextend -L +50M /dev/vg-demo1/lv-demo1```

![](image/013.png)

Với -L là tùy chọn để tăng kích thước

Kiểm tra lại bằng cách dùng lệnh ``lvs``

![](image/014.png)

Sau khi tăng kích thước cho Logical Volume thì Logical Volume đã được tăng nhưng file system trên volume này vẫn chưa thay đổi, bạn phải sử dụng lệnh sau để thay đổi:

![](image/015.png)

Để giảm kích thước của Logical Volume, trước hết các bạn phải umount Logical Volume mà mình muốn giảm

```umount /dev/vg-demo1/lv-demo1```

![](image/016.png)

Tiến hành giảm kích thước của Logical Volume

```
lvreduce -L 20M /dev/vg-demo1/lv-demo1
```
![](image/016.png)

Sau đó tiến hành format lại Logical Volume

```
mkfs.ext4 /dev/vg-tuan/tuannguyen
```
![](image/017.png)

### 2.2.2 Thay đổi dung lượng Volume Group trên LVM

thêm 1 partition vào Volume Group như sau:

 ```vgextend /dev/vg-tuan /dev/sdb2```

 ![](image/018.png)

 Chúng ta có thể cắt 1 Physical Volume ra khỏi Volume Group như sau:

```vgreduce /dev/vg-tuan /dev/sdb2```

 ![](image/020.png)

 ### 2.2.3 Xóa Logical Volume, Volume Group, Physical Volume

 Xóa Logical Volumes

Trước tiên ta phải Umount Logical Volume

```umount /dev/vg-tuan/tuannguyen```

Sau đó tiến hành xóa Logical Volume bằng câu lệnh sau:

```lvremove /dev/vg-demo1/lv-tuan```

![](image/021.png)

Xóa Volume Group

Trước khi xóa Volume Group, chúng ta phải xóa Logical Volume

Xóa Volume Group bằng cách sử dụng lệnh sau:

``` vgremove /dev/vg-tuan ```

![](image/022.png)

Xóa Physical Volume

Cuối cùng là xóa Physical Volume:

``` pvremove /dev/sdb3```


![](image/023.png)


## 3.  Snapshot của Logical Volume
### 1. Giới thiệu
   
LVM Snapshots là bản sao thời gian trỏ chuột hiệu quả về không gian của lvm volume. Nó chỉ hoạt động với lvm và chỉ sử dụng dung lượng khi thực hiện các thay đổi đối với nguồn Logical volume thành Snapshot volume. Nếu source volume có những thay đổi lớn được thực hiện đối với tổng 1GB, những thay đổi tương tự sẽ được thực hiện đối với snapshot volume. Tốt nhất là luôn luôn có một kích thước nhỏ thay đổi để tiết kiệm không gian.

![](image/028.png)

  ### 2.Tạo snapshot LVM

```lvcreate -L <số dung lượng snapshot> -s -n <tên snapshot> /dev/<tên vg>/<tên lv>```


Trong đó ý nghĩa các tuỳ chọn là:

- s: Tạo snapshot
- n: Tên cho snapshot


Kiểm tra lại snapshot đã được tạo

![](image/026.png)


Để biết thêm thông tin chi tiết về snapshot dùng lệnh

```
[root@localhost ~]# lvdisplay /dev/vg-tuan/tuan_snapshot
  --- Logical volume ---
  LV Path                /dev/vg-tuan/tuan_snapshot
  LV Name                tuan_snapshot
  VG Name                
  vg-tuan
  LV UUID                bUOx8A-cEb0-68MW-rPRZ-Y2et-c6dn-pJNlZp
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2021-11-14 15:07:33 +0700
  LV snapshot status     active destination for tuannguyen
  LV Status              available
  #open                 0
  LV Size                10.00 GiB
  Current LE             2560
  COW-table size         5.00 GiB
  COW-table LE           1280
  Allocated to snapshot  0.01%
  Snapshot chunk size    4.00 KiB
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:5
```
Trong đó ý nghĩa các trường của lệnh lvdisplay như sau:

- LV Name: Tên của Snapshot Logical Volume.

- VG Name.: Tên Volume group đang được sử dụng.

- LV Write Access: Snapshot volume ở chế độ đọc và ghi.

- LV Creation host, time: Thời gian khi snapshot được tạo. Nó rất quan trọng vì snapshot sẽ tìm mọi thay đổi sau thời gian này.

- LV snapshot status: Snapshot này thuộc vào logical volume hieu.

- LV Size: Kích thước của Source volume mà bạn đã snapshot.

- COW-table size: Kích thước bảng Cow.

- Snapshot chunk size: Cung cấp kích thước của chunk cho snapshot.

Nếu chúng ta có Source volume có kích thước là 10GB thì chúng ta cũng nên tạo snapshot có dung lượng 10GB để đủ không gian chứa các thay đổi của Source volume. Chú ý: Dung lượng Snapshot tăng lên đúng bằng dung lượng tạo mới trên LV. Không thể tạo Snapshot mới ghi đè lên Snapshot cũ. Trường hợp bạn có 2 Snapshot cho cùng 1 ổ LV thì dữ liệu mới cũng được ghi cả vào 2 ổ Snapshot

Tăng dung lượng snapshot trong lvm
Chúng ta cần mở rộng kích thước của snapshot, chúng ta có thể thực hiện bằng cách sử dụng:


```
[root@localhost ~]# lvextend -L +5GB /dev/vg-tuan/tuan_snapshot
  Size of logical volume vg-tuan/tuan_snapshot changed from 1.00 GiB (1280 extents) to 5.00 GiB (2560 extents).
  Logical volume vg-tuan/tuan_snapshot successfully resized.
[root@localhost ~]# lvdisplay /dev/vg-tuan/tuan_snapshot
  --- Logical volume ---
  LV Path                /dev/vg-tuan/tuan_snapshot
  LV Name                tuan_snapshot
  VG Name                
  vg-tuan
  LV UUID                bUOx8A-cEb0-68MW-rPRZ-Y2et-c6dn-pJNlZp
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2021-11-14 15:07:33 +0700
  LV snapshot status     active destination for tuannguyen
  LV Status              available
  open                 0
  LV Size                05.00 GiB
  Current LE             2560
  COW-table size         05.00 GiB
  COW-table LE           2560
  Allocated to snapshot  9.69%
  Snapshot chunk size    4.00 KiB
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:5
```


Mở rộng tự động
Vào file /etc/lvm/lvm.conf và chỉnh sửa

![](image/027.png)


Dòng 1: Khi dung lượng đạt tới 100% Dòng 2: Kích thước volume sẽ tăng 20%


# 4. Kích hoạt file Log cho LVM

Bước 1: Chỉnh sửa tệp lvm.conf, tìm phần log

[root@localhost log]# vi /etc/lvm/lvm.conf

![](image/030.png)

Bước 2: Thay đổi level = 0 thành giá trị từ 7

giá trị 7 là là mức hiện chi tiết nhất

![](image/031.png)

Bước 3: Lưu các thay đổi và thoát. THông báo nhật ký sẽ được lưu trong /var/log/messages

![](image/032.png)


# 5 Kiểm chứng 2 kiểu lưu trữ Linear và Striped
## 1.Chuẩn bị:
- 1 VM bao gồm 3 disk: sda,adb,sdc.
- Cài các gói: wget, bwm-ng
## 2. Thực hiện
- Bước 1: Đầu tiên ta cài gói wget cho VM bằng lệnh sau
    ```
    yum install wget
    ```
- Bước 2 : Sau khi download xong gói wget ta sử dụng wget để cài lệnh giám sát quá trình đọc ghi ổ đĩa bwn-ng

```# wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/b/bwm-ng-0.6.1-2.el7.x86_64.rpm```

- Bước 3 : Các gói cần thiết đã cài sau đó ta đến bước tạo ra các phân vùng
```
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   40G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   39G  0 part
  ├─centos-root 253:0    0   37G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0   5G  0 disk
├─sdb1            8:17   0    1G  0 part
├─sdb2            8:18   0    1G  0 part
sdc               8:32   0   5G  0 disk
├─sdc1            8:33   0    1G  0 part
└─sdc2            8:34   0    1G  0 part
sr0              11:0    1 1024M  0 rom
```
- Bước 4: Tiếp theo, tạo ra group-volume. Sau đó kiểm tra lại bằng lệnh vgs
```
[root@localhost ~]# vgcreate group1 /dev/sdb1 /dev/sdc1
  Physical volume "/dev/sdb1" successfully created.
  Physical volume "/dev/sdc1" successfully created.
  Volume group "group1" successfully created
[root@localhost ~]# vgcreate group2 /dev/sdb2 /dev/sdc2
  Physical volume "/dev/sdb2" successfully created.
  Physical volume "/dev/sdc2" successfully created.
  Volume group "group2" successfully created
[root@localhost ~]#
[root@localhost ~]# vgs
  VG     #PV #LV #SN Attr   VSize   VFree
  centos   1   2   0 wz--n- <39.00g  4.00m
  group1   2   0   0 wz--n-   1.99g  1.99g
  group2   2   0   0 wz--n-   1.99g  1.99g
```
- Bước 5: Tạo ra một logical với kiểu lưu trữ là linear và một logical với kiểu lưu trữ là striped. 
  - Tạo ra logical có tên là linear-LV với  group1 theo cú pháp:
    ```
    lvcreate --extents (số %)FREE --name (tên logical)
    ```
  - và striped-lv với group2 theo cú pháp:
```
lvcreate --extents N%FREE --stripes (số physical) --stripesize (số dung lượng) --name (tên logical) (tên group )
```
  - Minh hoạ:
  
![](image/033.png)

```
[root@localhost ~]# lvcreate --extents 100%FREE --name linear-LV group1
  Logical volume "linear-LV" created.
[root@localhost ~]# lvcreate --extents 100%FREE --stripes 2 --stripesize 64 --name striped-lv group2
  Logical volume "striped-lv" created.
[root@localhost ~]#
[root@localhost ~]# lvs
  LV         VG     Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root       centos -wi-ao---- 36.99g
  swap       centos -wi-ao----  2.00g
  linear-LV  group1 -wi-a-----  1.99g
  striped-lv group2 -wi-a-----  1.99g
```

- Bước 6: Tạo định dạng cho logical để có thể mount vào thư mục

```
[root@localhost ~]# mkfs -t ext4 /dev/group1/linear-LV
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
655360 inodes, 2619392 blocks
130969 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2151677952
80 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

[root@localhost ~]# mkfs -t ext4 /dev/group2/striped-lv
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=16 blocks, Stripe width=32 blocks
655360 inodes, 2619392 blocks
130969 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2151677952
80 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

- Bước 7: Mount nó lại vào cây thư mục root. Và để kiểm tra lại xem logical đã được mount hay chưa, sử dụng lệnh df -h


```
[root@localhost ~]# mkdir linear
[root@localhost ~]# mkdir striped
[root@localhost ~]#
[root@localhost ~]# mount /dev/group1/linear-LV linear
[root@localhost ~]# mount /dev/group2/striped-lv striped
[root@localhost ~]# df -h
Filesystem                     Size  Used Avail Use% Mounted on
devtmpfs                       476M     0  476M   0% /dev
tmpfs                          487M     0  487M   0% /dev/shm
tmpfs                          487M   14M  473M   3% /run
tmpfs                          487M     0  487M   0% /sys/fs/cgroup
/dev/sda3                       38G  2.1G   36G   6% /
/dev/sda1                     1014M  155M  860M  16% /boot
tmpfs                           98M     0   98M   0% /run/user/0
/dev/mapper/group1-linear--LV  2.0G  6.0M  1.9G   1% /root/linear
```



Tài liệu tham khảo

https://bachkhoa-aptech.edu.vn/gioi-thieu-ve-logical-volume-manager/279.html
