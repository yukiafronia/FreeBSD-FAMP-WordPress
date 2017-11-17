# FreeBSD-FAMP-WordPress
愛知ニキ用  
  
# 1.システムの最新化  
freebsd-update fetch  
freebsd-update install  
pkg update  
pkg upgrade  

# 2.mariadb101インストール  
pkg -y install mariadb101-client mariadb101-server  
sysrc mysql_enable=”YES”  
service mysql-server start  

# 3.Mariadb 自動インストーラ  
/usr/local/bin/mysql_secure_installation  
対話形式で進むので問いはすべて y を選択  

# 4.Mariadb Wordpress用データベース作成  
mysql -u root -p  
  
  下記SQL構文  
  create database wordpress;  
  grant all privileges on wordpress.* to 'wpuser'@'localhost' identified by 'pass1';  
  flush privileges;  
  exit  
  
# 5.ApacheServerインストール  
pkg -y install apache24  
sysrc apache24_enable = "yes"  
service apache24 start  

# 6.indexファイル削除
rm /usr/local/www/apache24/data/index.html  

# 7.php7.1インストール
pkg  -y install php71 php71-mysqli mod_php71 php71-mbstring php71-mclrypt php71-zlib php71-curl php71-gd php71-json  
vi /usr/local/etc/apache24/Includes/php.conf  

wordpressdevより拝借  
php.confの抜粋、下記部分を追記  
<IfModule dir_module>
    DirectoryIndex index.php index.html
    <FilesMatch "\.php$">
        SetHandler application/x-httpd-php
    </FilesMatch>
    <FilesMatch "\.phps$">
        SetHandler application/x-httpd-php-source
    </FilesMatch>
</IfModule>  

# 7-1.Apacheルートパスにphpconfを新規作成
echo '<?php phpinfo();  ?>' | tee /usr/local/www/apache24/data/phpinfo.php  
service apache24 restart  

# 8.wordpressインストール  
wget でftp.skm.shonan.bunkyo.ac.jp/hogehoge/wordpress-4.8.3-ja.tar.gzをダウンロード  
↓ここからは、フォルダ、ファイル名が変わるかもしれません..  
tar xfz wordpress.tar.gz  
cp -rf wordpress / * / usr / local / www / apache24 / data /  

# 8-1.フォルダ権限変更  
chown -R root：www / usr / local / www / apache24 / data /  
chmod -R 775 / usr / local / www / apache24 / data /  

あとは、http://IP/wp-adminで大丈夫かと思います。  

