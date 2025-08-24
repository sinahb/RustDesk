## مراحل نصب RustDesk روی سرور ایران : 
- 1- چون داکر ایران رو تحریم کرده اول dns شکن یا امثال اون رو روی سرور تنظیم میکنیم :
'
resolvectl status
'
- 2- نصب داکر از طریق کد های موجود در سایتش و تست نصب داکر
        # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo docker run hello-world'''

- 3- نصب rustdesk برای داکر، از طریق ساخت فایل docker-compose.yaml و اجرای ان
- mkdir rustdesk
- cd rustdesk
- nano docker-compse.yaml
- محتویات زیر را در فایل ساخته شده میگذاریم و ذخیره کرده و خارج میشویم
- services:
  hbbs:
    container_name: hbbs
    image: rustdesk/rustdesk-server:latest
    command: hbbs
    volumes:
      - ./data:/root
    network_mode: "host"

    depends_on:
      - hbbr
    restart: unless-stopped

  hbbr:
    container_name: hbbr
    image: rustdesk/rustdesk-server:latest
    command: hbbr
    volumes:
      - ./data:/root
    network_mode: "host"
    restart: unless-stopped
- 
- 4- اجرا کردن کانتینر ساخته شده
- docker compose up -d
- docker compose ps
- 
- 
- 5- بررسی logs و برداشتن key در گزارش داکر
- docker compose logs
- 6- استفاده از IP سرور و key برداشته شده برای استفاده در کلاینت ها و اتصال به شبکه ایجاد شده
