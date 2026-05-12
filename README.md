# MÔN HỌC: PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# HỌ VÀ TÊN: NGUYỄN MẠNH HIẾU - MSSV: K225480106020 - LỚP: K58KTP

## TRIỂN KHAI WEBSITE CÁ NHÂN VỚI WORDPRESS TRÊN DOCKER

### 1. Giới thiệu hệ thống

Website được xây dựng dựa trên mã nguồn mở **WordPress** và vận hành trong môi trường container hóa (Docker). Cấu trúc hạ tầng bao gồm 3 dịch vụ chính được định nghĩa trong file `docker-compose.yml`:

| Tên Dịch vụ | Vai trò trong hệ thống | Cổng (Port) |
| :--- | :--- | :--- |
| **MariaDB** | Hệ quản trị cơ sở dữ liệu lưu trữ nội dung website | Chạy ngầm |
| **phpMyAdmin** | Công cụ giao diện web để quản lý trực tiếp CSDL | 8080 |
| **WordPress** | Mã nguồn mở CMS, xử lý logic và giao diện người dùng | 8000 |

---

### 2. Triển khai Hạ tầng & Cơ sở dữ liệu

**Bước 1: Tạo thư mục**: Sử dụng lệnh `mkdir myblog && cd myblog` để tạo thư mục chứa project

**Bước 2: Tạo file docker-compose.yml**: Sử dụng lệnh `sudo nano docker-compose.yml` để tạo file và khi cửa sổ nano hiện ra thì thêm nội dung vào file và bấm `Ctrl + O` để lưu và `Ctrl + X` để thoát

<img width="1460" height="745" alt="image" src="https://github.com/user-attachments/assets/288b6608-b08d-48a4-b894-a59d0a3d5c9d" />

**Bước 3: Khởi chạy các dịch vụ Docker**: Sử dụng lệnh `docker compose up -d` để tải image và chạy đồng thời 3 container. Hệ thống tự động liên kết WordPress với MariaDB thông qua các biến môi trường (Environment variables).

<img width="1467" height="310" alt="image" src="https://github.com/user-attachments/assets/269c0af2-a4eb-422d-8e5c-9ce6252fe27b" />

**Bước 4: Tạo Cơ sở dữ liệu trống từ phpMyAdmin**: Truy cập phpMyAdmin qua cổng `8080`. Tại đây, tiến hành tạo một Database trống mang tên `db_wordpress`. WordPress sẽ tự động kết nối vào Database này và khởi tạo các bảng (tables) cần thiết trong quá trình cài đặt mà không cần thao tác bằng tay.

<img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/3c35d739-8129-4a94-a6c4-ced0aecc54d3" />

### 3. Cài đặt WordPress

**Các bước thiết lập**:

- Mở tab mới trên trình duyệt và truy cập vào: http://192.168.153.128:8000

- Tại đây màn hình cài đặt WordPress sẽ hiện ra chọn Tiếng Việt (hoặc English)

  <img width="1919" height="1017" alt="image" src="https://github.com/user-attachments/assets/90d0e200-af1a-47f0-8870-9483bb11505f" />

- Tiếp theo điền Tên Website, Tên đăng nhập Admin, Mật khẩu

  <img width="1915" height="1027" alt="image" src="https://github.com/user-attachments/assets/6fa60fca-81a0-402f-a2c3-5b90a8c9196f" />

- Cuối cùng chọn cài đặt và đăng nhập vào trang quản trị của WordPress

  <img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/416a048b-b50e-4c2c-a791-32910cb649d4" />

  <img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/1583d61a-9c83-4c23-a49c-e3debc50a996" />

### 4. Nội dung Website

Sau khi cài đặt xong WordPress, em đã thiết kế và đăng tải 2 bài viết:

**Bài viết 1: Giới thiệu bản thân sinh viên**

Bài viết bao gồm các thông tin, hình ảnh cá nhân

<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/cc804d2f-6970-4a73-ab19-aaba81e55318" />

**Bài viết 2: Giới thiệu ngành học tại trường TNUT**

Bài viết giới thiệu về ngành học đang theo đuổi tại Trường Đại học Kỹ thuật Công nghiệp (TNUT), bao gồm hình ảnh minh họa và video giới thiệu ngànhg.

<img width="888" height="946" alt="image" src="https://github.com/user-attachments/assets/8207db2a-1bca-4954-ae1b-79756e76355b" />

### 5. Public Website qua Cloudflare Tunnel

