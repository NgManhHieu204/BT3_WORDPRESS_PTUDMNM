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
| **n8n** | Nền tảng tự động hóa (Workflow Automation), đóng vai trò kết nối Telegram, AI Gemini và WordPress | 5678 |

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

### 7. PHẦN MỞ RỘNG: HỆ THỐNG TỰ ĐỘNG HÓA TẠO VÀ ĐĂNG BÀI VIẾT (AI & N8N)

#### 7.1 Kiến trúc luồng xử lý (Automation Architecture)

Hệ thống được thiết kế dựa trên sự liên kết của 4 thành phần chính:

- **Telegram Bot:** Đóng vai trò là giao diện giao tiếp (User Interface) để người dùng gửi yêu cầu (Prompt).
  
- **n8n (Workflow Automation):** Đóng vai trò là trung tâm điều phối (Hub), nhận tin nhắn từ Telegram, chuyển tiếp cho AI xử lý và đẩy kết quả cuối cùng lên WordPress.
  
- **Google Gemini AI:** Đóng vai trò sinh nội dung, tiếp nhận Prompt và xuất ra văn bản được định dạng chuẩn HTML.
  
- **WordPress REST API:** Đóng vai trò tiếp nhận nội dung từ n8n và tự động khởi tạo thành một bài viết (Post) mới trên website.

#### 7.2 Tích hợp n8n vào Docker

- Khai báo thêm dịch vụ `n8n` vào tệp `docker-compose.yml`. Sử dụng tham số `command: start --tunnel` để n8n tự động thiết lập đường hầm (tunnel) ra Internet, đảm bảo khả năng tiếp nhận Webhook từ Telegram một cách tức thời.

  <img width="1265" height="836" alt="image" src="https://github.com/user-attachments/assets/9c1c0049-b493-4634-9ec0-159e96070a3b" />

- Khởi chạy bầng lệnh: `docker compose up -d`

  <img width="1278" height="255" alt="image" src="https://github.com/user-attachments/assets/b7218a91-27eb-4ea5-9149-f0ea2adc3cb7" />

#### 7.3 Thiết lập Khóa bảo mật và Phân quyền (Credentials)

Để các dịch vụ độc lập có thể giao tiếp an toàn với nhau, hệ thống đã được thiết lập các cơ chế xác thực sau:

- **WordPress Application Password :** Khởi tạo mật khẩu ứng dụng (App Password) độc lập dành riêng cho n8n. Việc này cho phép n8n gọi API đăng bài mà không cần sử dụng trực tiếp mật khẩu của quản trị viên hệ thống. Cách thực hiện: vào trang quản trị WordPress tại menu bên trái, chọn Thành viên (Users) -> Hồ sơ (Profile). Tìm mục Mật khẩu ứng dụng (Application Passwords) và ở ô "Tên ứng dụng mới" gõ n8n_bot rồi bấm nút Thêm mật khẩu ứng dụng mới. WordPress sẽ hiện ra một dãy mật khẩu
 
  <img width="1919" height="1018" alt="image" src="https://github.com/user-attachments/assets/04ac80a9-548b-4551-8a53-c5f900b1cea6" />
    
- **Bước 2: Telegram Bot Token:** Khởi tạo Bot thông qua hệ thống BotFather và trích xuất chuỗi Token để cấp quyền cho n8n đọc/gửi tin nhắn qua API của Telegram. Cách thực hiện: mở ứng dụng Telegram gõ vào ô tìm kiếm chữ: @BotFather. Bấm Start rồi gõ lệnh /newbot, gõ tên Bot (Name) tùy thích (ví dụ: AI Đăng Bài), gõ tên Username của Bot (bắt buộc phải gõ không dấu, viết liền và kết thúc bằng chữ bot, ví dụ: hieunm_wp_ai_bot). BotFather sẽ đưa ra một dòng Token (dạng 123456789:AAH...)
 
  <img width="654" height="614" alt="image" src="https://github.com/user-attachments/assets/7b4fc768-4936-4f0b-84b1-bc1d198f48e5" />

- **Bước 3: Google Gemini API Key:** Tích hợp khóa API từ nền tảng Google AI Studio để n8n có quyền truy cập và gọi mô hình ngôn ngữ lớn (LLM) xử lý văn bản. Cách thực hiện: truy cập vào Google AI Studio: `https://aistudio.google.com/` và đăng nhập bằng Gmail, tại menu bên trái bấm vào Get API key -> Create API key -> Create API key in new project. Hệ thống sẽ sinh ra một chuỗi mã API Key
 
  <img width="647" height="501" alt="Screenshot 2026-05-13 225603" src="https://github.com/user-attachments/assets/4aa7b470-8e60-49ed-8a2b-2b38a398b95a" />

#### 7.4 Xây dựng Workflow trên n8n

- Truy cập vào `http://192.168.153.128:5678` lúc này sẽ thấy báo lỗi "secure cookie". Đây là một tính năng bảo mật mới của n8n, nó yêu cầu phải truy cập bằng https, nếu truy cập bằng http cục bộ qua IP 192.168... thì nó sẽ chặn lại ngay để bảo vệ mật khẩu.

  <img width="1899" height="1041" alt="Screenshot 2026-05-13 230247" src="https://github.com/user-attachments/assets/c9a20d2b-848a-4851-9670-acd7b7221e7a" />

- Cách tắt đi: sử dụng lệnh `sudo nano docker-compose.yml` để mở file và thêm dòng `- N8N_SECURE_COOKIE=false` vào phần `environment:` của thằng `n8n`. Sau khi thêm khởi chạy lại bằng lệnh `docker compose up -d`

  <img width="1287" height="838" alt="image" src="https://github.com/user-attachments/assets/4182a595-0d04-4f6f-976f-f15fac963727" />

