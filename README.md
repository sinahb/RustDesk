# نصب RustDesk روی سرور ایران

> \[!WARNING]
> قبل از شروع، توجه کنید که به دلیل تحریم‌ها، نصب داکر ممکن است بدون تنظیم DNS شکن با خطا مواجه شود. حتماً ابتدا DNS را تنظیم کنید.

---

## 1️⃣ تنظیم DNS

برای بررسی وضعیت DNS و اطمینان از اتصال اینترنت:

```
resolvectl status
```

---

## 2️⃣ نصب داکر و تست آن

```
# بروزرسانی بسته‌ها
sudo apt-get update
sudo apt-get install ca-certificates curl

# ایجاد دایرکتوری برای کلیدها
sudo install -m 0755 -d /etc/apt/keyrings

# دانلود و تنظیم GPG Key رسمی داکر
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# اضافه کردن ریپازیتوری داکر به Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

# نصب داکر
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# تست نصب داکر
sudo docker run hello-world
```

> \[!TIP]
> اگر خطایی دریافت کردید، مطمئن شوید که DNS یا پروکسی شما به درستی تنظیم شده باشد.

---

## 3️⃣ نصب RustDesk در داکر

ابتدا پوشه‌ای برای RustDesk بسازید:

```
mkdir rustdesk
cd rustdesk
nano docker-compose.yaml
```

سپس محتویات زیر را در فایل `docker-compose.yaml` قرار دهید و ذخیره کنید:

```
services:
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
```

> \[!NOTE]
> `network_mode: "host"` برای اتصال مستقیم شبکه سرور ضروری است. اگر حذف شود، RustDesk به درستی کار نخواهد کرد.

---

## 4️⃣ اجرا کردن کانتینر

```
docker compose up -d
docker compose ps
```

> \[!TIP]
> گزینه `-d` کانتینر را در پس‌زمینه اجرا می‌کند. برای توقف یا ریستارت کانتینر از `docker compose down` یا `docker compose restart` استفاده کنید.

---

## 5️⃣ بررسی لاگ‌ها و برداشتن Key

```
docker compose logs
```

> \[!IMPORTANT]
> در خروجی لاگ، `key` سرور را بردارید. این Key برای اتصال کلاینت‌ها به سرور RustDesk ضروری است.

---

## 6️⃣ اتصال کلاینت‌ها

از **IP سرور** و **Key برداشته شده** برای اتصال کلاینت‌ها به شبکه RustDesk استفاده کنید.

```
IP: <سرور شما>
Key: <برداشته شده از لاگ‌ها>
```

> \[!TIP]
> بهتر است کلاینت‌ها را ابتدا روی همان شبکه داخلی سرور تست کنید و سپس برای اتصال از راه دور اقدام کنید.
