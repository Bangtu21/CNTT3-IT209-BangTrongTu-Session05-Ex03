- 1. Chuẩn bị các nhánh và commit
Ban đầu tạo file `config.json` trên nhánh `master` với nội dung:
`{ "port": 8080, "debug": false }`

Commit:
`b7f6233 init config`

Sau đó tạo nhánh `feature-api`.

### Trên nhánh feature-api

**Commit 1 – `feat: change port`**

Thay đổi:

`port: 8080` → `port: 9000`

Commit:

`be9fb19 feat: change port`

**Commit 2 – `feat: enable debug`**

Thay đổi:

`debug: false` → `debug: true`

Commit:

`5fd0f1e feat: enable debug`

### Trên nhánh master

Quay lại `master` và tạo 2 commit:

**Commit 1 – `update port on main`**

Thay đổi:

`port: 8080` → `port: 8081`

Commit:

`b0a142d update port on main`

**Commit 2 – `dâ env config`**

Thêm cấu hình môi trường:

`"env": "production"`

Commit:

`58bce37 dâ env config`

---

- 2. Bắt đầu Rebase

Chuyển sang nhánh `feature-api`:

`git checkout feature-api`

Thực hiện:

`git rebase master`

Git bắt đầu áp dụng lần lượt 2 commit của `feature-api` lên trên `master`.

---

- 3. Conflict lần 1 – Thay đổi port

### Commit gây conflict

`be9fb19 feat: change port`

### Nội dung conflict

Git hiển thị:

`<<<<<<< HEAD`
`{ "port": 8081, "debug": false ,"env": "production" }`
`=======`
`{ "port": 9000, "debug": false }`
`>>>>>>> be9fb19 (feat: change port)`

### Nguyên nhân

Hai nhánh cùng thay đổi trường `port`:

* `master`: `8080` → `8081`
* `feature-api`: `8080` → `9000`

Ngoài ra `master` đã thêm trường `env`.

### Cách giải quyết

Mục tiêu là giữ thay đổi quan trọng của cả hai phía:

* Giữ `port: 9000` từ `feature-api`.
* Giữ `debug: false`.
* Giữ `env: "production"` từ `master`.

Kết quả:

`{ "port": 9000, "debug": false ,"env": "production" }`

Sau khi sửa file, đánh dấu conflict đã được giải quyết:

`git add config.json`

Tiếp tục rebase:

`git rebase --continue`

Git tạo lại commit:

`88d6b6f feat: change port`

---

- 4. Conflict lần 2 – Bật Debug

### Commit gây conflict

`5fd0f1e feat: enable debug`

Sau khi Git tiếp tục rebase, conflict thứ hai phát sinh trong `config.json`.

### Nội dung conflict

`<<<<<<< HEAD`
`{ "port": 9000, "debug": false ,"env": "production" }`
`=======`
`{ "port": 9000, "debug": true }`
`>>>>>>> 5fd0f1e (feat: enable debug)`

### Nguyên nhân

Commit tiếp theo của `feature-api` muốn thay đổi:

`debug: false` → `debug: true`

Trong khi kết quả sau conflict lần 1 đã có thêm trường:

`env: "production"`

Do file `config.json` được viết trên một dòng nên Git không thể tự động kết hợp hai thay đổi.

### Cách giải quyết

Giữ lại toàn bộ các thay đổi cần thiết:

* `port: 9000` từ `feature-api`.
* `debug: true` từ commit `feat: enable debug`.
* `env: "production"` từ `master`.

Kết quả:

`{ "port": 9000, "debug": true ,"env": "production" }`

Sau đó:

`git add config.json`

Tiếp tục:

`git rebase --continue`

Git tạo lại commit:

`a7831a7 feat: enable debug`

Rebase hoàn thành thành công.

---

- 5. Kiểm tra kết quả

Kiểm tra trạng thái:

`git status`

Kết quả cho thấy:

`On branch feature-api`

Không còn trạng thái `REBASE`, nghĩa là quá trình rebase đã hoàn tất.

File `.idea/` vẫn ở trạng thái `Untracked`, nhưng không ảnh hưởng đến kết quả của bài thực hành vì không tham gia vào quá trình rebase.

Kiểm tra lịch sử:

`git log --oneline --graph --all`

Kết quả:

`* a7831a7 (HEAD -> feature-api) feat: enable debug`
`* 88d6b6f feat: change port`
`* 58bce37 (master) dâ env config`
`* b0a142d update port on main`
`* b7f6233 init config`