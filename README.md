# Build a docker image to launch an Nginx container for web hosting
![doc ngx](https://user-images.githubusercontent.com/104782642/219571537-630be4bc-efe6-41d6-b888-3db819bc0202.jpg)

### Our Scenerio
The company has requested to have it's landing page dockerized. 
This means we have to add the existing website files when creating the docker image which will be used to launch the Nginx container. Hence for our scenerio we must 
* Use Alpine Linux as the base image - since this is more light weight than Ubuntu 
* Build the image using Nginx web server
* Use the existing website files and Nginx configuration

To accomplish this, we follow the steps to below:
1. Write the Dockerfile - The dockerfile will define the parent image, install Nginx, and move the configuration and website files to the desired location.
2. Build the image - Build an image using the Dockerfile, tag the image with the name *web*.
3. Launch a *web01* container using the new image, map port 80 on the container to port 80 on the host. 
4. Test the configuration

**Prerequisites**
* Docker Installation The steps to install docker can be gotten from here: https://docs.docker.com/engine/install/
* Website files


#### Step 1: Write the Dockerfile
![2 default conf](https://user-images.githubusercontent.com/104782642/219576219-b0c9a256-5a91-4ee8-8421-90af32c305c9.JPG)

In my home directory, I have the containerhub/files directory which contains the *default.conf* file and the html directory which contains the web files
From the *default.conf* you can see that directory we need to ensure our website files end up at is */var/www/html/*. and of course with 80.
To create the Dockerfile we use the vim command 

```$ vim Dockerfile```

![3 dockerfile](https://user-images.githubusercontent.com/104782642/219577844-f9f4cbfb-afed-42d0-be2b-cdb695149d37.JPG)

```FROM alpine:3.13```  - The base image </br>
```RUN apk upgrade```   - Update the base image </br>
```RUN apk add nginx``` - Install Nginx </br>
```COPY files/default.conf /etc/nginx/conf.d/default.conf``` - Copy the *default.conf* file to replace the existing one </br>
```RUN mkdir -p /var/www/html``` - Create the directory where the website files will exist </br>
```WORKDIR /var/www/html``` - Assign as the desired work directory </br>
```COPY --chown=nginx:nginx/files/html/ .```  - Copy the website files to work directory and ensure nginx has the right permission to access the files </br>
```EXPOSE 80``` -Ensure Port 80 is open </br>
```CMD``` [ "nginx", "-g", ""pid /tmp/nginx.pid; daemon off"] - Run the command for formatting and starting Nginx

