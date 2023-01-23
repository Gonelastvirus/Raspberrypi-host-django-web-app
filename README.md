# Raspberrypi-host-django-web-app
Here are the detailed steps for hosting your Django web app on a Raspberry Pi, including the commands involved:

## Install a web server:
* You can use Apache or Nginx as your web server. To install Apache, use the command:

 ```
 sudo apt-get install apache2
 ```
* To install Nginx, use the command:

```
sudo apt-get install nginx
```
* Install Python and pip:
```
sudo apt-get install python3
```

* To install Python3.
```
sudo apt-get install python3-pip
```

* Install Django, Django Channels, and any other required libraries:
```
pip3 install django
```
```
pip3 install channels
```
```
pip3 install <library-name>
```


## Configure your web server:

**For Apache:**
* Copy your Django project files to the directory /var/www/html/
* Create a file named django.wsgi in /var/www/html/ with the following content:
```python
        import os
        import sys

        path = '/var/www/html/<your-project-name>'  # change this to the path of your project
        if path not in sys.path:
            sys.path.append(path)

        os.environ['DJANGO_SETTINGS_MODULE'] = '<your-project-name>.settings'  # change this to the settings file of your project

        from django.core.wsgi import get_wsgi_application
        application = get_wsgi_application()
```
* Edit the Apache configuration file /etc/apache2/sites-available/000-default.conf and add the following lines at the end of the file:
```
WSGIScriptAlias / /var/www/html/django.wsgi
WSGIPythonPath /var/www/html/<your-project-name>
```
* Restart Apache using the command: sudo service apache2 restart.

**For Nginx:**

* Copy your Django project files to the directory /var/www/
* Create a file named django.conf in the directory /etc/nginx/conf.d/ with the following content:

  ```
  location / {
      proxy_pass http://127.0.0.1:8000;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  ```
Restart Nginx using the command: sudo service nginx restart.
* Configure Django Channels to use web sockets:

 Add the following code in the settings.py file of your project:
```
ASGI_APPLICATION = '<your-project-name>.asgi.application'
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels.layers.InMemoryChannelLayer"
    },
}
```

Here are the additional steps you need to take to access your web app over the internet:

Set up a static IP for your Raspberry Pi:
* You can set up a static IP for your Raspberry Pi so that it always has the same IP address, which makes it easier for you to access it over the internet.
* You can refer to the Raspberry Pi documentation on how to set a static IP.

        sudo nano /etc/dhcpcd.conf


    To open the DHCP configuration file and add the following lines at the bottom of the file:
    ```
    interface <interface-name>
    static ip_address=<ip-address>/<subnet-mask>
    static routers=<gateway-address>
    static domain_name_servers=<dns-server-address>
    ```
    e.g.
    ```
    interface eth0
    static ip_address=192.168.1.16/255.255.255.0
    static routers=192.168.1.1
    static domain_name_servers=192.168.1.1
    ```
    Replace the interface-name, ip-address, subnet-mask, gateway-address, and dns-server-address with the appropriate values for your network.

        sudo service dhcpcd restart

    to restart the DHCP service.

Configure your router:

   * You will need to configure your router to forward incoming traffic to your Raspberry Pi's IP address.
   * You will need to know the internal IP address of your Raspberry Pi, as well as the ports that need to be forwarded. By default, web servers use port 80 for HTTP and port 443 for HTTPS.
   * You can refer to your router's documentation on how to configure port forwarding.
   * log in to the router's web interface.
   * Go to the port forwarding section.
   * Add a new rule to forward incoming traffic on port 80 and 443 to the internal IP address of your Raspberry Pi.

Test your web app:

* Once you have completed all the steps, test your web app by accessing it from a different device on the same network.
* Then, test your web app by accessing it from a device on a different network, using the public IP address of your router.

Secure your server:

* Once you have your server online, it is important to secure it by creating strong passwords for all the users and for the router.
* You can use a firewall to restrict incoming and outgoing traffic and use SSL certficate to encrypt the traffic.
* Keep your server and the software updated.
* You can use the following command to install ufw firewall

        sudo apt-get install ufw

    then use the following command to enable ufw firewall

        sudo ufw enable

    you can allow specific ports like 80,443 and 22 (ssh) by using the following command

        sudo ufw allow 80/tcp
        sudo ufw allow 443/tcp
        sudo ufw allow 22/tcp

    you can check the status of firewall by using the following command

        sudo ufw status


