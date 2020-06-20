### nginx installation
```
sudo apt-get update
sudo apt-get install nginx
```
* At this point, nginx is not only installed,but also running. To ensure this, run
```
ps aux | grep nginx
```
* Output will be somethig like below
<img src="grep_nginx.png" width="100%" height="80%">

* We can also test on browser. Run 

```
ifconfig
```
* Copy the ip address (inet) and hit on browser. Nginx homepage should be appeared.
* Check the configuration files located at /etc/nginx folder. Run
```
ls -l /etc/nginx/
```
### Building nginx from source and adding modules
* Download the mainline version of nginx from http://nginx.org/en/download.html . Copy the link of .pgp(not the windows version)
* Extract the tar.gz file
```
tar -xzvf nginx-1.19.0.tar.gz
```
* Go to the extracted directory
```
cd nginx-1.19.0/
```
* First step is to configure our source code for the build. To do this, run the configure script inside the source code directory.
```
./configure
```
* If **C Compiler cc not found** appeares, run 
```
sudo apt-get install build-essential
```
* Now, we've all necessary tools to compile nginx. Re run the ./configure command, Compiler not found error should be gone.
* Now, we'll have to install some packages to remove PCRE library errors which is appeared at the bottom.
```
sudo apt-get install libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev 
```
* Now, it's time to add some customized configuration flags. 
```
./configure --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module
```
* Compile the configuration source by running 
```
make
```
* Now, install the compiled source by running
```
sudo make install
```
* Nginx is now installed with our customized configuration. Check the version of nginx by
```
nginx -V
```

### Creating a sample virtual host
* We'll create a basic virtual host that serves static files. 
* Create a directory in Desktop and place a html file,a css file linked with that html and a image.
```
cd Desktop/moshiur/nginx
touch index.html style.css sample.png
```
* We've to edit the main configuration file which is at /etc/nginx/ path.
```
sudo gedit /etc/nginx/nginx.conf
```
* Replace everything with the following lines inside it
```
events{}

http{
	server{
		listen 80;
		server_name localhost;
		root /home/moshiur/Desktop/nginx;
	}
}
```
* Always check the conf file if there is any error or not.
```
sudo nginx -t -c /etc/nginx/nginx.conf
```
* If there is no error in the conf file, reload nginx. We've to do this everytime we make any change inside the nginx.conf file.
```
sudo systemctl reload nginx
```
* Now, if we hit "localhost" on our browser, our html page should be appeared instead of the nginx homepage. Because, we've replaced everything inside the nginx.conf file. 
* We'll notice that, html file is appeared but css is not loaded. Its because, the css file is not treated according to its corresponsding Content-Type. To ensure this, run
```
curl -I http://localhost/style.css
```
We'll notice that, Content-Type of style.css is **text/plain**. 
To enable all the mime types, We'll have to include **mime.types** inside nginx.conf file. So, our nginx.conf file should be like following:
```
events{}

http{
  include mime.types;
	server{
		listen 80;
		server_name localhost;
		root /home/moshiur/Desktop/nginx;
	}
}
```
* If we run the command again
```
curl -I http://localhost/style.css
```
We'll notice that, Content-Type is not text/css. Hit the browser again and html page will be loaded with css.

## location blocks

#### Preference order: 
**1. Exact Match: = URI**
* Will execute this block that exactly match with this URI. 
* http://localhost/greet 

```
location = /greet{
			return 200 "Hello Moshiur - From exact location";
		}
```
**2. Preferential Prefix Match ^~ URI**
* Same as prefix match but has higher priority than regex match
* http://localhost/greet2
```
# Preferential Prefix match
		location ^~ /greet2{
			return 200 "Hello Moshiur- From Preferential prefix location";
		}
```
**3. REGEX match ~* URI**
* Has higher priority than prefix match. but less priority than preferential prefix match.
* http://localhost/greet2
```
# Regex match Case insensitive
		location ~* /greet[0-9]{
			return 200 "Hello Moshiur- From prefix-case insensitive location";
		}
```
**4. Prefix match URI**
* Lowest priority. Will execute this block when anything starts with greet
* http://localhost/greeting , http://localhost/greetmoshiur etc.
```
# Prefix match
		location /greet{ 
			return 200 "Hello Moshiur- From prefix location";
		}
```
* Location context should be placed inside server context.
* nginx.conf file

```
events{}

http{
	
	include mime.types;

	server{
	
		listen 80;
		server_name localhost;
		root /home/moshiur/Desktop/nginx;

		# Prefix match
		location /greet{ 
			return 200 "Hello Moshiur- From prefix location";
		}

		# Exact match
		location = /greet{
			return 200 "Hello Moshiur - From exact location";
		}

		# Regex  match Case sensitive
		location ~ /greet[0-9]{
			return 200 "Hello Moshiur - From regex-case sensitive location";
		}

		# Regex match Case insensitive
		location ~* /greet[0-9]{
			return 200 "Hello Moshiur- From prefix-case insensitive location";
		}

		# Preferential Prefix match
		location ^~ /greet2{
			return 200 "Hello Moshiur- From Preferential prefix location";
		}
	}
}

```

## nginx variables
* 2 types of variables
  -  **Configuration Variables:** set $var 'some';
  -  **Nginx Module Variables:** $http,$uri,$args etc
