# 📦 E-Commerce App Deployment on CentOS 7

This project demonstrates the manual deployment of a PHP-based e-commerce application on a CentOS 7 Minimal server. It includes Apache, MariaDB, and PHP configuration — perfect for Linux system administration and cloud engineering practice.

---

## 🔧 Prerequisites

Install and enable FirewallD:

```bash
sudo yum install -y firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

---

## 🛠️ Database Setup (MariaDB)

### Install MariaDB Server:

```bash
sudo yum install -y mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

### Open firewall for MySQL traffic:

```bash
sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
sudo firewall-cmd --reload
```

### Configure the database:

```bash
mysql
```

Inside the MariaDB shell:

```sql
CREATE DATABASE ecomdb;
CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
GRANT ALL PRIVILEGES ON *.* TO 'ecomuser'@'localhost';
FLUSH PRIVILEGES;
```

> 💡 For multi-node setups, use `'ecomuser'@'<web-server-ip>'` instead of `'localhost'`.

---

## 🧾 Load Product Inventory

Use the pre-written SQL script available in the `/assets` folder of this repository:  
📄 [`assets/db-load-script.sql`](assets/db-load-script.sql)

To load it into MariaDB:

```bash
sudo mysql < /path/to/db-load-script.sql
```

---

## 🌐 Web Server Setup (Apache + PHP)

### Install Apache and PHP:

```bash
sudo yum install -y httpd php php-mysqlnd
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
sudo firewall-cmd --reload
```

### Set PHP as the default landing page:

```bash
sudo sed -i 's/index.html/index.php/g' /etc/httpd/conf/httpd.conf
```

### Start and enable Apache:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

## 🧩 Application Setup

### (Optional) Install Git if not installed:

```bash
sudo yum install -y git
```

### Clone the application code:

```bash
sudo git clone https://github.com/Sengary/ecommerce-deployment-manual /var/www/html/
```

### Create the `.env` file in the web root:

```bash
cat > /var/www/html/.env <<-EOF
DB_HOST=localhost
DB_USER=ecomuser
DB_PASSWORD=ecompassword
DB_NAME=ecomdb
EOF
```

> 🔐 Ensure permissions allow Apache to read the `.env` file:

```bash
sudo chown apache:apache /var/www/html/.env
sudo chmod 644 /var/www/html/.env
```

---

## ✅ Test the Deployment

To verify that the app is running:

```bash
curl http://localhost
```

You should see HTML content output.

---

## ⚠️ SELinux Note (If Curl Shows Blank)

If `curl http://localhost` returns nothing and everything else is correct, SELinux may be blocking Apache from reading your files.

Temporarily set SELinux to permissive (this resets after reboot):

```bash
sudo setenforce 0
```

---

## ✅ Done!

Your e-commerce app should now be live on your CentOS 7 machine. This setup reflects real-world Linux administration and service configuration best practices.
