# Green-Tech – Đặc tả không gian cộng tác

**Dự án:** Trang trại thông minh (Green-Tech)  
**Mục đích:** Lưu trữ an toàn mã nguồn cảm biến, chia sẻ tài liệu đúng đối tượng và quản lý lỗi bằng Task có thời hạn.  
**Nền tảng đề xuất:** Git (mã nguồn), Google Drive/OneDrive (tài liệu & bản sao lưu), Microsoft Teams/Slack/Lark (trao đổi & Task).

## 1. Cấu trúc thư mục (Folder)

```text
Green-Tech/                          # Thư mục Cloud của dự án
├── README.md                        # Nội quy và hướng dẫn cộng tác
├── 01_Source-Code/
│   ├── sensors/                     # BẢN SAO mã nguồn đã kiểm duyệt từ Git
│   ├── .env.example                 # Tên biến cấu hình mẫu, KHÔNG có mật khẩu thật
│   └── README_Sensors.md            # Hướng dẫn cài đặt và đường dẫn kho Git
├── 02_Documents/
│   ├── SRS/                         # Yêu cầu hệ thống
│   └── Technical-Specs/             # Đặc tả thiết bị, sơ đồ cảm biến
├── 03_Reports/
│   ├── Internal/                    # Báo cáo nội bộ
│   └── Investor-Reports/            # Chỉ các báo cáo đã duyệt cho nhà đầu tư
├── 04_Partners/
│   └── Device-Feedback/             # Tài liệu để đối tác nhận xét
├── 05_Task-Records/                 # Biên bản xử lý lỗi và kết quả kiểm thử
└── 06_Backups/                      # Bản sao lưu mốc quan trọng, giới hạn quyền
```

**Lưu ý:** Mã nguồn gốc được quản lý trong kho Git riêng (`green-tech-sensors`), ví dụ có `sensors/`, `tests/` và `.gitignore`. Thư mục `01_Source-Code/sensors/` trên Cloud chỉ chứa bản sao được xuất từ Git, không phải nơi nhiều Dev sửa mã trực tiếp. Không đồng bộ thư mục `.git`, file `.env` hay dữ liệu bí mật lên thư mục Cloud chia sẻ.

## 2. Hybrid Workflow – Lưu trữ an toàn cho đội Dev

1. **Local:** Dev clone kho Git về máy, tạo nhánh riêng (`feature/...`, `fix/...`) và viết/chạy thử code cảm biến tại máy.
2. **Git/Remote:** Mỗi thay đổi được `commit` và `push`; tạo Pull Request để người khác review trước khi nhập vào `main`. Bật bảo vệ nhánh `main` nếu nền tảng hỗ trợ.
3. **Cloud:** Đồng bộ tự động **tài liệu, báo cáo** và lưu các **snapshot mã nguồn đã duyệt** lên Cloud. Sử dụng lịch sử phiên bản và thùng rác Cloud để cứu dữ liệu khi cần.
4. **Sao lưu:** Có bản sao lưu độc lập, kiểm tra khả năng phục hồi định kỳ; không coi đồng bộ là một bản sao lưu chống xóa nhầm. Duy trì ít nhất một bản sao không bị đồng bộ xóa theo.
5. **Bảo mật:** File `.env` thật lưu cục bộ tại môi trường Dev hoặc trong hệ thống quản lý secrets; tuyệt đối không commit, gửi qua chat hay chia sẻ Cloud. Dùng `.env.example` chỉ chứa tên biến và giá trị giả. Thêm `.env`, `**/.env`, `.env.*` vào `.gitignore` (ngoại lệ riêng cho `.env.example`). Nếu lộ mật khẩu/token, phải thu hồi và thay mới ngay.

**Ví dụ `.gitignore`:**

```gitignore
.env
**/.env
.env.*
**/.env.*
!.env.example
!**/.env.example
```

## 3. Quy định phân quyền (Permissions)