Hệ thống được public ra Internet để có thể truy cập toàn cầu bằng giao thức HTTPS thông qua Cloudflare Tunnel với domain cá nhân, đảm bảo an toàn và chuyên nghiệp. Các bước thực hiện:

- **Tạo đường hầm:** Sử dụng lệnh `cloudflared tunnel create wpblog`

  <img width="1475" height="147" alt="image" src="https://github.com/user-attachments/assets/6e69a170-a72f-4ef3-a1be-6da53714fd63" />
  
- **Gắn tên miền vào đường hầm này:** Sử dụng lệnh `cloudflared tunnel route dns wpblog blog.nguyenmanhhieu.id.vn`

  <img width="1474" height="97" alt="image" src="https://github.com/user-attachments/assets/2c4f4093-618a-4129-963f-bd3e51dc1713" />

- **Tạo file cấu hình và khởi chạy Tunnel:**
  ```text
  TUNNEL_ID=$(cloudflared tunnel list | grep wpblog | awk '{print $1}')
  echo "url: http://localhost:8000" > ~/.cloudflared/config.yml
  echo "tunnel: $TUNNEL_ID" >> ~/.cloudflared/config.yml
  echo "credentials-file: /home/hieu/.cloudflared/$TUNNEL_ID.json" >> ~/.cloudflared/config.yml
  cloudflared tunnel run wpblog
  ```

- **Cập nhật lại URL:** WordPress có một cơ chế rất bảo thủ là lúc cài đặt nó bằng đường link nào `http://192.168.153.128:8000` nó sẽ ghi cứng cái link đó. Khi có ai đó truy cập bằng tên miền `https://blog.nguyenmanhhieu.id.vn`, WordPress nhận ra người lạ và nó sẽ tự động ép trình duyệt chuyển hướng về cái đuôi :8000. Các bước thực hiện:

  - Mở trình duyệt và truy cập lại bằng link cục bộ: http://192.168.153.128:8000/wp-admin
 
  - Tại menu bên trái, bấm vào chữ Cài đặt (Settings) -> Chọn Tổng quan. Ở đây có 2 ô rất quan trọng đang chứa link cũ là Địa chỉ WordPress (URL) và Địa chỉ trang web (URL). Chỉnh sửa cả 2 thành: https://blog.nguyenmanhhieu.id.vn

  <img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/47008cde-4869-4cd4-93d5-0b45da11a105" />

- **Thành quả:** Truy cập https://blog.nguyenmanhhieu.id.vn

  <img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/4ea7656b-f342-4ba6-bee7-447c39370c0b" />

### 6. Nhận xét & Đánh giá việc sử dụng WordPress

Sau quá trình tự tay cấu hình và thiết lập website bằng mã nguồn mở WordPress, em rút ra một số nhận xét như sau:

**Về công sức và thời gian triển khai:**

- **Tiết kiệm công sức tối đa:** Thay vì phải tự code toàn bộ hệ thống Backend, Frontend và Database từ con số 0, WordPress cung cấp sẵn một hệ thống hoàn chỉnh. Chỉ mất khoảng 15-20 phút dựng Docker và cấu hình là đã có một trang web sẵn sàng hoạt động.

**Về mức độ dễ/khó khi sử dụng:**

- **Rất dễ tiếp cận và thao tác:** Giao diện quản trị (Admin Dashboard) thân thiện, trực quan. Trình soạn thảo dạng khối (Block Editor) giúp việc thêm văn bản, hình ảnh, nhúng video (YouTube) hay âm thanh trở nên cực kỳ đơn giản (chỉ bằng thao tác kéo thả) mà không yêu cầu kiến thức về HTML/CSS.
  
- **Hệ sinh thái phong phú:** Dễ dàng thay đổi giao diện (Theme) hoặc thêm tính năng (Plugin) chỉ với vài cú click chuột.

**Về tài nguyên tiêu thụ của máy chủ (RAM/CPU):**

- **Tiêu tốn khá nhiều tài nguyên:** Nhược điểm của WordPress (và các CMS viết bằng PHP nói chung) là khá nặng. Khi chạy kết hợp cả container WordPress và MariaDB, hệ thống ngốn nhiều RAM (thường yêu cầu tối thiểu 1GB RAM để chạy mượt) và Disk I/O cao hơn so với các ứng dụng tự code gọn nhẹ. Nếu có lượng truy cập lớn, máy chủ sẽ rất dễ tốn tài nguyên CPU nếu không áp dụng các biện pháp tối ưu bộ nhớ đệm (Caching).
