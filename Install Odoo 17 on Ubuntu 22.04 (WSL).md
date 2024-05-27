### Step 1: Update & Upgrade

Cập nhật và nâng cấp các gói phần mềm trên hệ điều hành Ubuntu

```
sudo apt-get update -y && sudo apt-get upgrade
```

---

### Step 2: Install OpenSSH and Fail2ban

Cài đặt máy chủ OpenSSH và Fail2Ban giúp cho phép truy cập từ xa an toàn vào hệ thống qua SSH, ngăn chặn xâm nhập giúp bảo vệ khỏi các cuộc tấn công brute-force và các nỗ lực truy cập trái phép.

```
sudo apt-get install openssh-server fail2ban
```

---

### Step 3: Install necessary packages

> **Install Wkhtmltopdf**
Giúp chuyển đổi các trang web hoặc tệp HTML thành tệp PDF
> 

```
sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb
sudo apt install -f
```

>
⚠️ Nếu dòng thứ 2 bị lỗi (libssl1.1,…), cần thực thi đoạn script bên dưới
>


```
//xfonts-75dpi
sudo apt-get install -y xfonts-75dpi
//xfonts-base
sudo apt-get install -y xfonts-base
//libssl1.1
sudo wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2_amd64.deb
sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2_amd64.deb
//Chạy lại lệnh dpkg
```

> **Install Python Packages**
> 

```
//Install pip
sudo apt-get install -y python3-pip
//install packages
sudo apt-get install python3-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev build-essential libssl-dev libffi-dev libmysqlclient-dev libjpeg-dev libpq-dev libjpeg8-dev liblcms2-dev libblas-dev libatlas-base-dev
```

> **Install Node.js and NPM**
> 

```
sudo apt-get install -y npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g less less-plugin-clean-css
sudo apt-get install -y node-less
```

---

### Step 4: **Create and Setup Database**

> **Install PostgreSQL is the database server used by Odoo**
> 

```
sudo apt install postgresql postgresql-client
```

> **Create Database User**
> 

Truy cập vào tài khoản người dùng “postgres”

```
sudo su - postgres
psql -d postgres
```

>
⚠️ Nếu truy cập người dùng postgres không được thì phải kiểm tra trạng thái postgresql
>

```
//Kiểm tra trạng thái
sudo service postgresql status
//Nếu trạng thái 'down' thì cần start
sudo service postgresql start
```

Tạo username và password với role là superuser

```
create role *odoo17* with superuser login password '*odoo17*';
//Nếu in ra CREATE ROLE là tạo thành công
```

---

### **Step 5: Clone Odoo17 From Github**

> **Install git**
> 

```
sudo apt-get install git
```

> **Create a folder containing odoo projects**
> 

```
mkdir odoo_projects
```

> **Clone Odoo 17**
> 

```
//Point to the newly created folder
cd odoo_projects
git clone -b 17.0 --single-branch --depth=1 https://github.com/odoo/odoo.git odoo17
```

---

### Step 6: Create virtual environment for odoo

> **Install python-venv**
> 

```
sudo apt-get install -y python3-venv
```

> Install folder venv
> 

```
cd odoo_projects/odoo17
python3 -m venv odoo17env
```

> Enable virtual environment
> 

```
source odoo17env/bin/activate
```

---

### Step 7: Run Wheel and Requirements File

>
⚠️ Keep in source odoo17env/bin/activate
>

> Install wheel
> 

```
pip install wheel
```

> Run requirements.txt
> 

```
python3 -m pip install -r requirements.txt
```

>
⚠️ If have Error any module, open requirements.txt and delete that line. Then reinstall requirements.txt.
Next reinstall faulty modules.
OR
Execute this script: **sudo apt-get install python3-dev**. Then reinstall requirements.txt
>

---

### Step 8: **Create a directory for the 3rd party addons**

```
cd odoo_projects/odoo17
mkdir custom
```

---

### Step 9: **Create a configuration file for the Odoo Installation**

> **Copy odoo_projects/odoo17/debian/odoo.conf file to odoo_projects/odoo17/odoo_conf/odoo17.conf**
> 

```
sudo cp odoo_projects/odoo17/debian/odoo.conf odoo_projects/odoo17/odoo_conf/odoo17.conf
sudo nano odoo_projects/odoo17/odoo_conf/odoo17.conf
```

> **Update the Conf File you have copied**
> 

```
[options]
   ; This is the password that allows database operations:
   admin_passwd = admin
   db_host = False
   db_port = False
   db_user = odoo17
   db_password = odoo17
   http_port = 8069
   addons_path = odoo_projects/odoo17/addons, odoo_projects/odoo17/custom
```

---

### Step 10: Configure Postgresql

```
sudo nano /etc/postgresql/14/main/pg_hba.conf
```

> In the line “Local” is for Unix…” → change ‘peer’ become ‘md5’
> 

---

### Step 11: Start Odoo

```
//In odoo/odoo17/
cd odoo_projects/odoo17
source odoo17env/bin/activate
python3 odoo-bin -c odoo_conf/odoo17.conf
```
