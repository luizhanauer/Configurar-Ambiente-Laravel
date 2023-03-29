Configurar Ambiente LEMP (Projeto Laravel)
==========================================

Para criar um ambiente de desenvolvimento Laravel no Ubuntu com Nginx, MariaDB e PHP8, siga os seguintes passos:

1.  Instalar o Nginx:

```
sudo apt update 
```
```
sudo apt install nginx -y
```

2.  Instalar o MariaDB:

```
sudo apt install mariadb-server mariadb-client -y
```

3.  Instalar o PHP8:

```
sudo apt install php8.0-fpm php8.0-mysql php8.0-mbstring php8.0-xml php8.0-zip -y
```
Há mais alguns pacotes extras que podem ser instalados junto (eu uso)
```
sudo apt install php8.0-cli php8.0-curl php-memcached 
                  php8.0-dev php8.0-sqlite3 php8.0-gd php8.0-json 
                    php8.0-intl php8.0-cgi php8.0-xsl php8.0-bcmath 
                      php8.0-bz2 php8.0-common php8.0-dba -y
```

4.  Instalar o Composer:

```
sudo apt install composer -y
```

5.  Configurar o Nginx:

```
sudo nano /etc/nginx/sites-available/nome-do-projeto
```
```
server {
        listen 80;
        listen [::]:80;

        root /var/www/nome-do-projeto/public;
        index index.php index.html index.htm;

        server_name nome-do-projeto.com;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php8.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}

```

6.  Habilitar o site do Nginx:

```
sudo ln -s /etc/nginx/sites-available/nome-do-projeto /etc/nginx/sites-enabled/
```

7.  Reiniciar o Nginx:

```
sudo systemctl restart nginx
```

8.  Criar um usuário e banco de dados para o projeto no MariaDB:

```
sudo mysql -u root -p
```

```
CREATE DATABASE nome_do_projeto;
CREATE USER 'usuario_do_projeto'@'localhost' IDENTIFIED BY 'senha_do_projeto';
GRANT ALL PRIVILEGES ON nome_do_projeto.* TO 'usuario_do_projeto'@'localhost';
FLUSH PRIVILEGES;
exit

```

Por padrão, o valor de bind-address é 127.0.0.1, o que significa que o servidor só pode ser acessado localmente.
Para permitir conexões externas, você precisa alterar o valor de bind-address para o endereço IP do servidor ou para 0.0.0.0, o que permite conexões de qualquer endereço IP.
**No entanto, tenha em mente que permitir conexões externas pode ser um risco de segurança se não for configurado corretamente. Certifique-se de configurar as permissões de usuário corretas e restringir o acesso apenas aos endereços IP autorizados.**

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
```
bind-address = 0.0.0.0
```
```
sudo systemctl restart mysql
```

9.  Configurar o Laravel para usar o banco de dados:

```
cd /var/www/nome-do-projeto
cp .env.example .env
nano .env

DB_DATABASE=nome_do_projeto
DB_USERNAME=usuario_do_projeto
DB_PASSWORD=senha_do_projeto

php artisan key:generate
php artisan config:clear
php artisan cache:clear

```

10.  Configurar as permissões necessárias no arquivo do projeto:

```
sudo chown -R www-data:www-data /var/www/nome-do-projeto 
```
```
sudo chmod -R 755 /var/www/nome-do-projeto/storage
```

Com esses passos, você terá configurado um ambiente de desenvolvimento Laravel completo com Nginx, MariaDB e PHP8 no Ubuntu, instalado o Composer e configurado o Laravel para usar o banco de dados e as permissões necessárias no arquivo do projeto.
