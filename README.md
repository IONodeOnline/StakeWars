# Hướng dẫn cài đặt tham gia Stake Wars III challenges
1. Mua VPS trên Google Cloud

  a. Yêu cầu phần cứng của node:
  
  ![image](https://user-images.githubusercontent.com/91251550/180995412-2dee15c3-096d-44f2-a682-1643a0b9f800.png)
  
  b. Đăng ký mua VPS phù hợp
  
    1. Tạo tài khoản Google cloud: https://console.cloud.google.com/
    
    2. GCC đang có chương trình tặng 300$ trial miễn phí: lưu ý cần có thẻ tín dụng
    
    3. Thiết lập Compute Engine API: enable và liên kết tài khoản bill
    
    4. Tạo 1 VM instance với cấu hình tối thiểu: để setup, sau này tùy hiệu năng máy chủ và dung lượng mà mua thêm: chi phí tối thiểu 74$
    
    ![image](https://user-images.githubusercontent.com/91251550/181026430-3cb5d32d-1278-4346-8b66-ad061cdbc20f.png)
    
    5. Login vào VPS

2. Stake Wars: Episode III. Challenge 001

  a. Tạo ví (wallet)
  
    1. Cài đặt NEAR-CLI
    
    # Cập nhật máy chủ linux
    
    sudo apt update && sudo apt upgrade -y
    
    # Cài đặt bộ công cụ lập trình, Node.js, and npm
    
    curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
    sudo apt install build-essential nodejs
    PATH="$PATH"
    
    # Cài đặt NEAR-CLI
    
    sudo npm install -g near-cli
    
    # Chọn mạng dùng cho Stakewars
    
    export NEAR_ENV=shardnet
    \\ hoặc
    echo 'export NEAR_ENV=shardnet' >> ~/.bashrc
    
    2. Một số câu lệnh NEAR-CLI
    
    # Xem Proposal Validator
    
    near proposals
    
    ![image](https://user-images.githubusercontent.com/91251550/181028040-bed3b840-02f9-45d3-8df3-b93414f2b948.jpg)

    # Xem Validators Current
    
    near validators current
    
    # Xem Validator next
    
    ![image](https://user-images.githubusercontent.com/91251550/181028350-47eacaf6-662c-4aff-b2a5-5657f49b3f8c.jpg)

3. Stake Wars: Episode III. Challenge 002

  a. Chạy câu lệnh sau để xác định cấu hình phần cứng đáp ứng yêu cầu để chạy không:
  
  lscpu | grep -P ‘(?=.*avx )(?=.*sse4.2 )(?=.*cx16 )(?=.*popcnt )’ > /dev/null \
  && echo “Supported” \
  || echo “Not supported”
  
  => Kết quả là “Supported” thì đáp ứng
  
  b. Cài đặt các thành phần phụ thuộc khác
  
    sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm python3-pip
  
    sudo apt-get install awscli -y
  
  # xét cầu hinh
  
    USER_BASE_BIN=$(python3 -m site — user-base)/bin
    export PATH=”$USER_BASE_BIN:$PATH”
  
  # Install Building env
  
  sudo apt install clang build-essential make
  
  # Install Rust & Cargo
  
    curl --proto ‘=https’ -- tlsv1.2 -sSf https://sh.rustup.rs | sh
  
    ==> nhấn số 1)
    
    ![002-2](https://user-images.githubusercontent.com/91251550/181031529-fb63fb1f-0de3-455a-b38c-4d950dcdc2c3.jpg)

  
  # Source the environment
  
    source $HOME/.cargo/env
  
  c.  Cài đặt nearcore project from GitHub
  
    git clone https://github.com/near/nearcore
    cd nearcore
    git fetch
  
    # lấy thông tin commit mới nhất: tại đây
    
    git checkout <commit>
    
    # Compile nearcore binary
    
    cargo build -p neard --release --features shardnet
    
    # Initialize working directory
    
    ./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis
    
    ![image](https://user-images.githubusercontent.com/91251550/181031874-6b0a8954-a771-47bb-98df-ec9a703bc982.jpg)

    # Replace the config.json
    
    rm ~/.near/config.json && wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
    
    # Get latest snapshot: Quan trọng: NOT REQUIRED TO GET SNAPSHOT AFTER HARDFORK ON SHARDNET DURING 2022-07-18
    
    cd ~/.near && wget https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/genesis.json
    
    ![image](https://user-images.githubusercontent.com/91251550/181032981-94c5a4a3-44de-4ecf-af31-49a41f3e101d.jpg)

    
    # Run the node
    
    cd ~/nearcore
    ./target/release/neard — home ~/.near run
    
  d. Active node trở thành Validator:
  
  # Authorize Wallet Locally
  
  near login
  
  ![image](https://user-images.githubusercontent.com/91251550/181034178-1dc90c84-8769-4066-b2cd-c7b41e1fcbcc.jpg)

  # 1 – Copy the link vào trình duyệt của bạn: với Account ID chính là tài khoản ví: VD: stakewarsnode1.shardnet.near
  
  ![image](https://user-images.githubusercontent.com/91251550/181034272-a932297e-48c2-4865-8bf4-8fde437e2557.png)

  # 2 – Gán quyền truy cập đến Near CLI:
  
  ![image](https://user-images.githubusercontent.com/91251550/181034335-d428486e-0c7e-4774-be40-630e03312439.png)

  # Nhập lại tên Account ID vào: VD: stakewarsthd1.shardnet.near
  
  ![image](https://user-images.githubusercontent.com/91251550/181034528-49bb58a8-20ba-4bf3-b25e-f735eadb1544.png)

  # ==> Sẽ sinh ra file "stakewarsthd1.shardnet.near" tại thư mục "$HOME/.near-credentials/shardnet"
  
  # Copy file và đổi tên theo câu lệnh:
  
  cp $HOME/.near-credentials/shardnet/stakewarsthd1.shardnet.near.json $HOME/.near/validator_key.json
  
  # Chỉnh sửa file validator_key.json
  
  nano ~/.near/validator_key.json
  
    a. Chỉnh “account_id” thêm chữ "factory" => stakewarsnode1.factory.shardnet.near
    
    b. Thay chữ "private_key" => "secret_key"
    
    ![002-9](https://user-images.githubusercontent.com/91251550/181035742-51314de1-c748-4da8-a1e2-6b706afac996.jpg)

  # Setup Systemd Command
  
  sudo nano /etc/systemd/system/neard.service
  [Unit]
  Description=NEARd Daemon Service
  [Service]
  Type=simple
  User=ubuntu
  #Group=near
  WorkingDirectory=/home/ubuntu/.near
  ExecStart=/home/ubuntu/nearcore/target/release/neard run
  Restart=on-failure
  RestartSec=30
  KillSignal=SIGINT
  TimeoutStopSec=45
  KillMode=mixed
  [Install]
  WantedBy=multi-user.target

  # Start Neard và kiểm tra log cho đồng bộ xong
  
  sudo systemctl enable neard \
  && sudo systemctl restart neard \
  && sudo journalctl -n 100 -f -u neard
  
  !![image](https://user-images.githubusercontent.com/91251550/181036312-daab9b1d-c062-42ab-8fcd-6b742e9445cb.jpg)

4. Stake Wars: Episode III. Challenge 003

  a. Tạo Staking Pool Contract:
  
  # Thông tin chung
  
  near call factory.shardnet.near create_staking_pool ‘{“staking_pool_id”: “<pool id>”, “owner_id”: “<accountId>”, “stake_public_key”: “<public key>”, “reward_fee_fraction”: {“numerator”: 1, “denominator”: 100}, “code_hash”:”DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ”}’ --accountId=”<accountId>” --amount=30 --gas=300000000000000
  
    pool_id：if you accountID is abc.shardnet.near,the pool_id is abc
    owner_id： you accountID is abc.shardnet.near,the accountId is abc.shardnet.near
    public key：field public_key field in validator_key.json
    accountId：The SHARDNET account deploying and signing the mount tx. Usually the same as the Owner ID.
    reward_fee_fraction：fee，1/100，1%
    
    # VD: lưu ý những thông tin cần thay đổi theo thông tin các node riêng biệt
    
    near call factory.shardnet.near create_staking_pool ‘{“staking_pool_id”: “stakewarsnode1”, “owner_id”: “stakewarsnode1.shardnet.near”, “stake_public_key”: “ed25519:24Fuo2ohHeszxHoUk4d7hFd1oWCKBdoUXsKQcjFV7sHP”, “reward_fee_fraction”: {“numerator”: 1, “denominator”: 100}, “code_hash”:”DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ”}’ — accountId=”stakewarsnode1.shardnet.near” --amount=30 --gas=300000000000000
    
    ![image](https://user-images.githubusercontent.com/91251550/181037923-c454db8f-07cf-47b5-bd02-7fb23bfcdbbb.jpg)
    
  b. Deposit and Stake NEAR
  
  # câu lệnh chung
  
  near call <staking_pool_id> deposit_and_stake --amount <amount> --accountId <accountId> --gas=300000000000000
  
  # VD:
  
  near call stakewarsnode1.factory.shardnet.near deposit_and_stake --amount 110 --accountId stakewarsnode1.shardnet.near --gas=300000000000000
  
  ![image](https://user-images.githubusercontent.com/91251550/181041336-213ac1bf-9f30-4219-bf67-65fea923e6a2.jpg)
  
  # Nếu tạo Staking pool contract mà sai thông tin thì có thể update lại theo câu lệnh sau:
  
      # Update staking pool key
      
        near call <POOL_ID> update_staking_key ‘{“stake_public_key”: “<PUBLIC_KEY>”}’ — accountId <ACCOUNT_ID>
        
      # To change the pool parameters, such as changing the amount of commission charged to 1%
      
        near call stakewarsthd1.factory.shardnet.near update_reward_fee_fraction ‘{“reward_fee_fraction”: {“numerator”: 1, “denominator”: 100}}’ --accountId stakewarsthd1.shardnet.near --gas=300000000000000
        
5. Stake Wars: Episode III. Challenge 004

# Trở thành người xác minh không hiển thị ngay lập tức. Sau khi tạo nhóm tham gia, trạng thái sẽ là đề xuất và được thêm vào:

![image](https://user-images.githubusercontent.com/91251550/181039486-65fa6f2b-eb05-4e81-9015-d191f922104e.png)

# Monitor and make alerts

journalctl -n 100 -f -u neard | ccze -A

# RPC

sudo apt install curl jq

# Check your node version

curl -s http://127.0.0.1:3030/status | jq .version

# Check Delegators and Stake

near view <your pool>.factory.shardnet.near get_accounts '{"from_index": 0, "limit": 10}' --accountId <accountId>.shardnet.near

![image](https://user-images.githubusercontent.com/91251550/181041140-dbc2e279-1cd0-4f84-95de-e59850213113.jpg)

# Check Reason Validator Kicked

curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.prev_epoch_kickout[] | select(.account_id | contains ("<POOL_ID>"))' | jq .reason

# Check Blocks Produced / Expected

curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.current_validators[] | select(.account_id | contains ("POOL_ID"))'

# Dùng app mobile : UptimeRobot để mornitor port 3030

![image](https://user-images.githubusercontent.com/91251550/181040344-dfa8ff24-185f-4a7c-b4fa-18aa99002fcc.png)

6. Stake Wars: Episode III. Challenge 006

# Tạo crontab trên máy chạy node validator cho phép ping đến network một cách tự động

nano /home/ubuntu/scripts/ping.sh

# Nội dung trong file ping.sh

#!/bin/sh
# Ping call to renew Proposal added to crontab

  export NEAR_ENV=shardnet
  export LOGS=/home/ubuntu/logs
  export POOLID=stakewarsnode1
  export ACCOUNTID=stakewarsnode1

  echo “ — -” >> $LOGS/all.log
  date >> $LOGS/all.log
  near call $POOLID.factory.shardnet.near ping ‘{}’ — accountId $ACCOUNTID.shardnet.near — gas=300000000000000 >> $LOGS/all.log
  near proposals | grep $POOLID >> $LOGS/all.log
  near validators current | grep $POOLID >> $LOGS/all.log
  near validators next | grep $POOLID >> $LOGS/all.log

# cấp quyền thực thi file

chmod +x ~/scripts/ping.sh

# Tạo crontab

crontab -e
*/5 * * * * sh /home/ubuntu/scripts/ping.sh

# Kết quả nội dung trong file all.log

![image](https://user-images.githubusercontent.com/91251550/181042722-7311c42a-c58e-4533-98ea-f6418eaee6f2.png)

# Xem online

https://explorer.shardnet.near.org/accounts/stakewarsthd1.shardnet.near

      
    


    
    