| Đối tượng | Khu vực được cấp quyền | Mức quyền | Quy định |
|---|---|---|---|
| Trưởng dự án | Thư mục gốc và toàn bộ tài nguyên | Owner / Quản trị | Duyệt quyền, khôi phục, bàn giao |
| Dev kỹ thuật | Kho Git, tài liệu kỹ thuật liên quan | Write qua Git; Editor tài liệu được giao | Mã nguồn thay đổi qua Pull Request; không có quyền cấp chia sẻ bên ngoài |
| Nhà đầu tư | `03_Reports/Investor-Reports/` (chỉ báo cáo đã duyệt) | **Viewer** | Chỉ xem, không sửa và không thấy code, báo cáo nội bộ |
| Đối tác thiết bị | Tài liệu được chọn trong `04_Partners/Device-Feedback/` hoặc bản sao đặc tả phù hợp | **Commenter** | Được bình luận/góp ý, không sửa nội dung gốc |
| Người ngoài khác | Không có | **No access** | Chỉ cấp khi trưởng dự án phê duyệt |

- Mặc định dùng **Restricted / Specific people**, chia sẻ bằng tài khoản cụ thể, không bật **Anyone with the link**.
- Không chia sẻ cả thư mục gốc cho nhà đầu tư hoặc đối tác. Nếu tài liệu kế thừa quyền rộng từ thư mục cha, tách ra vị trí riêng có quyền giới hạn trước khi chia sẻ.
- Chủ dự án rà soát quyền và nhật ký truy cập (nếu dịch vụ hỗ trợ) định kỳ, đồng thời thu hồi quyền ngay khi thành viên rời nhóm.
- **Cờ đỏ bảo mật:** Không đưa `.env`, API key, mật khẩu Wi-Fi, mã truy cập cảm biến hay file cấu hình thật vào bất kỳ thư mục chia sẻ nào.

## 4. Cấu trúc Channel và quy tắc giao tiếp

| Channel | Thành viên | Mục đích |
|---|---|---|
| `#announcements` | Toàn đội | Quy định, kế hoạch, hạn nộp (chỉ người phụ trách đăng thông báo chính) |
| `#dev-sensors` | Đội Dev | Thảo luận code, tích hợp cảm biến, Pull Request |
| `#sensor-bugs` | Đội Dev + QA | Báo lỗi cảm biến theo Thread, kèm log và cách tái hiện |
| `#task-updates` | Đội dự án | Trạng thái Task, người chịu trách nhiệm, deadline |
| `#device-partners` | Nhân sự phụ trách đối tác | Tổng hợp ý kiến đối tác, không đăng bí mật kỹ thuật |

**Quy tắc:** Viết tin nhắn đầy đủ ngữ cảnh, không chỉ gửi “Chào bạn” rồi chờ; sử dụng Thread cho từng vấn đề; `@mention` đúng người; gửi thông báo theo lịch nếu khác múi giờ; không yêu cầu phản hồi tức thì trừ sự cố khẩn cấp. Không gửi khóa bí mật qua Channel.

### Chuyển lỗi cảm biến thành Task có Deadline

1. Thành viên báo lỗi tại `#sensor-bugs` gồm mã thiết bị, biểu hiện, bước tái hiện, ảnh/log đã che thông tin nhạy cảm và mức độ ảnh hưởng.
2. Trưởng nhóm tạo **Task** từ nội dung thảo luận, gắn đường dẫn tới Thread và người xử lý (Assignee).
3. Task phải có **tiêu đề, mô tả, độ ưu tiên, deadline, checklist và trạng thái** (`To do` → `In progress` → `Review/Test` → `Done`).
4. Người phụ trách cập nhật Task thay vì để thông tin trôi trong chat. QA xác nhận kết quả rồi mới đóng Task.

**Ví dụ Task:**

