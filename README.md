## Bước 1: Khởi tạo môi trường Docker và cài công cụ
Mở terminal trên máy host, khởi tạo container Rust và cài đặt Foundry:

```bash
docker run -it --name ityfuzz_env -w /app rust:latest /bin/bash
curl -L [https://foundry.paradigm.xyz](https://foundry.paradigm.xyz) | bash
source ~/.bashrc
foundryup

Bước 2: Chuẩn bị 2 phân xưởng (Workspaces)
Kéo mã nguồn ItyFuzz gốc về, sau đó nhân bản ra làm 2 thư mục riêng biệt (một bản giữ nguyên làm mốc đối chứng Baseline, một bản để cấy thuật toán RL):

Bash
git clone [https://github.com/fuzzland/ityfuzz.git](https://github.com/fuzzland/ityfuzz.git) ityfuzz_old
cp -r ityfuzz_old ityfuzz_rl

Bước 3: Build bản gốc (Baseline)
Truy cập vào phân xưởng 1 và biên dịch bản cũ:

Bash
cd ityfuzz_old
cargo build --release

Bước 4: Cấy thuật toán RL và Build bản mới
Quay lại thư mục gốc, tải lõi thuật toán RL từ Repository này và ghi đè vào phân xưởng 2:

Bash
cd /app
git clone [https://github.com/qkhanh0512-lab/ityfuzz-rl-src.git](https://github.com/qkhanh0512-lab/ityfuzz-rl-src.git)
cp -r ityfuzz-rl-src/* ityfuzz_rl/src/

# Truy cập vào phân xưởng 2 để biên dịch bản RL
cd ityfuzz_rl
cargo build --release

Bước 5: Phân loại và Khai hỏa
Sau khi biên dịch xong, đưa 2 file thực thi ra ngoài thư mục /app và đổi tên để dễ dàng quản lý:

Bash
cp /app/ityfuzz_old/target/release/ityfuzz /app/ityfuzz_old_exe
cp /app/ityfuzz_rl/target/release/ityfuzz /app/ityfuzz_rl_exe
Cách chạy Fuzzer:
Bây giờ trong thư mục /app đã có 2 file là ityfuzz_old_exe và ityfuzz_rl_exe. Bạn có thể tải dataset về và dùng 2 công cụ này chạy song song để kiểm thử Smart Contract.

Lệnh chạy bản cũ:
/app/ityfuzz_old_exe evm -m <đường_dẫn_contract> -- forge test

Lệnh chạy bản mới (RL):
/app/ityfuzz_rl_exe evm -m <đường_dẫn_contract> -- forge test