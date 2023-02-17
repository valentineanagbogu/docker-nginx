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
3. Launch container - Launch a *web01* container using the new image, map port 80 on the container to port 80 on the host. 
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
```CMD [ "nginx", "-g", "pid /tmp/nginx.pid; daemon off;"]``` - Run the command for formatting and starting Nginx

#### Step 2: Build the image

To build the image use the command: </br>
```$ docker build . -t web``` </br>
This will build the image in the currentlly directory which contains the Dockerfile with a tagname *web*. </br>
Confirm the build by viewing a list of images </br>

![5 docker image](https://user-images.githubusercontent.com/104782642/219584943-a5e6462e-eea7-4f99-8ef6-bc4ea4f3a28e.JPG)

#### Step 3: Launch Container
To launch the container using the newly created *web* image: </br>
```$ docker run -dt -p 80:80 --name web01 web``` </br>
This ensure the container is detached and running in the background. The container port 80 is alos mapped to the host port 80. </br>
To make sure this is running we can go ahead and curl localhost; this will return the HTML files from the container.
```$ curl localhost```
![7 curl localhost](https://user-images.githubusercontent.com/104782642/219587191-59383bd2-921b-42b7-a210-033e1033dc2c.JPG)


We can test further by putting the IP address on our browser to see the webpage:

![8 container page](https://user-images.githubusercontent.com/104782642/219590740-351f0518-1a46-4666-9220-34ea1b2cb41c.JPG)

#### Conclusion
In summary we were able to:
1. Create a Dockerfile using alpine as the base image, alongside the necessary file and Nginx configuration
2. Build the docker image
3. And use the image to launch our container
4. Test the website by using the web browser to view our landing page.









