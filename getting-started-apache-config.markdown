# Apache configuration

If you don't use Nginx:

```
<VirtualHost *:80> 
      ServerAdmin webmaster@localhost  
      ServerName <YOUR DOMAIN> 
      DocumentRoot /var/www/whatsspy 

      <Directory /> 
            Options FollowSymLinks  
            AllowOverride None 
      </Directory> 

      <Directory /var/www/> 
            Options Indexes FollowSymLinks MultiViews 
            AllowOverride None 
            Order allow,deny 
            allow from all 
      </Directory> 

      <Directory /var/www/whatsspy/images/profilepicture/> 
            Options -Indexes +FollowSymLinks +MultiViews 
            AllowOverride None 
            Order allow,deny 
            deny from all 
      </Directory> 

      ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/ 
      <Directory "/usr/lib/cgi-bin"> 
            AllowOverride None Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch 
            Order allow,deny 
            Allow from all 
      </Directory> 

      ErrorLog "{APACHE LOG DIR}/error.log 
      # Possible values include: debug, info, notice, warn, error, crit, # alert, emu. 
      LogLevel warn 
      CustomLog ${APACHE LOG DIR}/access.log combined 
</VirtualHost> 

```