- **Tiêu đề:** BUG-01 – Cảm biến độ ẩm trả về 0% bất thường
- **Người xử lý:** Dev phụ trách cảm biến
- **Ưu tiên:** High
- **Deadline minh họa:** 17:00 ngày 27/10/2026 (giờ Việt Nam)
- **Checklist:** Tái hiện lỗi → xác định nguyên nhân → sửa code → kiểm thử trên thiết bị → cập nhật tài liệu
- **Tiêu chí hoàn thành:** Dữ liệu đọc ổn định và có log/bằng chứng kiểm thử

## 5. Transfer Ownership – Bàn giao khi kết thúc bài tập

1. Chốt phiên bản `main`, lưu tài liệu và sao lưu độc lập; thống kê Task chưa xong.
2. Chỉ định tài khoản nhận bàn giao được phép sở hữu tài nguyên (giảng viên/trưởng nhóm kế tiếp hoặc tài khoản chung của tổ chức, tùy chính sách).
3. Thực hiện **Transfer Ownership / Change owner** trên nền tảng Cloud nếu hỗ trợ. Chuyển hoặc cấp quyền quản trị tương ứng cho kho Git, Channel và bảng Task **riêng biệt** (chuyển chủ sở hữu file không tự chuyển toàn bộ dịch vụ).
4. Người nhận xác nhận truy cập và khả năng quản trị. Sau đó hạ/thu hồi quyền của thành viên cũ và rà soát các liên kết chia sẻ.
5. Bàn giao hướng dẫn khởi chạy qua tài liệu; chuyển bí mật bằng kênh quản lý secrets an toàn, **không gửi file `.env`**.

> Nếu nền tảng không cho chuyển Owner giữa hai tổ chức/tên miền, dùng tài khoản quản trị tổ chức hỗ trợ hoặc tạo bản bàn giao được kiểm soát, rồi xác minh quyền trước khi khóa tài khoản cũ.

## 6. Kiểm thử sự cố – Một thành viên xóa sạch folder `sensors`

**Mục tiêu:** Khôi phục thư mục `Green-Tech/01_Source-Code/sensors/` nhanh, đầy đủ và không ghi đè công việc mới.

1. **Khoanh vùng:** Báo tại `#sensor-bugs`, tạm dừng thao tác/xóa thêm và tạm dừng đồng bộ trên các máy liên quan nếu thao tác xóa đang lan truyền; trưởng nhóm kiểm tra thời điểm xóa.
2. **Khôi phục nhanh từ Cloud:** Mở **Trash/Recycle Bin** của dịch vụ Cloud bằng tài khoản đủ quyền → tìm `sensors` → chọn **Restore** về đường dẫn ban đầu. Kiểm tra cả thư mục và file bên trong, tránh khôi phục đè lên nội dung mới.
3. **Nếu Cloud không còn bản xóa:** Kiểm tra lịch sử phiên bản/khôi phục toàn thư mục (nếu dịch vụ hỗ trợ), rồi lấy bản snapshot gần nhất tại `06_Backups/`.
4. **Khôi phục mã nguồn từ Git:** Vì Git mới là nguồn chính, tải lại kho Git hoặc restore thư mục `sensors/` từ commit tốt gần nhất; kiểm tra thay đổi chưa push trên từng máy trước khi thay thế. Không chạy lệnh xóa/reset toàn bộ kho một cách tùy tiện.
5. **Xác minh và mở lại:** So sánh số file và commit/bản snapshot; chạy test cảm biến, xác nhận file cấu hình không bị lộ, bật đồng bộ lại sau khi thư mục chuẩn đã được khôi phục.
6. **Phòng ngừa lặp lại:** Giảm quyền xóa, bật cảnh báo/nhật ký truy cập nếu có, duy trì bản sao lưu tách biệt và diễn tập khôi phục định kỳ.

**Kết quả mong đợi:** Thư mục `sensors` được phục hồi từ thùng rác Cloud hoặc bản sao lưu; toàn bộ mã nguồn có thể tái tạo từ Git; `.env` không bị phát tán.
