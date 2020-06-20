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
* If "C Compiler cc not found" appeares, run 
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
