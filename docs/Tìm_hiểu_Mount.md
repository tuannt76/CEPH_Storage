 Tìm hiểu Mount

- [I. Tổng quan](#i-tổng-quan)
  - [1. Khái niệm](#1-khái-niệm)
  - [2. Quá trình mount](#2-quá-trình-mount)
  - [2. Mount point](#2-mount-point)
  - [3. Unmount](#3-unmount)
  - [4. Cách sử dụng các lệnh Mount và Umount trong Linux](#4-cách-sử-dụng-các-lệnh-mount-và-umount-trong-linux)
  - [4.Vfstab](#4vfstab)
  - [5.Cú pháp](#5cú-pháp)


# I. Tổng quan
## 1. Khái niệm
- Mount là một quá trình mà trong đó hệ điều hành làm cho các tập tin và thư mục trên một thiết bị lưu trữ (như ổ cứng, CD-ROM hoặc tài nguyên chia sẻ) có thể truy cập được bởi người dùng thông qua hệ thống tệp của máy tính.

- Lệnh Linux Mount được sử dụng để gắn USB, DVD, thẻ SD và các loại thiết bị lưu trữ khác trên máy tính Linux. Linux sử dụng cấu trúc cây thư mục . Trừ khi thiết bị lưu trữ được gắn vào cấu trúc cây, người dùng không thể mở bất kỳ tệp nào trên thiết bị.

## 2. Quá trình mount 
- Quá trình mount bao gồm việc hệ điều hành được truy cập vào phương tiện lưu trữ, công nhận, đọc và xử lý cấu trúc hệ thống tệp cùng với siêu dữ liệu trên nó, sau đó, đăng ký chúng vào thành phần hệ thống tệp ảo (VFS).

Ví dụ: Chúng ta có 1 PC Linux  tuỳ ý, PC này có nhu cầu nâng cấp Hard Disk và đã có "new hard disk". Vấn đề đặt ra là chúng ta làm thế nào để sử dụng được hard disk mới ở trong hệ điều hành.
- Chia Partition: MBR - Master Boot Record (4 primary partition), GPT - GUID Partition Table (128 primary partition)
- Format Partition: Ext4
- Kết nối Partition vào "Folder do admin chỉ định".
=> Quá trình này là quá trình mount

- Vị trí đăng ký trong VFS của phương tiện mới được mount gọi là mount point. 

## 2. Mount point
Đây là điểm mà người dùng có thể truy cập tập tin, thư mục của phương tiện sau khi quá trình mount hoàn thành.

Một điểm mount là một vị trí vật lý trong phân vùng được sử dụng như hệ thống tệp gốc (root filesystem). Có nhiều loại thiết bị lưu trữ, chẳng hạn đĩa từ, từ – quang, quang, và bán dẫn. Tính đến năm 2013, đĩa từ vẫn phổ biến nhất, thông dụng như đĩa cứng hoặc ít thông dụng hơn như đĩa mềm. Trước khi chúng có thể được sử dụng để lưu trữ, tức là có thể đọc ghi thông tin, chúng phải được tổ chức và hệ điều hành phải biết về điều này. Sự tổ chức ấy gọi là hệ thống tệp. Mỗi hệ điều hành có một hệ thống tệp khác nhau cung cấp cho nó siêu dữ liệu để nó biết cách đọc ghi ra sao. Hệ điều hành sẽ đọc những siêu dữ liệu ấy khi phương tiện được mount.

## 3. Unmount
- unmount, trong đó, hệ điều hành huỷ tất cả quyền truy cập tập tin, thư mục của người dùng tại điểm mount, ghi tiếp những dữ liệu người dùng đang trong hàng đợi vào thiết bị, làm mới siêu dữ liệu hệ thống tệp, sau đó, tự huỷ quyền truy cập thiết bị và làm cho thiết bị có thể tháo ra an toàn.

Bình thường, khi tắt máy tính, mỗi thiết bị lưu trữ sẽ trải qua quá trình unmount để đảm bảo rằng tất cả các dữ liệu trong hàng đợi được ghi và để duy trì tính toàn vẹn của cấu trúc hệ thống tệp trên các phương tiện.

## 4. Cách sử dụng các lệnh Mount và Umount trong Linux

Ví dụ sau minh họa cách sử dụng điển hình của lệnh Mount để gắn thư mục tệp của thiết bị vào cây thư mục tệp của hệ thống Linux . Các thiết bị lưu trữ bên ngoài thường được gắn trong các thư mục con của thư mục "/ mnt", nhưng chúng có thể được gắn theo mặc định trong bất kỳ thư mục nào khác được tạo bởi người dùng. Trong ví dụ này, một đĩa CD đã được đưa vào ổ đĩa CD của máy tính. Để xem các tập tin trên đĩa CD, mở một cửa sổ dòng lệnh trong Linux và nhập:

mount / dev / cdrom / mnt / cdrom
Lệnh này kết nối thiết bị "/ dev / cdrom" (ổ CD ROM) vào thư mục "/ mnt / cdrom" để bạn có thể truy cập các tệp và thư mục trên đĩa CD ROM trong thư mục "/ mnt / cdrom". Thư mục "/ mnt / cdrom" được gọi là điểm gắn kết, và nó phải tồn tại khi lệnh này được thực thi. Điểm gắn kết trở thành thư mục gốc của hệ thống tệp của thiết bị.

umount / mnt / cdrom
Lệnh này sẽ ngắt kết nối ổ đĩa CD ROM. Sau khi lệnh này được thực thi, các tệp và thư mục trên CD ROM có thể truy cập được lâu hơn từ cây thư mục của hệ thống Linux.

umount / dev / cdrom
Điều này có tác dụng tương tự như lệnh trước đó - nó ngắt kết nối CD ROM.


**Kết luận**

- mount được sử dụng để gắn kết hệ thống tệp được tìm thấy trên thiết bị với cấu trúc cây lớn ( hệ thống tệp Linux ) có gốc tại ' / '.
- Ngược lại, lệnh umount có thể được sử dụng để tách các thiết bị từ Tree.



## 4.Vfstab

Trong nhiều trường hợp, những hệ thống tệp không phải gốc vẫn cần sẵn sàng ngay khi hệ điều hành khởi động. Tất cả các hệ thống Unix-like đều cung cấp tiện ích để làm điều này. Quản trị viên hệ thống xác định những hệ thống tệp đó trong tập tin cấu hình fstab (vfstab trong Solaris), tập tin này cũng kèm theo các tuỳ chọn và điểm mount. Trong một số trường hợp khác, có những hệ thống tệp nhất định không cần mount khi khởi động dù có cần sử dụng sau đó hay không. Vài tiện ích của các hệ thống Unix-like cho phép mount những hệ thống tệp đã định trước chỉ khi nào cần tới.

```
#vi /etc/fstab
```
- Khi mở file này ra sẽ có rất nhiều dòng, mỗi dòng có 5 cột

Ví dụ:
/dev/sdb1       /data       ext4    default     0       0
Cột 1           Cột 2       Cột 3   Cột 4       Cột 5   Cột 6


- Cột 1: device hoặc partition
- Cột 2: mount point
- Cột 3: Loại file system. Ví dụ: ext2, ext3, ext4,...
- Cột 4: Mount option: auto, no-auto, user, no-user, ro, rw... Option mặc định là "default" bao gồm các tính năng: rw, suid, dev, exec, auto, nouser. (trường hợp nhiều thì dùng dấu phẩy để ngăn cách)
- Cột 5: tính năng DUMP dùng để backup. Mặc định là 0 trong hầu hết các trường hợp (nghĩa là không cần dump, nếu để 1 nó sẽ tạo ra file back up cho mình).
- Cột 6: Sử dụng fsck (filesystem check) để check ổ đĩa có lỗi hay không. Để 0 để không check file system tại phân vùng này.

Thông tin mount của mỗi file hệ thống sẽ lần lược được viết từng dòng vào file fstab. 
Mỗi field của dòng sẽ được cách bởi space, chia nhau ra bởi TAB.
Thêm vào đó, chương trình dùng để đọc file fstab chẳng hạn như fsck(*) mount, unmount thì sẽ đọc từ đầu xuống theo thứ tự từ top nên thứ tự của record (file hệ thống) được ghi lại bên trong fstab thì khá quan trọng.
Note:
- rw: read/write mode
- suid enable SUID
- dev: cho phép sử dụng các ký tự đặc biệt trên file hệ thống
- exec cho phép thực thi trên binary
- auto nếu option -a được chỉ định, ổ cứng sẽ auto mount
- nouser người dùng thông thường (không phải super user) thì không được phép thực thi lệnh mount
- async đối với file hệ thông thì mọi I/O sẽ được tiến hành không đồng bộ
- ro mount với readonly
- nosuid SUID cũng như SGID bị disable
- nodev các ký tự, ký tự đặc biệt, device block, special device…sẽ không sử dụng được
- noexec không được phép trực tiếp execute binary
- noauto nếu option -a được chỉ định, ổ cứng sẽ không auto mount
- user cho phép general user được quyền mount ổ cứng
- users cho phép toàn bộ user đều có thể mount/unmount ổ cứng
## 5.Cú pháp

```mount [OPTION...] DEVICE_NAME DIRECTORY```