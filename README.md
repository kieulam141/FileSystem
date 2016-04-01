# Cơ bản về FileSystem
## Mục lục
[1. Cách làm việc] (#CLV)

[2. Journaling Filesystem] (#2)
- [a. Khái niệm] (#2a)
- [b. Hoạt động] (#2b)
- [c. Ưu, nhược điểm] (#2c)

[3. Phân Loại Filesystem] (#3)
- [a. Linux] (#3a)
- [b. Oracle] (#3b)
- [c. Silicon Graphics] (#3c)
- [d. IBM] (#3d)

[4. Các lệnh làm việc với file] (#4)
- [a. du] (#4a)
- [b. df] (#4b)

[5. Tham Khảo] (#5)

<a name="CLV"></a>
### 1. Cách làm việc

- Trình fsck(`file system consistency check`): *kiểm tra* tính toàn vẹn của hệ thống file, khi khởi động.
- Có lỗi: *quét lại* toàn bộ hệ thống file để cố gắng *khôi phục* lại dữ liệu.
- *Tốc độ* kiểm tra và khôi phục dữ liệu: phụ thuộc vào *dung lượng* của ổ cứng.

<a name="2"></a>
### 2. Journaling Filesystem

<img src="http://i.imgur.com/4Wbtddb.gif" />

<a name="2a"></a>
#### a. Khái niệm
- Như 1 dạng `log`, bản ghi để ghi lại tất cả những `thay đổi` khi thao tác với ổ đĩa.

<a name="2b"></a>
#### b. Hoạt động
- `File` sẽ được *ghi* vào `journaling`.
- *Đẩy* vào bên trong lớp quản lý dữ liệu.
- `Journal` sẽ ghi file đó vào phân vùng ổ cứng khi đã sẵn sàng.
- *File* sẽ được xóa bỏ khỏi journal, đẩy ngược ra bên ngoài và quá trình hoàn tất.
- `Journal` chỉ ghi lại: file `metada` và `inode`.

<a name="2c"></a>
#### c. Ưu, nhược điểm
- Ưu: Nếu xảy ra lỗi, hdh sẽ lấy thông tin ở `journal` để biết lỗi xảy ra ở đâu.
- Nhược: tốn thêm *hiệu suất* của hdh.

<a name="3"></a>
### 3. Phân Loại Filesystem

<a name="3a"></a>
#### a. Linux
<a name="3aI"></a>
##### I. Ext2
- `Không journaling`.
- Max `File` Size: 2T
- Max `Partition` Size: 32T
- Dùng cho thiết bị lưu trữ `bên ngoài` như thẻ nhớ, ổ USB.

<a name="3aII"></a>
##### II. Ext3
- `Có journaling`.
- Max `File` Size: 2T
- Max `Partition` Size: 32T
- Ưu: hoạt động *nhanh* và *ổn định*.
- Nhược: *không* hỗ trợ tính năng tạo disk `snapshot`.

<a name="3aIII"></a>
##### III.Ext4
- `Có journaling` .
- Max `file` size:16 TiB.
- Max `Partition` Size: 1 EiB.
- Ưu: *giảm* bớt hiện tượng `phân mảnh`, *hỗ trợ* các file và phân vùng có dung lượng *lớn*, tốc độ *nhanh*.
- Nhược: *Không* thích hợp trên server bằng ext3.

<a name="3b"></a>
#### b. Oracle

<a name="3bI"></a>
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

<a name="3bII"></a>
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

<a name="3c"></a>
#### c.Silicon Graphics

<a name="3cI"></a>
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

<a name="3d"></a>
#### d.IBM

<a name="3bI"></a>
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

<a name="4"></a>
### 4.Các lệnh làm việc với file

<a name="4a"></a>
#### a.du
##### I.Khái niệm
- du(`disk usage`) dùng để kiểm tra thông tin các file và thư mục sử dụng ổ đĩa.
- lệnh *du* có những tham số cho ra các kết quả khác nhau.
- lệnh *du* cũng hiển thị size của file và thư mục theo cách đệ quy

##### II.Ví dụ
- Trước tiên ta sẽ show ở thư mục đang hiện hành có những gì.

<img src="http://i.imgur.com/BnZEYCm.png" />

###### *Ví dụ cơ bản*
<img src="http://i.prntscr.com/72ea0b2e246045c9a35eaf74307c6a59.png" />
Sử dụng tùy chọn "-a" để show ra thông tin tất cả file lẫn thư mục, "-h" để người đọc có thể đọc dễ dàng hơn, "-c" để show ra tổng dung lượng của thư mục hiện hành, "-b" hiện đầu ra với tham số là bytes.

###### *Loại Trừ loại file bất kì sử dụng --excute*
<img src="http://i.prntscr.com/a1535bb3e32c449a8439ca68f42a0611.png" />
Bạn có thể thấy những file đuôi .txt không được show ra.

##### *Hiển thị ra thời gian và tùy chỉnh style*
<img src="http://i.prntscr.com/47ef0e5fa0d84736b6a7f2fe436a3306.png" />
<img src="http://i.prntscr.com/7d603bd58d9345aebde878461d3ca6b1.png" />
với --time-style bạn cũng có thể dùng tham số:full-iso, long-iso

<a name="4b"></a>
#### b.df
##### I.Khái niệm
df(`disk free`) dùng để lấy toàn bộ thông tin về lượng ổ cứng khả dụng và lượng ổ cứng đã dùng của các file hệ thống trên linux.

###### Kiểm tra dung lượng ổ cứng mà các file hệ thống sử dụng.
<img src="http://i.prntscr.com/aa17a6bdaca944f2b077419a7ed47f70.png" />

###### Hiện thị thông tin  ổ đĩa các file hệ thống sử dụng
<img src="http://i.prntscr.com/15cb6d658034491881ec9a9465f5f456.png" />

###### Hiển thị dung lượng đĩa sử dụng dạng MB và GB
- "-h": theo Gigabytes
- "-m": theo Megabytes
- "-k": theo kilobytes

<img src="http://i.prntscr.com/6da94f4074634cbd80a40a21b7d7b1af.png" />

###### Hiện thị thông tin về thư mục Home
- "-T":Hiển thị định dạng file

<img src="http://i.prntscr.com/07d3df0861184622b64beae2af4981f2.png" />

###### Xem thông tin Inodes
<img src="http://i.prntscr.com/0d23ab285ee14d0bb851a010b10d9890.png" />

<a name="5"></a>
### 5.Tham Khảo
- https://btrfs.wiki.kernel.org/index.php/Main_Page
- http://www.idz.vn/2015/08/tim-hieu-file-system-cua-linux.html
- https://hostingaz.vn/2654-tong-hop-cac-lenh-df-huu-dung-khi-kiem-tra-dung-luong-o-dia-tren-linux.html
- http://www.thegeekstuff.com/2012/06/du-command-examples/


