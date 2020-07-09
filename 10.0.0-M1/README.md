# Apache Tomcat 10.0.0-M1 on RedHat UBI 8.1

Docker container: Red Hat Ubi 8.1 + Java 13 + Apache Tomcat 10.0.0-M1

## Important Notes
- I used kirillF's [centos-tomcat repo](https://github.com/kirillF/centos-tomcat) as my starting point and then altered it so it could run Apache Tomcat 10.0.0-M1 instead of 9.0.31. I wanted ot put this up near the top so it wouldn't get missed. Thanks KirillF for your awesome work that helped me figure this out. Playing with your container did a lot to help me learn more about containers.
- Kept their *create-admin-user.sh* script because it rocks.

## Build the image

```sh
git clone https://github.com/corgidev/ubi8-tomcat.git
cd ubi8-tomcat
docker build -t corgidev/ubi8-tomcat .
```

## How to use
Put your war under the `/opt/tomcat/webapps` directory and run the following command.

```sh
docker run -v /opt/tomcat/webapps:/opt/tomcat/webapps -v /opt/tomcat/logs:/opt/tomcat/logs -p 8080:8080 -it --name ubi8-tomcat corgidev/ubi8-tomcat
```

Once you run it, you can start the container with `docker start ubi8-tomcat` in next time and log file will be under the `/opt/tomcat/logs` directory.

Also, if you got some error, you can remove the container with `docker rm ubi8-tomcat`. Your current container list will be show with `docker ps -a`.

For Mac user, you must share the directory `/opt/tomcat/webapps` and `/opt/tomcat/logs` on Docker > Preferences > File Sharing.

### Run it locally
`docker run -it --name ubi8-tomcat -p 8080:8080/tcp name:tag`

## Create Admin User


## Versions
If you got error while build the docker image, please check the latest version of Java and Tomcat.

|Software|Version|Note|
|:-----------|:------------|:------------|
|Red Hat UBI|8.1|[Red Hat Universal Base Images](https://access.redhat.com/articles/4238681)|
|Java|13.0.2|[Java Release Note](https://www.oracle.com/technetwork/java/javase/12-0-2-relnotes-5480112.html)|
|Apache Tomcat|10.0.0-M1|[Tomcat Download Page](https://downloads.apache.org/tomcat/tomcat-10/v10.0.0-M1/)|

[Docker Official Image for Tomcat](https://github.com/docker-library/tomcat) is also available.
