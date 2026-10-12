# Quy trình Git của Nhóm 4 — TTCS02

Repo chính: https://github.com/imthoai2k4/Management-Group4
Trưởng nhóm / chủ repo: **@imthoai2k4**.

## Các nhánh

| Nhánh | Mục đích | Cách cập nhật |
| --- | --- | --- |
| main | Bản ổn định để demo/phát hành | PR từ develop, chỉ trưởng nhóm merge |
| develop | Tích hợp công việc đã hoàn thành | PR từ feature/*, có review và CI |
| feature/<thanh-vien>/<cong-viec> | Công việc của từng thành viên | Thành viên push lên nhánh của mình |

Không push trực tiếp lên main hoặc develop. Không force-push hay xóa hai nhánh này.
Nhánh codex/* dành cho công việc có Codex hỗ trợ, cũng mở PR vào develop.

## Bắt đầu một công việc

```bash
git clone https://github.com/imthoai2k4/Management-Group4.git
cd Management-Group4
git switch develop
git pull --ff-only origin develop
git switch -c feature/ten-thanh-vien/quan-ly-phong
# Sửa code và kiểm tra trước khi commit.
git add <cac-file-da-sua>
git commit -m "feat: them quan ly phong"
git push -u origin feature/ten-thanh-vien/quan-ly-phong
```

Tạo PR với **base: develop**, **compare: nhánh feature của mình**.
Điền mô tả và kết quả kiểm tra. Cần CI xanh, ít nhất một người khác có quyền ghi
approve, và giải quyết hết thảo luận. Commit mới làm mất approval cũ.

## Đồng bộ nhánh làm việc

```bash
git fetch origin
git switch feature/ten-thanh-vien/quan-ly-phong
git merge origin/develop
# Giải quyết conflict, kiểm tra lại rồi push.
git push
```

## Phát hành / demo

Trưởng nhóm mở PR **develop → main**, ghi các tính năng và kết quả demo.
PR cần CI xanh, ít nhất một approval từ người khác và giải quyết hết thảo luận.
Chỉ @imthoai2k4 được merge main. Dùng **Create a merge commit** cho PR phát hành
để giữ quan hệ lịch sử giữa hai nhánh dài hạn.

## Kiểm tra tự động

GitHub Actions kiểm tra luồng PR, build frontend, build/test backend Node,
và kiểm thử phê duyệt backend Python với database CI riêng.
Các script backend/test_phase*.py là kịch bản giai đoạn cũ, chạy thủ công khi sửa
phần liên quan; chưa được dùng làm điều kiện merge.

```bash
npm ci
npm run prisma:generate
npm --prefix frontend ci
npm --prefix frontend run build
npm run build
npm test
```

Backend Python: cài backend/requirements.txt và httpx, vào backend,
đặt DATABASE_URL tới SQLite dùng riêng cho test rồi chạy python test_approvals.py.
Script seed/reset database; không chạy trên database đang dùng hoặc production.

## Quyền và bảo vệ nhánh

Repo thuộc tài khoản cá nhân. Chủ repo giữ Admin; mời thành viên qua
**Settings → Collaborators → Add people** khi có GitHub username chính xác.
Repo cá nhân không có đầy đủ vai trò tùy chỉnh như Organization.
Repo public nên người xem tiến độ có thể đọc mà không cần được mời.

Ba ruleset:

1. TTCS02 - PR and CI: main/develop cần PR, một approval, CI và giải quyết thảo luận;
   chặn force-push và xóa; không có bypass.
2. TTCS02 - Main release owner: chặn cập nhật main; chỉ Admin có bypass **qua PR**.
   Thành viên không thể merge main; chủ repo cũng không push trực tiếp.
3. TTCS02 - Release code owner: main cần review của CODEOWNERS @imthoai2k4.
   Admin có bypass **qua PR** cho riêng quy tắc này khi tự mở PR phát hành;
   vẫn cần approval từ người khác theo ruleset thứ nhất.

Giữ quyền Admin cho trưởng nhóm vì Admin có thể sửa settings của repo.
Không commit .env, token, mật khẩu, database thật hay uploads/.
Chỉ commit .env.example dùng giá trị mẫu.