- Sau khi khởi chạy lại, vào lại `http://192.168.153.128:5678` sẽ thấy `n8n` yêu cầu tạo tài khoản admin nội bộ

  <img width="1919" height="1031" alt="image" src="https://github.com/user-attachments/assets/1a22c1b3-d866-439d-9a8f-b5692697d0ea" />

- Đăng nhập thành công và giao diện của `n8n` sẽ hiện ra

  <img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/8552d104-5147-4c5c-944f-0ee1604d83e8" />

- Xây dựng Workflow trên `n8n`: Bấm Start from scratch -> Add first step.

  - **NODE 1: TELEGRAM TRIGGER**

    - Ở thanh tìm kiếm bên phải gõ chữ Telegram -> Chọn biểu tượng Telegram -> On message

      <img width="1916" height="1023" alt="image" src="https://github.com/user-attachments/assets/061a9664-d8b0-4060-991c-39dca8b9ad53" />
      
    - Ở phần Parameter tại mục Credential chọn Set up Credential
   
      <img width="1919" height="1022" alt="image" src="https://github.com/user-attachments/assets/6da3375f-aaff-454e-a517-10d37f5c31b5" />

    - Khi cửa sổ hiện ra dán mã Token của BotFather vào ô Access Token rồi bấm Save
   
      <img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/e2f087ae-3e12-4836-8592-399d6b1ea80e" />

    - Đóng cửa sổ cài đặt Token lại chọn nút Test this trigger để n8n bắt đầu ở trạng thái chờ. Mở Telegram chat với Bot câu: "Test hệ thống". Quay lại n8n sẽ thấy nó hiện chữ Success

      <img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/8e1ed5c8-9cd0-4fc5-9cea-c3a08af0762a" />

  - **NODE 2: Google Gemini**
 
    - Bấm vào dấu + nằm ngay bên phải cái Node Telegram Trigger rồi gõ chữ Google Gemini -> Chọn biểu tượng Google Gemini -> Message a model
   
      <img width="1919" height="1019" alt="image" src="https://github.com/user-attachments/assets/922944c5-96ce-409b-816f-0b38fb59f3a3" />

    - Ở phần Parameter tại mục Credential chọn Set up Credential. Khi cửa sổ hiện ra dán mã dán mã API Key đã lấy ở Google AI Studio vào, sau đó bấm Save.
   
      <img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/50c90591-1742-4836-a1ea-9e11957e34a4" />

    - Mục Model nó tự động chọn gemini-1.5-flash, có thể chọn phiên bản khác nếu muốn. Tại ô tên là Message thêm nội dung vào Prompt rồi chọn Expression. Ngay bên dưới bên trái của Prompt có 1 biểu tưởng ⤢ khi bấm vào một cửa sổ Edit Expression sẽ hiện ra. Kéo text (bên cạnh nó có ghi giá trị là "Test hệ thống") ở bên trái cửa sổ vào ngay sau dấu cách của chữ "Chủ đề bài viết là: ". Ngay lập tức sẽ có đoạn mã {{ $json.message.text }} tự động thêm vào đúng vị trí trong khung Prompt.
   
      <img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/4dc89c09-b5c9-4faa-bb1d-f85977f01a5f" />

      <img width="1919" height="1018" alt="image" src="https://github.com/user-attachments/assets/ed36f92a-1fdb-46b7-8a7a-33f6c8e6cc1a" />

    - Kết quả:
   
      <img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/538293bb-481b-47c0-8418-a8b2e54ab48b" />

  - **Node 3: WordPress**

    - Bấm vào dấu + nằm ngay bên phải cái Node Google Gemini gõ tìm và chọn WordPress -> Create a post
   
     <img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/d9ed724e-b765-451f-b3ca-c63dd9416716" />

    - Ở phần Parameter tại mục Credential chọn Set up Credential. Khi cửa sổ hiện ra điền địa chỉ URL (`https://blog.nguyenmanhhieu.id.vn`), Username và Password (dùng Application Password)

      <img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/b0b2b23a-66b4-46c7-bb6e-1e3c65d8ad03" />

    - Cấu hình đăng bài: tại Title bật Expression lên rồi kéo text từ Telegram vào; tại Additional Fields chọn Add rồi chọn Content, bật Expression lên rồi kéo text từ Gemini vào; Tiếp tục bấm Add Field -> Chọn Status tại đây mặc định sẽ là Draft chọn thành Publish để bài viết được public thẳng lên trang chủ
   
      <img width="1919" height="1023" alt="image" src="https://github.com/user-attachments/assets/2db6a6ec-72ad-4d3d-b439-7ca4d6192d36" />

      <img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/c8131793-41ba-4e39-844a-0dc1f2e53709" />

      <img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/e15bfea7-2553-43e3-b9ca-738be9175203" />

    - Kết quả: bấm nút Execute step và cột Output bên phải sẽ trả về một dãy dữ liệu và một cái link
   
      <img width="1919" height="1023" alt="image" src="https://github.com/user-attachments/assets/423164fc-0bea-4c68-93b2-8d2fe3efdc35" />

    - Cuối cùng tại góc trên cùng bên phải chọn Publish để chuyển sang trạng thái Active

      <img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/dbcb37d8-f7e9-4a96-b32e-07a4c86ae3f0" />

      <img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/3aa52866-af9c-4ebd-9919-64b47f8c54fb" />

#### Thành quả 

 - Truy cập link và xem kết quả:
   
   <img width="1919" height="1022" alt="image" src="https://github.com/user-attachments/assets/56f21751-6d45-4c62-a75d-2c781acfcd13" />

- Thêm ví dụ: 

  <img width="1919" height="1010" alt="image" src="https://github.com/user-attachments/assets/b7480e7b-49a9-43dd-b755-95110c394967" />
