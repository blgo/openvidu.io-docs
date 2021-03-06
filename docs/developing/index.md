
Developing OpenVidu
===================

| Dependecy     | Check version   | Install                               |
| ------------- | --------------- |-------------------------------------- |
| java 8 JDK    | `java -version` | `sudo apt-get install -y openjdk-8-jdk` |
| node          | `node -v`       | `sudo curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -`<br>`sudo apt-get install -y nodejs` |
| maven         | `mvn -v`        | `sudo apt-get install -y maven`       |
| angular-cli   | `ng -v`         | `sudo npm install -g @angular/cli`    |


Setup for development
------------------

Here we show how to develop an Angular app (_openvidu-testapp_) with ***openvidu-browser*** and ***openvidu-server*** as local dependencies, waiting to be modified as you want.

**1)** Run Kurento Media Server Docker container (`docker run -d -p 8888:8888 kurento/kurento-media-server:latest`) or [install and run KMS](#installing-kms) (only for Ubuntu 16.04)

**2)** Clone repo:

```bash
git clone https://github.com/OpenVidu/openvidu.git
```

**3)** `openvidu/openvidu-browser/`

```bash
npm install
npm run build
sudo npm link
```

**4)** `openvidu/`

```bash
mvn -DskipTests=true clean install
```

**5)** `openvidu/openvidu-testapp/`

```bash
npm install
npm link openvidu-browser
ng serve
```

**6)** `openvidu/openvidu-server/`

```bash
mvn exec:java
```

*(or if you prefer you can just run the Java application in your favourite IDE)*


----------


At these point, you can start modifying *openvidu-testapp*, *openvidu-browser* or *openvidu-server*.

 - **_openvidu-testapp_** :  the "ng serve" command will take care of refreshing the browser's page whenever any change takes place

 - **_openvidu-browser_** : after modifying any typescript file, you will need to run the following command to update your changes:
 
      */openvidu/openvidu-browser*:  `npm run build`

 - **_openvidu-server_** : after modifying any file, there is no other alternative but to re-launch the java application if you want to update your changes

      */openvidu/openvidu-server*: `mvn clean exec:java`

    *(or re-launch the Java application in your IDE. Some IDE's support automatic re-launch in response to changes)*

---

Setup for advanced development: share the app through your network
------------------
You can also use **different machines** in the **same network** to build a more advanced development environment, so you can test the application in different devices at the same time. It's very similar to the process outlined above:

Run exactly the same commands as the process above, but:

1. On step **5)** extend `ng serve` command with:

        ng serve --host 0.0.0.0 --ssl true

2. On step **6)** extend `mvn exec:java` command with:

        mvn -Dopenvidu.publicurl=https://HOST_LOCAL_IP:4443/ exec:java
        # Being HOST_LOCAL_IP the local IP that your machine serving the app has in your WiFi network

This way we first tell AngularCLI to serve our app through https and to expose the port in our WiFi network, and secondly we set OpenVidu Server public url to the public IP of the machine in our WiFi network. This way our devices will be able to reach it as long as they are connected to the same network.

To connect to the application from any device, be sure to be connected to the same WiFi network and navigate to `https://HOST_LOCAL_IP:4200` (being `HOST_LOCAL_IP` the local IP that your machine serving the app has in your WiFi network).

---

Installing KMS
------------------

How to *install* and *run* KMS in your development machine (Ubuntu 16.04):

```bash
sudo echo "deb [arch=amd64] http://ubuntu.openvidu.io/6.10.0 xenial kms6" | sudo tee /etc/apt/sources.list.d/kurento.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5AFA7A83
sudo apt-get update
sudo apt-get -y install kurento-media-server
```

Change the default user running KMS to your current user with this line:

```bash
sudo sed -i "s/DAEMON_USER=\"kurento\"/DAEMON_USER=\"${USER}\"/g" /etc/default/kurento-media-server
```

To start and stop KMS service:

```bash
sudo service kurento-media-server start
sudo service kurento-media-server stop
```

[Here](http://doc-kurento.readthedocs.io/en/stable/user/installation.html){:target="_blank"} you can check Kurento's official documentation.
