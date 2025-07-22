# CÀI ĐẶT MÔI TRƯỜNG PHÁT TRIỂN COSMOS SDK

Cài đặt Ubuntu và chạy WSL
## 1️⃣ Cài đặt Go

```bash
# Xóa Go cũ (nếu có)
sudo rm -rf /usr/local/go

# Tải và cài đặt Go (có thể thay đổi version nếu cần)
wget https://go.dev/dl/go1.24.5.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.24.5.linux-amd64.tar.gz

# Thiết lập biến môi trường
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc

# Kiểm tra phiên bản Go
go version

```
## 2️⃣ Cài đặt Ignite CLI
Ignite CLI hỗ trợ khởi tạo và quản lý dự án Cosmos SDK nhanh chóng.

```bash
# Cài Ignite CLI (phiên bản stable)
curl https://get.ignite.com/cli! | bash

# Kiểm tra phiên bản Ignite
ignite version

```
## 3️⃣ Gói cần thiết

Trước khi cài Go và Ignite, cần đảm bảo máy của bạn đã có đầy đủ công cụ cơ bản.

```bash
# Cập nhật danh sách gói
sudo apt update

# Cài đặt các gói cần thiết
sudo apt install -y curl wget tar git build-essential jq
sudo apt install net-tools

```

# Sau khi cài xong môi trường, thiết lập kết nối

## 🛠️ Bước 1: Cài đặt trên cả 3 máy

```bash
git clone https://github.com/yourname/exchange-token.git
cd exchange-token
ignite chain build

```

## 🛠️ Bước 2: Khởi tạo thư mục node
- Node 2:
  ```bash
  exchange-tokend init node2 --chain-id exchange-token
  ```

- Node 3 :
  ```bash
  exchange-tokend init node3 --chain-id exchange-token

## 🛠️ Bước 3: Copy file genesis.json từ Node1

**Trên Node1 (máy chủ), thực hiện:**

```bash
# Copy sang Node2:
scp ~/.exchange-token/config/genesis.json user@ip_node2:/home/user/.exchange-token/config/genesis.json

# Copy sang Node3:
scp ~/.exchange-token/config/genesis.json user@ip_node3:/home/user/.exchange-token/config/genesis.json
```
 - Lưu ý: dùng whoami lấy tên user
          ip_node2 hoặc ip_node3 lấy bằng ifconfig
          ssh vào máy kia để kết nối rồi mới copy đc: ssh user@ip_node2
## 🛠️ Bước 4: Cấu hình persistent_peers
- 1️⃣ Trên Node1 (máy chủ):

+ Lấy Node ID:
  exchange-tokend tendermint show-node-id

+ Lấy địa chỉ IP Node1:
  ifconfig  
  Ghép thành chuỗi kết nối:
  node_id@ip_node1:26656
  Ví dụ:
  abc123def456@192.168.1.10:26656\



- 2️⃣ Trên Node2 và Node3:

+ Mở file cấu hình trên từng máy:
  nano ~/.exchange-token/config/config.toml
+ Tìm dòng:
  persistent_peers = ""
   -> Sửa thành:
      persistent_peers = "node_id@ip_node1:26656"
  
  Ví dụ:
  persistent_peers = "abc123def456@192.168.1.10:26656"

## Bước 5: Khởi động node
+ Node2:
  exchange-tokend start
  
+ Node3:
  exchange-tokend start

🎯 Sau khi chạy, Node2 và Node3 sẽ tự kết nối về Node1 và đồng bộ blockchain.
