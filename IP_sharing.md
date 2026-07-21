Step 1: Assign a Static IP to the Host PCTo prevent your IP address from changing every time you restart your PC or Wi-Fi,
set a fixed local IP address.Press Win + R, type ncpa.cpl, and hit Enter.Right-click your active Wi-Fi adapter and
click Properties.Double-click Internet Protocol Version 4 (TCP/IPv4).Select "Use the following IP address" and
fill in your current Wi-Fi details (e.g., if your current IP is 192.168.150.14):
IP address: 192.168.150.14Subnet mask: 255.255.255.0 Default gateway: 192.168.150.1 (your router's IP)
Preferred DNS server: 8.8.8.8Click OK to save.Step 2: Configure WordPress (wp-config.php)
Tell WordPress to dynamically serve pages using your Host IP address so images, CSS, and
internal links don't break on the second laptop.Go to your
WordPress folder: C:\xampp\htdocs\dynamic_horizon\.Open wp-config.php in Notepad or VS Code.Add these lines right above 
/* That's all, stop editing! Happy publishing. */:PHP// Dynamic Site URLs for LAN / Wi-Fi Sharing

if ( isset( $_SERVER['HTTP_HOST'] ) ) {
    $http_protocol = ( isset( $_SERVER['HTTPS'] ) && $_SERVER['HTTPS'] === 'on' ) ? 'https://' : 'http://';
    define( 'WP_HOME', $http_protocol . $_SERVER['HTTP_HOST'] . '/dynamic_horizon' );
    define( 'WP_SITEURL', $http_protocol . $_SERVER['HTTP_HOST'] . '/dynamic_horizon' );
}
Save and close the file.Step 3: Grant Wi-Fi Access to phpMyAdmin & MySQLBy default, XAMPP blocks other network computers from accessing phpMyAdmin and MySQL.A. Allow phpMyAdmin in ApacheOpen XAMPP Control Panel.Click Config next to Apache $\rightarrow$ Apache (httpd-xampp.conf).Search (Ctrl + F) for <Directory "C:/xampp/phpMyAdmin">.Change Require local to Require all granted:Apache<Directory "C:/xampp/phpMyAdmin">
    AllowOverride AuthConfig
    Require all granted
    ErrorDocument 403 /error/XAMPP_FORBIDDEN.html.var
</Directory>
Save the file and restart Apache.B. Allow External MySQL ConnectionsIn XAMPP, click Config next to MySQL $\rightarrow$ my.ini.Make sure bind-address is set to listen to all interfaces:Ini, TOMLbind-address = 0.0.0.0
Save the file and restart MySQL.Step 4: Open Windows Firewall PortsWindows Defender Firewall will block incoming traffic from the second laptop if Apache (Port 80) and MySQL (Port 3306) aren't allowed through.1.Open Firewall Rules:Control Panel.Press Win + R, type firewall.cpl, and press Enter. Click Allow an app or feature through Windows Defender Firewall on the left panel.2.Enable Apache and MySQL:Application Rules.Click Change settings. Locate Apache HTTP Server (or httpd.exe) and mysqld.exe (or MySQL) in the list and check the boxes for Private and Public.3.Create Explicit Inbound Port Rules:Port Rules (Optional).If connection is still blocked, open Advanced Settings in Firewall $\rightarrow$ Inbound Rules $\rightarrow$ New Rule $\rightarrow$ Select Port $\rightarrow$ Enter Ports 80, 3306 $\rightarrow$ Select Allow the connection.Step 5: How the Second Person ConnectsOnce steps 1–4 are complete, the second person connected to the same Wi-Fi can open their browser and access everything:Target ServiceURL to enter on 2nd LaptopWordPress Site[http://192.168.150.14/dynamic_horizon/](http://192.168.150.14/dynamic_horizon/)WordPress Admin Panel[http://192.168.150.14/dynamic_horizon/wp-admin/](http://192.168.150.14/dynamic_horizon/wp-admin/)phpMyAdmin Database[http://192.168.150.14/phpmyadmin/](http://192.168.150.14/phpmyadmin/)💡 Troubleshooting Tip: If the second laptop gets "Connection Timed Out" or "Site Unreachable":Verify both computers are connected to the exact same Wi-Fi band (e.g., both on main Wi-Fi, not a "Guest" Wi-Fi network).On the Host PC, temporarily disable Windows Firewall for 1 minute to check if Firewall rules were configured correctly.
