---
layout: tutorial
title: Deploy website via Github webhook
---
## Getting started with Github webhook

* ### Introduction 💻

  Git is your best friend when you want to keep track of your source code. Github provides a git repository for those source code which allows you to store, manage, and collaborate within your team. Github can send a hook to any HTTP endpoint when certain events occur.  For example when you push a new version into the repository, Github will send a notification to your endpoint signaling your deployment to pull the source code again. 

  We have created a docker image which acts as a reverse proxy to your website and writen a webhook endpoint which can accept hooks from Github. This image will keep your website content aligned with the version in git.
 
* ### How to start your static website ?

 1. Firstly, create `new workload` and choose `Stateful Set` 
    <p align="left">
      <img src="/assets/git_webhook/statefull.png">
    </p>
 2. Fill `Stateful Set Name` and `Service Name`.
     <br><br>
     <img src="/assets/git_webhook/stateful2.png">
     <br><br>
 3. Create new container that's have [webhook](https://hub.docker.com/r/maxoatzadn/webhook) image inside.
    <br>
    `Container Name`: <"Your container name"> <br>
    `Image Name`: "maxoatzadn/webhook" <br>
    `Ports`:<br>
     &nbsp; &nbsp; &nbsp; &nbsp;-`Name`: <"Name container port"><br>
     &nbsp; &nbsp; &nbsp; &nbsp;-`Port`: "80"
    
    > Hint: Port 80 is default port of nginx.
    
    ![container1](/assets/git_webhook/container1.png)
    
    <br>
    
 4. Fill Enviroment Varible with your [GitHub](https://github.com/) source code repository for website's content which will be cloned into the container.
    <br>
     `Environment Variable`:<br>
     &nbsp; &nbsp; &nbsp; &nbsp;-`Key`: "LOCATION"<br>
     &nbsp; &nbsp; &nbsp; &nbsp;-`Value`: <"Your project on github"> <br>
     &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;(eg. https://github.com/toptapznt/nginx-test.git)
    <br><br>
    ![container2](/assets/git_webhook/container2.png)
  
    <br>
    
 5. Create new service that's target to your container and select service name that's same as previous part. <br>
    `Service Name` : <"Your service name"> <br>
     select : `Label` <br>
    `Selector`: select container that's your create.<br>
    `Ports`:<br>
    &nbsp; &nbsp; &nbsp; &nbsp;-`Name`: <"Name service port"><br>
    &nbsp; &nbsp; &nbsp; &nbsp;-`Service Port`: "80" <br><br>
    ![service1](/assets/git_webhook/service1.png)
    
    <br>
    
 6. Create new volume, type `Config Map`. <br>
    `Volume Name`: <"Your Volume name"> <br>
    `ConfigMap Name`: <"Your ConfigMap name"><br>
    then, click **Add New Config Map**. <br><br>
    ![volume3](/assets/git_webhook/voulme3.png)
        
 7. Insert new ConfigMap data that's for nginx config file. <br>
    `Key`: "default.conf" <br>
    `Content`: you can use content of default.conf file below. <br><br>
    > Given `root` and `index` are root contents of your app (html) from [GitHub](https://github.com/) for nginx. 
    
    ```
    # default.conf
    server {
    listen       80;
    server_name  localhost;

    location / {
        root   /App;
        index  index.nginx-debian.html;
    }
    
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/local/openresty/nginx/html;
    }

    location /_hook {
        default_type text/html;
        content_by_lua_block {
            os.execute("/bin/webhook.sh")
            ngx.say("<p>pull complete !!</p>")
        } 
      }
    }
    ```
    <br>
    
    ![volume4](/assets/git_webhook/voulme4.png)
    
    <br>
    
 8. Back to Stateful Set, Add new volume mounts for mounts default.conf from configMap to `/etc/nginx/conf.d` . <br>
    `Volume Name`: <"select your configMap name"> <br>
    `Mount Point`: "/etc/nginx/conf.d" <br>

    ![volume6](/assets/git_webhook/volume6.png) <br>
    
    <br>
    
 9. Click **Deploy**. <br>
    ![deploy](/assets/git_webhook/deploy.png) <br>
    <br>
 10. Now we got new workload that's contain webhook container inside pod.
     <br><br>
    ![workload](/assets/git_webhook/workload.png) <br><br><br>
 11. Scroll down to services and click link for view your web page has deploy.
    <br><br>
    ![deployment1](/assets/git_webhook/deployment1.png) <br><br>
 
 * ### Setting Git Webhook 
 
 1. Go to your project on [GitHub](https://github.com/), then click **setting** select **webhook** menu and click **Add webhook**. <br><br>
    ![hook1](/assets/git_webhook/hook1.png) <br><br><br>
 2. Change `Payload URL` to your domain name and append with `/_hook`.<br>
    Select `Content type` to `application/json`.<br>
    Select `Just the push event.` for git request to trigger your website and pull contents when every push event of your project. <br>
    Click **Add webhook**. <br><br>
    ![hook2](/assets/git_webhook/hook2.png) <br><br>
 3. Now, you complete to set webhook. <br><br>
    ![hook3](/assets/git_webhook/hook3.png) <br><br><br>
 4. Test git webhook by change content project and push. <br>
 
  ```
    <!DOCTYPE html>
    <html>
    <head>
    <title>Sawadee to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Hello World !</h1>
    </body>
    </html>
 ```
   <br>
   Now, Content of your website will change to below picture. <br><br>
   
   ![hello](/assets/git_webhook/hello.png) <br><br>
   
   <br>
   
   * ### Start new PHP Web Application container <br>
   In this tutorial will show how to deploy PHP-Apache that's connect with git webhook. <br><br>
   
   1. Click **Edit** your workload. <br><br>
      ![clickEdit](/assets/apache/clickEdit.png) <br><br><br>
   2. Add your PHP Application image by click on **Add New Container**. Then fill `Container Name`, `Image Name` and set port of your application will listen to in this tutorial will be 8000. <br>
      `Container Name`: <"your container name"> <br>
      `Image Name`: "maxoatzadn/php-server" <br>
      (This's php-apache image running on port 8000)<br><br>
      > **Warning!**   Webhook base on nginx running on port 80 and care about web server port make sure your application is not running on port 80 too. In this tutorial we use modify image from php:7.2-apache.
      
      <br>
      
      ![addContainerPhp](/assets/apache/addContainerPhp.png) <br><br>
      
   3. Create new volume, type `Persistent Volume Claim`. which will hold your source code <br>
      `Volume Name`: <"Your PVC Volume name"> <br>
      `PVC Name`: <"Your PVC name"><br>
      then, click **Add New PVC**. <br><br>
      ![addNewVolume](/assets/apache/addNewVolume.png) <br><br>
   4. Select Storage Class. <br>
      `Storage Class`: "rbd-r2" <br><br>
      ![storageClass](/assets/apache/storageClass.png) <br><br><br>
   5. We will make previously created PVC available on both webhook container and php application container. In Stateful Set menu, click **Add New Volume Mount** on each container to create a mount point of webhook container and web server container (php). Same volume content will available between two container. <br><br>
      * **Mount of webhook** <br>
      `Volume Name`: <"select your PVC name"> <br>
      `Mount Point`: "/App" <br>
      ![mountWebhook](/assets/apache/mountWebhook.png) <br><br><br>
      * **Mount of web server** <br>
      `Volume Name`: <"select your PVC name"> <br>
      `Mount Point`: "/var/www/html" <br>
      ![mountPhp](/assets/apache/mountPhp.png) <br><br>
   6. For next, lets open port 8000 of service. <br><br>
      ![editServices](/assets/apache/editServices.png) <br><br><br>
   7. Go to **Config Map** change code to below. <br><br>
       ```
        # default.conf
        server {
        listen       80;
        server_name  localhost;

        location / {
           # root   /App;
           # index  index.nginx-debian.html;
           # add proxy_pass to web server that's running at port 8000.
           proxy_pass http://localhost:8000;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/local/openresty/nginx/html;
        }

        location /_hook {
            default_type text/html;
            content_by_lua_block {
                os.execute("/bin/webhook.sh")
                ngx.say("<p>pull complete !!</p>")
            } 
          }
        }
      ```
      
   8. **Save Workload** <br>
   
   9. Click link service to view your website. <br><br>
      ![clickLinkService](/assets/apache/clickLinkService.png) <br><br>
   10. push index.php to your [GitHub](https://github.com/). <br>
   
        ```
          # index.php
          <?php
          echo phpinfo();
          ?>
       ```
      
       Now, you can see content of your website.
       
       ![phpWeb](/assets/apache/phpWeb.png) <br><br>

   
   Reference : https://hub.docker.com/r/maxoatzadn/webhook
   <br>
   
Good luck ... ✌️
