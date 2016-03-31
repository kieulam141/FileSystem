# Cơ bản về FileSystem

### 1. Cách làm việc

- Trình fsck(`file system consistency check`): *kiểm tra* tính toàn vẹn của hệ thống file, khi khởi động.
- Có lỗi: *quét lại* toàn bộ hệ thống file để cố gắng *khôi phục* lại dữ liệu.
- *Tốc độ* kiểm tra và khôi phục dữ liệu: phụ thuộc vào *dung lượng* của ổ cứng.

### 2. Journaling Filesystem

<img src="http://i.imgur.com/4Wbtddb.gif" />

#### a. Khái niệm
- Như 1 dạng `log`, bản ghi để ghi lại tất cả những `thay đổi` khi thao tác với ổ đĩa.

#### b. Hoạt động

- `File` sẽ được *ghi* vào `journaling`.
- *Đẩy* vào bên trong lớp quản lý dữ liệu.
- `Journal` sẽ ghi file đó vào phân vùng ổ cứng khi đã sẵn sàng.
- *File* sẽ được xóa bỏ khỏi journal, đẩy ngược ra bên ngoài và quá trình hoàn tất.
- `Journal` chỉ ghi lại: file `metada` và `inode`.

#### c. Ưu, nhược điểm
- Ưu: Nếu xảy ra lỗi, hdh sẽ lấy thông tin ở `journal` để biết lỗi xảy ra ở đâu.
- Nhược: tốn thêm *hiệu suất* của hdh.

### 3. Phân Loại Filesystem
#### a. Linux
##### I. Ext2
- `Không journaling`.
- Max `File` Size: 2T
- Max `Partition` Size: 32T
- Dùng cho thiết bị lưu trữ `bên ngoài` như thẻ nhớ, ổ USB.

##### II. Ext3
- `Có journaling`.
- Max `File` Size: 2T
- Max `Partition` Size: 32T
- Ưu: hoạt động *nhanh* và *ổn định*.
- Nhược: *không* hỗ trợ tính năng tạo disk `snapshot`.

##### III.Ext4
- `Có journaling` .
- Max `file` size:16 TiB.
- Max `Partition` Size: 1 EiB.
- Ưu: *giảm* bớt hiện tượng `phân mảnh`, *hỗ trợ* các file và phân vùng có dung lượng *lớn*, tốc độ *nhanh*.
- Nhược: *Không* thích hợp trên server bằng ext3.

#### b. Oracle
##### I. BtrFS
- Max `file` size: 16 EiB.
- Thiết kế riêng biệt dành cho các doanh nghiệp có *quy mô lớn*.

###### Tính năng
- Hỗ trợ tính năng `pool` trên ổ cứng.
- Tạo và lưu trữ `snapshot`.
- `Nén` dữ liệu ở mức độ cao.
- Phân bổ inode `động`.
- Có `checksum` trên data và metadata.
- Hỗ trợ `tích hợp` nhiều thiết bị.

###### Ưu điểm
- `Backup` hiệu quả.
- Chống `phân mảnh` dữ liệu *nhanh chóng*.
- Phù hợp để hoạt động với server dựa vào `hiệu suất` làm việc cao.

###### Nhược điểm
- Không hoạt động *ổn định* trên 1 số nền tảng `distro` nhất định.

##### II. Reiserfs
- Max `file` size: 8 TiB.
- Max `Partition` Size: 16 TiB.
- Có journaling.

###### Tính năng:
- Một thư mục có thể chứa đến *100.000* thư mục *con*.
- Cấp phát `động` inode.
- Không cấp phát `cố định` dung lượng theo các khối 1KB hoặc 4KB, cấp `chính xác` kích thước khối lưu trữ cần thiết.

###### Ưu:
- `Tối ưu` được dung lượng khi lưu trữ các file có kích thước *nhỏ*.
- Đạt `hiệu suất` hoạt động rất cao đối với những file nhỏ như file `log`, phù hợp với database và server email.

###### Nhược:
Không hỗ trợ *đầy đủ* hệ thống kernel của Linux

#### c.Silicon Graphics
##### I. XFS
- Max `file` size: 8 EiB.
- Max `Partition` Size: 8 EiB.
- Có Journaling.

###### Tính năng:
- `Hạn chế` được tình trạng `phân mảnh` dữ liệu.
- *Không* cho phép các snapshot *tự động* kết hợp với nhau.
- *Hỗ trợ* nhiều file dung lượng *lớn*.
- Có thể *thay đổi* `kích thước` file dữ liệu.
- *Không* thể `shrink` – chia nhỏ phân vùng XFS.

###### Ưu:
Phù hợp với việc áp dụng vào mô hình server `media` vì khả năng *truyền tải* file video rất tốt.

###### Nhược:
Hiệu suất hoạt động với các file dung lượng *nhỏ* kém.

#### d.IBM
##### I.JFS
- Max `File` Size: 4P
- Max `Partition` Size: 32P
- Có Journaling.

###### Ưu:
- Tốn *ít* tài nguyên hệ thống.
- Đạt *hiệu suất* hoạt động tốt với nhiều file dung lượng lớn và nhỏ *khác nhau*.
- Có `tốc độ` kiểm tra ổ đĩa *nhanh nhất* so với các phiên bản Ext.

###### Nhược:
Không thể *shrink*.

### 4.Các lệnh làm việc với file
#### a.du
##### I.Khái niệm
- du(`disk usage`) dùng để kiểm tra thông tin các file và thư mục sử dụng ổ đĩa.
- lệnh *du* có những tham số cho ra các kết quả khác nhau.
- lệnh *du* cũng hiển thị size của file và thư mục theo cách đệ quy

##### II.Ví dụ
- Trước tiên ta sẽ show ở thư mục đang hiện hành có những gì.

<img src="http://i.imgur.com/BnZEYCm.png" />

###### *Basic Example*
<img src="http://i.imgur.com/VKoTjQe.png" />
Sử dụng tùy chọn -a để show ra thông tin tất cả file lẫn thư mục.
### 4.Tham Khảo
- https://btrfs.wiki.kernel.org/index.php/Main_Page
- http://www.idz.vn/2015/08/tim-hieu-file-system-cua-linux.html


