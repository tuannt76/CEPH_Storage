# Tìm hiểu Raid 0, 1, 5


- [Tìm hiểu Raid 0, 1, 5](#tìm-hiểu-raid-0-1-5)
- [I. Tổng quan về RAID](#i-tổng-quan-về-raid)
  - [1. Khái niệm](#1-khái-niệm)
  - [2 Kỹ thuật lưu trữ](#2-kỹ-thuật-lưu-trữ)
  - [3. Phân loại](#3-phân-loại)
  - [4.Cách hoạt động](#4cách-hoạt-động)
    - [4.1 Raid 0](#41-raid-0)
    - [4.2 Raid 1](#42-raid-1)
    - [4.3 Raid 5](#43-raid-5)
  - [5. Triển khai RAID](#5-triển-khai-raid)
    - [5.1 Cài đặt mdadm](#51-cài-đặt-mdadm)
    - [5.2. Hướng dẫn tạo RAID0](#52-hướng-dẫn-tạo-raid0)
    - [5.3. Hướng dẫn tạo RAID1](#53-hướng-dẫn-tạo-raid1)
    - [5.4 Hướng dẫn tạo RAID5](#54-hướng-dẫn-tạo-raid5)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

# I. Tổng quan về RAID
## 1. Khái niệm
- RAID (Redundant Array of Independent Disks) 
- là hình thức ghép nhiều ổ đĩa cứng vật lý thành một hệ thống ổ đĩa cứng có chức gia tăng tốc độ đọc/ghi dữ liệu hoặc nhằm tăng thêm sự an toàn của dữ liệu chứa trên hệ thống đĩa hoặc kết hợp cả hai yếu tố trên.
Lợi ích:
- An toàn vì có cơ chế dự phòng

- Hiệu quả cao, sử dụng bộ nhớ dung lượng lớn

- Quản lý dữ liệu người dùng dễ dàng, phân chia thư mục chi tiết

Sự dự phòng là nhân tố quan trọng nhất trong quá trình phát triển RAID cho môi trường máy chủ. Dự phòng cho phép sao lưu dữ liệu bộ nhớ khi gặp sự cố. Nếu một ổ cứng trong dãy bị trục trặc thì nó có thể hoán đổi sang ổ cứng khác mà không cần tắt cả hệ thống hoặc có thể sử dụng ổ cứng dự phòng. Phương pháp dự phòng phụ thuộc vào phiên bản nào được sử dụng.

Khi áp dụng các phiên bản RAID mạnh bạn có thể thấy rõ hiệu quả tăng cao của nó. Hiệu quả cũng tùy thuộc vào số lượng ổ cứng được liên kết với nhau và các mạch điều khiển.

Tất cả các nhà quản lý những tập đoàn CNTT đều muốn giảm giá thành. Khi ra đời, giá thành là một vấn đề chủ chốt. Mục tiêu của các mảng RAID Array là cung cấp bộ nhớ tốt hơn cho hệ thống so với việc sử dụng riêng biệt các ổ đĩa có dung lượng lớn
## 2 Kỹ thuật lưu trữ
- **Striping** (Phân chia dải): Tách luồng dữ liệu thành các khối có kích thước nhất định (được gọi là kích thước khối) sau đó viết từng khối này qua từng RAID. Cách lưu trữ dữ liệu này ảnh hưởng đến hiệu suất.
- **Mirroring** (mirroring): Là một kỹ thuật lưu trữ trong đó các bản sao dữ liệu giống hệt nhau được lưu trữ trên các thành viên RAID cùng một lúc. Loại vị trí dữ liệu này ảnh hưởng đến khả năng chịu lỗi cũng như hiệu suất.
- **Parity** là một kỹ thuật lưu trữ được sử dụng các phương pháp phân loại và tổng kiểm tra. Trong kỹ thuật chẵn lẻ, một hàm chẵn lẻ nhất định được tính cho các khối dữ liệu. Nếu một ổ đĩa bị lỗi, khối bị thiếu được tính toán lại từ tổng kiểm tra, cung cấp khả năng chịu lỗi RAID.
## 3. Phân loại
- RAID-0: Sử dụng nhiều hơn hai ổ đĩa cứng để cấu hình. RAID-0 phân chia dữ liệu thành các đoạn và lưu trữ tại các ổ cứng nhằm tăng tốc độ đọc và ghi dữ liệu. Nếu một ổ cứng bị hỏng thì dữ liệu cũng bị mất.
- RAID-1: Sử dụng nhiều hơn hai ổ đĩa cứng để cấu hình. Dữ liệu được ghi giống nhau vào hai ổ cứng nhằm đảm bảo tính sẵn sàng cho dữ liệu. Nếu một trong hai ổ cứng bị hỏng thì dữ liệu vẫn còn.
- RAID-5: Sử dụng ba hoặc năm ổ đĩa cứng để cấu hình. Dữ liệu và bản sao lưu được chia đều lên tất cả các ổ cứng.
## 4.Cách hoạt động
### 4.1 Raid 0
![](./image/../../2.LINUX/LinuxBasic/image/Raid_1.png)

Đây là dạng RAID được người dùng ưa thích do khả năng nâng cao hiệu suất trao đổi dữ liệu của đĩa cứng.
RAID 0 cần ít nhất 2 ổ đĩa. Tổng quát ta có n đĩa (n>=2) và các đĩa cùng loại.
Dữ liệu sẽ được chia ra nhiều phần bằng nhau để lưu trên từng đĩa. Như vậy mỗi đĩa sẽ chứa 1/n dữ liệu.
Tổng dung lượng = dung lượng nhỏ nhất x Tổng số đĩa

- Ưu điểm: Tăng tốc độ đọc/ghi ổ đĩa. (Lý thuyết thì tốc độ tăng n lần)
- Nhược điểm: Tính an toàn thấp (Vì nếu 1 ổ đĩa hư thì tất cả dữ liệu trên đĩa còn lại sẽ không sử dụng được)
- Ứng dụng: Lý tưởng cho việc lưu trữ dữ liệu không quan trọng cần được đọc ghi tốc độ cao (Thường dùng trong các máy trạm). ví dụ như chỉnh sửa ảnh hoặc video.
### 4.2 Raid 1
![](./image/../../2.LINUX/LinuxBasic/image/Raid_2.png)

RAID1 (Mirroring) là sự sao chép dữ liệu vào hai hoặc nhiều đĩa(Dữ liệu giống hệt nhau)
Đây là dạng RAID cơ bản nhất có khả năng đảm bảo an toàn dữ liệu. 
Cũng giống như RAID 0, RAID 1 đòi hỏi ít nhất 2 đĩa cứng để làm việc.
Trong trường hợp 1 ổ bị trục trặc, ổ còn lại sẽ tiếp tục hoạt động bình thường. Có thể thay đổi đĩa hỏng mà không lo lắng vấn đề mất thông tin
Đối với RAID 1, hiệu năng không phải là yếu tố hàng đầu. Tuy nhiên đối với hệ thống quản lý nhiều thông tin quan trọng thì RAID 1 là điều cần thiết.
Tổng dung lượng = Dung lượng của 1 ổ đơn.
RAID1 yêu cầu tối thiểu hai đĩa vật lý, vì dữ liệu được ghi đồng thời đến hai nơi. Nếu một đĩa bị lỗi, đĩa kia có thể truy xuất dữ liệu.
- Ưu điểm: An toàn về dữ liệu, khả năng hoạt động liên tục cao
- Nhược điểm: Hiệu suất không cao, chi phí cao
- Đối tượng sử dụng: Các dịch vụ lưu trữ, các website vừa và nhỏ (Chỉ cần dung lượng 1 đĩa là đủ)không yêu cầu quá cao về tốc độ đọc ghi của ổ cứng.
### 4.3 Raid 5
![](./image/../../2.LINUX/LinuxBasic/image/Raid_3.png)

RAID5 (Distributed Parity) được sử dụng ở cấp doanh nghiệp. RAID5 hoạt động theo phương pháp parity. Thông tin chẵn lẻ sẽ được sử dụng để xây dựng lại dữ liệu. Nó xây dựng lại từ thông tin còn lại trên các ổ đĩa tốt còn lại. Điều này sẽ bảo vệ dữ liệu của chúng ta khi ổ đĩa bị lỗi. Dử liệu trên RAID5 có thể tồn tại sau một lỗi ổ đĩa duy nhất, nếu các ổ đĩa bị lỗi nhiều hơn 1 sẽ gây mất dữ liệu.
Khi một ổ đĩa trong mảng bị hỏng. Ổ đĩa này có thể được thay thế bởi một ổ đĩa mới mà không cần tắt NAS hoặc máy chủ, và cũng không làm gián đoạn người dùng đang truy cập NAS hoặc máy chủ đó. Đây là một giải pháp tuyệt vời cho khả năng chịu lỗi do ổ đĩa hỏng (hoặc dần dần sẽ hỏng), dữ liệu có thể được tái tạo (hay còn gọi là đồng bộ) trên ổ đĩa mới khi ổ đĩa hỏng được thay thế. Thời gian tái tạo phụ thuộc vào nhiều yếu tố như dung lượng dữ liệu cần tái tạo, tốc độ ghi của ổ đĩa mới, mức độ ưu tiên tái tạo (rebuild priority)... có thể mất nhiều tiếng hoặc thậm chí nhiều ngày mới hoàn tất.

Tổng dung lượng= Dung lượng của 1 ổ cứng x (Số lượng các ổ cứng tham gia -1)

Một số ưu điểm của RAID5:
- Ưu điểm: Hiệu suất tốt, an toàn dữ liệu (Khả năng chịu lỗi tốt), khả năng hoạt động liên tục (Thay nóng- không cần tắt máy chủ)
- Nhược điểm: Chi phí phát sinh thêm 1 ổ so với hình thức lưu trữ thông thường.
- Đối tượng sử dụng: Tất cả những website, dịch vụ, ứng dụng có số lượng truy cập và yêu cầu tài nguyên từ nhỏ đến vừa và lớn.s

## 5. Triển khai RAID
### 5.1 Cài đặt mdadm
RAID được quản lý bằng gói `mdadm` trong hầu hết các bản phân phối Linux. Để kiểm tra xem gói `mdadm` có khả dụng trên hệ thống của chưa bằng cách chạy lệnh sau:

```[root@localhost ~]# mdadm  --version```

Nếu gói `mdadm` không có trên hệ thống của bạn, bạn có thể dễ dàng cài đặt như sau:

Đối với với bản phân phối RHEL/CentOS:
Chạy lệnh bên dưới để thực hiện cài đặt gói mdadm:

```yum install mdadm```

```
[root@localhost ~]# yum -y install mdadm
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirrors.nhanhoa.com
 * extras: mirrors.nhanhoa.com
 * updates: mirrors.nhanhoa.com
base                                                                                      | 3.6 kB  00:00:00
extras                                                                                    | 2.9 kB  00:00:00
updates                                                                                   | 2.9 kB  00:00:00
(1/4): extras/7/x86_64/primary_db                                                         | 243 kB  00:00:00
(2/4): base/7/x86_64/group_gz                                                             | 153 kB  00:00:01
(3/4): base/7/x86_64/primary_db                                                           | 6.1 MB  00:00:01
(4/4): updates/7/x86_64/primary_db                                                        |  12 MB  00:00:01
...
```
### 5.2. Hướng dẫn tạo RAID0
- **Bước 1: Cập nhật hệ thống và cài đặt `mdadm` để quản lý RAID**


- **Bước 2: Kiểm tra thông tin ổ đĩa trên máy:**

Trước khi tạo RAID0, cần đảm bảo có ít nhất hai ổ đĩa cứng chạy:
```
[root@localhost ~]# fdisk -l |grep sd
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_4.png)

Kiểm tra xem các ổ cứng có sử dụng RAID nào chưa bằng lệnh mdadm cùng với tùy chọn --examine như bên dưới.
```
[root@localhost ~]# mdadm --examine /dev/sd[b-c]
```
Kết trên trả về cho chúng ta biết rằng không có RAID nào được áp dụng cho hai ổ sdb và sdc.

![](./image/../../2.LINUX/LinuxBasic/image/Raid_5.png)

- **Bước 3: Tạo phân vùng đĩa cứng**

Thực hiện tạo phân vùng trên đĩa có tên là sdb và sdc cho RAID bằng lệnh fdisk.

Tạo phân vùng trên ổ đĩa sdb.

Chạy lệnh fdisk /dev/sdb để tạo phân vùng cho sdb và thực hiện các thao tác sau:

- Nhấn n để tạo phân vùng mới.
- Sau đó chọn p cho phân vùng chính.
- Tiếp theo chọn số phân vùng là 1 .
- Nhập giá trị ban đầu, giá trị kết thúc và nhấn phím Enter.
- Tiếp theo nhấn p để in phân vùng đã được tạo.
Thực hiện các bước sau đây để tạo Linux RAID tự động trên các phân vùng:

- Nhấn L để liệt kê tất cả các loại có sẵn.
- Nhập t để chọn phân vùng.
- Nhập fd để chọn Linux RAID tự động và nhấn Enter để áp dụng.
- Sử dụng phím p để in những thay đổi.
- Cuối cùng chúng ta nhấn phím w lưu các thay đổi.


Các thao tác trên được thực hiện như sau:

```
[root@localhost ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x9995df5d.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-23068671, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-23068671, default 23068671):
Using default value 23068671
Partition 1 of type Linux and of size 11 GiB is set

Command (m for help): p

Disk /dev/sdb: 11.8 GB, 11811160064 bytes, 23068672 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x9995df5d

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    23068671    11533312   83  Linux

Command (m for help): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT
1e  Hidden W95 FAT1 80  Old Minix

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): p

Disk /dev/sdb: 11.8 GB, 11811160064 bytes, 23068672 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x9995df5d

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    23068671    11533312   fd  Linux raid autodetect

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Tương tự như vậy chúng ta tạo phân vùng cho sdc.

Sau khi tạo xong chúng ta chạy lệnh bên dưới kiểm tra:
```
[root@localhost ~]# fdisk -l | grep sd
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_6.png)

Tiếp theo chúng ta chạy lệnh bên dưới để kiểm tra xem các đĩa hiện có tham gia RAID nào không:
```
[root@localhost ~]#  mdadm --examine /dev/sd[b-c]

[root@localhost ~]#  mdadm --examine /dev/sd[b-c]1
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_7.png)

- **Bước 4: Tạo RAID0**

Chạy lệnh sau để tạo RAID0:

```
[root@localhost ~]# mdadm -C /dev/md0 -l raid0 -n 2 /dev/sd[b-c]1
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```
Trong đó:

-C: Tạo RAID mới.
-l: Level của RAID.
-n: số thiết bị RAID.
Ngoài ra lệnh tạo RAID0 sau đây cũng có ý nghĩa như lệnh chúng ta vừa chạy:
```
mdadm --create /dev/md0 --level=stripe --raid-devices=2 /dev/sd[b-c]1
```
Kiểm tra lại RAID vừa tạo bằng các cách sau:
```
[root@localhost ~]# cat /proc/mdstat
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_8.png)

Qua kết quả trên cho chúng ta thấy RAID0 đã được tạo với hai phân vùng sdb1 và sdc1. Cũng có thể kiểm tra bằng lệnh bên dưới:
```
[root@localhost ~]# mdadm -E /dev/sd[b-c]1
/dev/sdb1:
          Magic : a92b4efc
        Version : 1.2
    Feature Map : 0x0
     Array UUID : 7a861741:000a63ca:e5544305:bf71674a
           Name : localhost.localdomain:0  (local to host localhost.localdomain)
  Creation Time : Fri Nov 19 16:35:41 2021
     Raid Level : raid0
   Raid Devices : 2

 Avail Dev Size : 23048192 sectors (10.99 GiB 11.80 GB)
    Data Offset : 18432 sectors
   Super Offset : 8 sectors
   Unused Space : before=18352 sectors, after=0 sectors
          State : clean
    Device UUID : 6a1db223:6c7fd8fd:850267f8:5ed012a5

    Update Time : Fri Nov 19 16:35:41 2021
  Bad Block Log : 512 entries available at offset 8 sectors
       Checksum : 9a2a01d3 - correct
         Events : 0

     Chunk Size : 512K

   Device Role : Active device 0
   Array State : AA ('A' == active, '.' == missing, 'R' == replacing)
/dev/sdc1:
          Magic : a92b4efc
        Version : 1.2
    Feature Map : 0x0
     Array UUID : 7a861741:000a63ca:e5544305:bf71674a
           Name : localhost.localdomain:0  (local to host localhost.localdomain)
  Creation Time : Fri Nov 19 16:35:41 2021
     Raid Level : raid0
   Raid Devices : 2

 Avail Dev Size : 23048192 sectors (10.99 GiB 11.80 GB)
    Data Offset : 18432 sectors
   Super Offset : 8 sectors
   Unused Space : before=18352 sectors, after=0 sectors
          State : clean
    Device UUID : c7040a20:a6bf264e:ebaf2c10:18c18646

    Update Time : Fri Nov 19 16:35:41 2021
  Bad Block Log : 512 entries available at offset 8 sectors
       Checksum : a009c789 - correct
         Events : 0

     Chunk Size : 512K

   Device Role : Active device 1
   Array State : AA ('A' == active, '.' == missing, 'R' == replacing)
```
Hoặc có thể chạy lệnh sau để kiểm tra:
```
[root@localhost ~]# mdadm --detail /dev/md0
```
![](./image/../../2.LINUX/LinuxBasic/image/Raid_9.png)

- **Bước 5: Tạo file system (ext4) cho thiết bị RAID /dev/md0**
```
[root@localhost ~]#  mkfs.ext4 /dev/md0
```
![](./image/../../2.LINUX/LinuxBasic/image/Raid_10.png)

Tiếp theo tạo thư mục gắn kết raid0 để gắn thiết bị /dev/md0 thực hiện như bên dưới.
```
[root@localhost ~]# mkdir c-raid0
[root@localhost ~]# mount /dev/md0 c-raid0/
```
Tiếp theo, chạy lệnh df để kiểm tra xem thiết bị /dev/md0 được gắn trong thư mục /root/raid0 không.

![](./image/../../2.LINUX/LinuxBasic/image/Raid_11.png)

Thiết bị /dev/md0 gắn kết của chúng ta là tạm thời. Nếu hệ điều hành được khởi động lại, các thư mục được gắn kết này sẽ bị mất. Vì vậy, chúng ta cần phải gắn kết vĩnh viễn. Để thực hiện gắn kết vĩnh viễn phải nhập trong tệp `/etc/fstab`. Bạn có thể sử dụng trình soạn thảo vi để nhập dòng bên dưới vào:

```/dev/md0        raid0   ext4    defaults        0 0```

Thực hiện như sau:

![](./image/../../2.LINUX/LinuxBasic/image/Raid_12.png)

Lưu lại file /etc/fstab và chạy lệnh:

```
[root@localhost ~]# mount -a
[root@localhost ~]# mount -av
/                        : ignored
/boot                    : already mounted
swap                     : ignored
c-raid0                  : already mounted
```

Nếu có lỗi, không reboot server để tránh tình trạng server không thể khởi động. Kiểm tra cấu hình trong file `/etc/fstab` và chạy lại lệnh cho tới khi không có thông báo lỗi.

- **Bước 6: Lưu cấu hình RAID**

Lưu cấu hình RAID vào một tệp để giữ cấu hình để sử dụng trong tương lai.

Chúng ta sử dụng lệnh mdadm với các tùy chọn -s(quét) và -v (dài dòng) như sau:

```[root@localhost ~]# mdadm -E -s -v >> /etc/mdadm.conf```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_13.png)
Qua các bước trên ta đã tạo thành công RAID0 trên Centos7.

### 5.3. Hướng dẫn tạo RAID1

- **Bước 1: Cập nhật hệ thống và cài đặt `mdadm` để quản lý RAID**
- **Bước 2: Kiểm tra thông tin ổ đĩa trên máy:**

Trước khi tạo RAID1, chúng ta cần có ít nhất hai ổ đĩa cứng chạy lệnh sau để kiểm tra:

```
[root@localhost ~]# fdisk -l | grep sd
```

Qua lệnh kiểm tra trên chúng ta thấy có 2 đĩa cứng mới được phát hiện. Khi gói mdadm đã được cài đặt chúng ta thực hiện kiểm tra xem các ổ cứng có sử dụng RAID nào chưa bằng lệnh mdadm bên dưới.

```
[root@localhost ~]# mdadm -E /dev/sd[b-c]
mdadm: No md superblock detected on /dev/sdb.
mdadm: No md superblock detected on /dev/sdc.
```

Như kết quả trên, chưa có bất kỳ super-block nào được phát hiện, có nghĩa là không có RAID nào được xác định.

- **Bước 3: Tạo phân vùng đĩa cứng**

Tạo phân vùng đĩa sdb và sdc cho RAID bằng lệnh fdisk như bên dưới.

Tạo phân vùng trên ổ đĩa sdb.

Chạy lệnh `fdisk /dev/sdb` để tạo phân vùng cho sdb và thực hiện các thao tác sau:

- Nhấn n để tạo phân vùng mới.
- Sau đó chọn p cho phân vùng chính.
- Tiếp theo chọn số phân vùng là 1 .
- Nhập giá trị ban đầu, giá trị kết thúc và nhấn phím Enter.
- Tiếp theo nhấn p để in phân vùng đã được tạo.
Thực hiện các bước sau đây để tạo Linux RAID tự động trên các phân vùng:

- Nhấn L để liệt kê tất cả các loại có sẵn.
- Nhập t để chọn phân vùng.
- Nhập fd để chọn Linux RAID tự động và nhấn Enter để áp dụng.
- Sử dụng phím p để in những thay đổi.
- Cuối cùng chúng ta nhấn phím w lưu các thay đổi.
Các thao tác trên được thực hiện như sau:

```
[root@localhost ~]# fdisk /dev/sdb
```

Tương tự cách tạo trên chúng ta thực hiện tạo phân vùng trên sdc.

Tiếp theo chúng ta chạy lệnh bên dưới để kiểm tra xem các đĩa hiện có tham gia RAID nào không:

```
[root@localhost ~]# mdadm -E /dev/sd[b-c]
/dev/sdb:
   MBR Magic : aa55
Partition[0] :     20969472 sectors at         2048 (type fd)
/dev/sdc:
   MBR Magic : aa55
Partition[0] :     20969472 sectors at         2048 (type fd)
[root@localhost ~]# mdadm -E /dev/sd[b-c]1
mdadm: No md superblock detected on /dev/sdb1.
mdadm: No md superblock detected on /dev/sdc1.
```

Kết quả trên cho thấy không có bất kỳ RAID nào được xác định trên các phân vùng sdb1 và sdc1.

- **Bước 4: Tạo RAID1**

Tiếp theo chúng ta sẽ tạo thiết bị RAID1 có tên /dev/md0 bằng cách sử dụng lệnh sau và xác thực RAID1 đã được tạo.

```
[root@localhost ~]# mdadm -C /dev/md0 -l raid1 -n 2 /dev/sd[b-c]1
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_14.png)
Trong quá trình tạo hệ thống yêu cầu chúng ta xác nhận tạo RADI thì bấm phím y để xác nhận.

Qua kết quả trên cho chúng ta thấy RAID1 đã được tạo với hai phân vùng sdb1 và sdc1. Chúng ta có thể kiểm tra bằng lệnh bên dưới:

```
[root@localhost ~]# mdadm -E /dev/sd[b-c]1
```
![](./image/../../2.LINUX/LinuxBasic/image/Raid_15.png)

Hoặc có thể chạy lệnh sau để kiểm tra:

```
[root@localhost ~]# mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Sun May 19 13:34:48 2019
        Raid Level : raid1
        Array Size : 10475520 (9.99 GiB 10.73 GB)
     Used Dev Size : 10475520 (9.99 GiB 10.73 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Sun May 19 13:35:41 2019
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : localhost.localdomain:0  (local to host localhost.localdomain)
              UUID : 6e8d46d8:d307ecf4:13ec9c89:52955fe6
            Events : 17

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1
```

Từ 2 kết quả trên chúng ta có thể thấy RAID1 đã được tạo và sử dụng phân vùng /dev/sdb1 và /dev/sdc1 và bạn cũng có thể thấy trạng thái là đồng bộ hóa.

- **Bước 5: Tạo file system (ext4) cho thiết bị RAID /dev/md0**

```
[root@localhost ~]# mkfs.ext4 /dev/md0
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_16.png)

Tiếp theo tạo thư mục gắn kết raid1 để gắn thiết bị /dev/md0 thực hiện như bên dưới.

```
[root@localhost ~]# mkdir c-raid1
[root@localhost ~]# mount /dev/md0 c-raid1
```

Tiếp theo, chạy lệnh df để kiểm tra xem thiết bị /dev/md0 được gắn trong thư mục /root/c-raid1 không.

![](./image/../../2.LINUX/LinuxBasic/image/Raid_17.png)

```
[root@localhost ~]# cat /proc/mdstat
Personalities : [raid1]
md0 : active raid1 sdc1[1] sdb1[0]
      10475520 blocks super 1.2 [2/2] [UU]

unused devices: <none>
```

Để tự động gắn kết `/dev/md0` khi khởi động lại hệ thống chúng ta cần tạo một mục trong tệp `/etc/fstab`. Bạn có thể sử dụng trình soạn thảo vi để nhập dòng bên dưới vào:

```/dev/md0        c-raid1   ext4    defaults         0 0```

Thực hiện như sau:

Lưu lại và chạy lệnh sau để kiểm tra xem có bất kỳ lỗi nào trong mục /etc/fstab không.

```
[root@localhost ~]# mount -av
/                        : ignored
/boot                    : already mounted
swap                     : ignored
c-raid1                  : already mounted
```

Nếu có lỗi, không reboot server để tránh tình trạng server không thể khởi động. Kiểm tra cấu hình trong file /etc/fstab và chạy lại lệnh cho tới khi không có thông báo lỗi.

- Bước 6: Lưu cấu hình RAID1 theo cách thủ công vào tệp mdadm.conf bằng lệnh bên dưới.

```[root@localhost ~]#  mdadm --detail --scan --verbose >> /etc/mdadm.conf```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_18.png)

Dữ liệu của chúng ta vẫn còn không bị mất mát. Qua đây cho chúng ta biết được lợi ích của RAID1 và giúp bạn hiểu cách thức hoạt động của RAID 1(Mirror).

### 5.4 Hướng dẫn tạo RAID5
- **Bước 1: Cập nhật hệ thống và cài đặt mdadm để quản lý RAID**
- **Bước 2: Kiểm tra thông tin ổ đĩa trên máy:**

Trước khi tạo RAID5, cần đảm bảo có ít nhất ba ổ đĩa cứng chạy lệnh sau để kiểm tra:

```
[root@localhost ~]# fdisk -l | grep sd
```

Qua lệnh kiểm tra trên chúng ta thấy có 3 đĩa cứng mới được phát hiện. Khi gói mdadm đã được cài đặt chúng ta thực hiện kiểm tra xem các ổ cứng có sử dụng RAID nào chưa bằng lệnh mdadm với tùy chọn -E bên dưới.

```
[root@localhost ~]# mdadm -E /dev/sd[b-d]
mdadm: No md superblock detected on /dev/sdb.
mdadm: No md superblock detected on /dev/sdc.
mdadm: No md superblock detected on /dev/sdd
```

Như kết quả trên, chưa có bất kỳ super-block nào được phát hiện, có nghĩa là không có RAID nào được xác định.

- **Bước 3: Tạo phân vùng đĩa cứng**

Chúng ta cần phải tạo phân vùng đĩa cứng cho sdb, sdc và sdd để tạo RAID bằng lệnh fdisk.

Tạo phân vùng trên ổ đĩa sdb.

Chạy lệnh fdisk /dev/sdb để tạo phân vùng cho sdb và thực hiện các thao tác sau:

  - Nhấn n để tạo phân vùng mới.
  - Sau đó chọn p cho phân vùng chính.
  - Tiếp theo chọn số phân vùng là 1 .
  - Nhập giá trị ban đầu, giá trị kết thúc và nhấn phím Enter.
  - Tiếp theo nhấn p để in phân vùng đã được tạo.
Thực hiện các bước sau đây để tạo Linux RAID tự động trên các phân vùng:

  - Nhấn L để liệt kê tất cả các loại có sẵn.
  - Nhập t để chọn phân vùng.
  - Nhập fd để chọn Linux RAID tự động và nhấn Enter để áp dụng.
  - Sử dụng phím p để in những thay đổi.
  - Cuối cùng chúng ta nhấn phím w lưu các thay đổi.
Các thao tác trên được thực hiện như sau:

```
[root@localhost ~]# fdisk /dev/sdb
```

Tương tự cách tạo phân vùng trên chúng ta thực hiện tạo phân dùng cho sdc và sdd.

Sau khi chúng ta đã tạo xong phân vùng chúng ta cần chạy lệnh sau để kiểm tra xem các phân vùng đã được tạo chưa:

```
[root@localhost ~]# fdisk -l | grep sd
```

Tiếp theo chúng ta chạy lệnh bên dưới để kiểm tra xem các đĩa hiện có tham gia RAID nào không:

```
[root@localhost ~]# mdadm -E /dev/sd[b-d]
[root@localhost ~]# mdadm -E /dev/sd[b-d]1
```

Kết quả trên cho thấy không có bất kỳ RAID nào được xác định trên các phân vùng sdb1 và sdc1 và sdd1.

- Bước 4: Tạo RAID5

Tiếp theo chúng ta sẽ tạo thiết bị RAID5 có tên /dev/md0 bằng cách sử dụng lệnh sau và xác thực RAID5 đã được tạo.
```
[root@localhost ~]# mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1
```
or
```
[root@localhost ~]# mdadm -C /dev/md0 -l raid5 -n 3 /dev/sd[b-d]1
```

![](./image/../../2.LINUX/LinuxBasic/image/Raid_19.png)

Qua kết quả trên cho chúng ta thấy RAID5 đã được tạo với ba phân vùng sdb1, sdc1 và sdd1. Chúng ta có thể kiểm tra bằng lệnh bên dưới:

```
[root@localhost ~]# mdadm -E /dev/sd[b-d]1
/dev/sdb1:
          Magic : a92b4efc
        Version : 1.2
    Feature Map : 0x0
     Array UUID : 24605d00:23afd830:ad19e71f:cd51481b
           Name : localhost.localdomain:0  (local to host localhost.localdomain)
  Creation Time : Fri Nov 19 17:58:51 2021
     Raid Level : raid5
   Raid Devices : 3

 Avail Dev Size : 20951040 sectors (9.99 GiB 10.73 GB)
     Array Size : 20951040 KiB (19.98 GiB 21.45 GB)
    Data Offset : 18432 sectors
   Super Offset : 8 sectors
   Unused Space : before=18280 sectors, after=0 sectors
          State : clean
    Device UUID : 638dd049:9d2eba26:0b3bd9b6:32ca32b0

    Update Time : Fri Nov 19 17:59:18 2021
  Bad Block Log : 512 entries available at offset 136 sectors
       Checksum : c51f4ea8 - correct
         Events : 18

         Layout : left-symmetric
     Chunk Size : 512K

   Device Role : Active device 0
   Array State : AAA ('A' == active, '.' == missing, 'R' == replacing)
/dev/sdc1:
          Magic : a92b4efc
        Version : 1.2
    Feature Map : 0x0
     Array UUID : 24605d00:23afd830:ad19e71f:cd51481b
           Name : localhost.localdomain:0  (local to host localhost.localdomain)
  Creation Time : Fri Nov 19 17:58:51 2021
     Raid Level : raid5
   Raid Devices : 3

 Avail Dev Size : 23048192 sectors (10.99 GiB 11.80 GB)
     Array Size : 20951040 KiB (19.98 GiB 21.45 GB)
  Used Dev Size : 20951040 sectors (9.99 GiB 10.73 GB)
    Data Offset : 18432 sectors
   Super Offset : 8 sectors
   Unused Space : before=18280 sectors, after=2097152 sectors
          State : clean
    Device UUID : 30c344b2:b4c81849:a7bf2468:78f22024

    Update Time : Fri Nov 19 17:59:18 2021
  Bad Block Log : 512 entries available at offset 136 sectors
       Checksum : 754bcb6f - correct
         Events : 18

         Layout : left-symmetric
     Chunk Size : 512K

   Device Role : Active device 1
   Array State : AAA ('A' == active, '.' == missing, 'R' == replacing)
/dev/sdd1:
          Magic : a92b4efc
        Version : 1.2
    Feature Map : 0x0
     Array UUID : 24605d00:23afd830:ad19e71f:cd51481b
           Name : localhost.localdomain:0  (local to host localhost.localdomain)
  Creation Time : Fri Nov 19 17:58:51 2021
     Raid Level : raid5
   Raid Devices : 3

 Avail Dev Size : 41922560 sectors (19.99 GiB 21.46 GB)
     Array Size : 20951040 KiB (19.98 GiB 21.45 GB)
  Used Dev Size : 20951040 sectors (9.99 GiB 10.73 GB)
    Data Offset : 18432 sectors
   Super Offset : 8 sectors
   Unused Space : before=18280 sectors, after=20971520 sectors
          State : clean
    Device UUID : d35e8c58:4cabd031:ee87476f:a68f8e0d

    Update Time : Fri Nov 19 17:59:18 2021
  Bad Block Log : 512 entries available at offset 136 sectors
       Checksum : f5fbaf20 - correct
         Events : 18

         Layout : left-symmetric
     Chunk Size : 512K

   Device Role : Active device 2
   Array State : AAA ('A' == active, '.' == missing, 'R' == replacing)
```
Hoặc có thể chạy lệnh sau để kiểm tra:

```
[root@localhost ~]# mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Fri Nov 19 17:58:51 2021
        Raid Level : raid5
        Array Size : 20951040 (19.98 GiB 21.45 GB)
     Used Dev Size : 10475520 (9.99 GiB 10.73 GB)
      Raid Devices : 3
     Total Devices : 3
       Persistence : Superblock is persistent

       Update Time : Fri Nov 19 17:59:18 2021
             State : clean
    Active Devices : 3
   Working Devices : 3
    Failed Devices : 0
     Spare Devices : 0

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : localhost.localdomain:0  (local to host localhost.localdomain)
              UUID : 24605d00:23afd830:ad19e71f:cd51481b
            Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1
       3       8       49        2      active sync   /dev/sdd1

```
Từ 2 kết quả trên chúng ta có thể thấy RAID5 đã được tạo và sử dụng phân vùng /dev/sdb1, /dev/sdc1 và /dev/sdd1.

- **Bước 5: Tạo file system (ext4) cho thiết bị RAID5 /dev/md0**


```
[root@localhost ~]# mkfs.ext4 /dev/md0
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
1310720 inodes, 5237760 blocks
261888 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2153775104
160 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
Tiếp theo tạo thư mục gắn kết raid5 để gắn thiết bị /dev/md0 thực hiện như bên dưới.
```
[root@localhost ~]# mkdir c-raid5
[root@localhost ~]# mount /dev/md0 c-raid5/
```
Tiếp theo, chạy lệnh df để kiểm tra xem thiết bị /dev/md0 được gắn trong thư mục /root/raid không.
```
[root@localhost ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   37G  1.9G   36G   6% /
devtmpfs                 475M     0  475M   0% /dev
tmpfs                    487M     0  487M   0% /dev/shm
tmpfs                    487M  7.7M  479M   2% /run
tmpfs                    487M     0  487M   0% /sys/fs/cgroup
/dev/sda1               1014M  133M  882M  14% /boot
tmpfs                     98M     0   98M   0% /run/user/0
/dev/md0                  20G   45M   19G   1% /root/c-raid5
```

Để tự động gắn kết /dev/md0 khi khởi động lại hệ thống chúng ta cần tạo một mục trong tệp /etc/fstab. Bạn có thể sử dụng trình soạn thảo vi để nhập dòng bên dưới vào:

```
/dev/md0        raid5   ext4    defaults         0 0
```


Lưu lại và chạy lệnh sau để kiểm tra xem có bất kỳ lỗi nào trong mục /etc/fstab không.
```
[root@localhost ~]# mount -av
/                        : ignored
/boot                    : already mounted
swap                     : ignored
c-raid5                    : already mounted
```
Nếu có lỗi, không reboot server để tránh tình trạng server không thể khởi động. Kiểm tra cấu hình trong file /etc/fstab và chạy lại lệnh cho tới khi không có thông báo lỗi.

- Bước 6: Lưu cấu hình RAID1 theo cách thủ công vào tệp mdadm.conf bằng lệnh bên dưới.

```
[root@localhost ~]# mdadm --detail --scan --verbose >> /etc/mdadm.conf
```

# Tài liệu tham khảo
1. https://blogd.net/linux/software-raid-toan-tap-tren-linux/#4
2. https://vi.wikipedia.org/wiki/RAID#RAID_5
3. https://www.cuudulieutransang.vn/post/2014/03/27/gi%E1%BA%A3i-th%C3%ADch-c%C3%A1c-c%E1%BA%A5p-%C4%91%E1%BB%99-raid-ph%E1%BA%A7n-1
4. 