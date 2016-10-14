---
layout: post
title: Configuring network of z/OS v10 running on Hercules (MAC OS)
---

After some days of trying, I was able to ping z/OS running on Hercules! Hurray!

[![Bitfhacker]({{ site.baseurl }}/images/dino.jpg)](https://bitfhacker.github.io/)

# Here are the steps I took.

First of all, keep in mind that **SYS1.PROCLIB** is the standard *system procedure JCL*, but in ADCD (z/OS v10) the corresponding PROCLIB is **ADCD.Z110.PROCLIB**.

To configure the network, you must have a profile. To keep things simple and straight, browse the file SYS1.PROCLIB(TCPIP) and find the line with //PROFILE. It's something like this:

    //PROFILE  DD DISP=SHR,DSN=SYS1.TCPPARMS(PROF1)

Browse to SYS1.TCPPARMS (ADCD.Z110.TCPPARMS) and create a new file named MYPROF using PROF1 as source - that's where we will configure the network! Then change the above line to:

    //PROFILE  DD DISP=SHR,DSN=SYS1.TCPPARMS(MYPROF)

The rest of configuration, use the [good tips] of Soldier of Fortran. 

There is a typo in your SYS1.TCPPARMS(PROFILE), Soldier of Fortran! You need to break a line between E20 and LINK, like this: 

    DEVICE CTCA1 CTC E20
    LINK CTC1 CTC 1 CTCA1
    HOME
      192.168.1.200 CTC1
    GATEWAY
      192.168.1.1 = CTC1 1492 HOST
    DEFAULTNET 192.168.1.13 CTC1 1492 0
    START CTCA1



> **You can go to jail warning**: Keep in mind that using ADCD without license, can get you in trouble


### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [markdown-it] - Markdown parser done right. Fast and easy to extend.
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [keymaster.js] - awesome keyboard handler lib by [@thomasfuchs]
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

### Installation

Dillinger requires [Node.js](https://nodejs.org/) v4+ to run.

Download and extract the [latest pre-built release](https://github.com/joemccann/dillinger/releases).

Install the dependencies and devDependencies and start the server.

```sh
$ cd dillinger
$ npm install -d
$ node app
```

For production environments...

```sh
$ npm install --production
$ npm run predeploy
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins

* Dropbox
* Github
* Google Drive
* OneDrive

Readmes, how to use them in your own application can be found here:

* [plugins/dropbox/README.md] [PlDb]
* [plugins/github/README.md] [PlGh]
* [plugins/googledrive/README.md] [PlGd]
* [plugins/onedrive/README.md] [PlOd]


### Docker
Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 80, so change this within the Dockerfile if necessary. When ready, simply use the Dockerfile to build the image.

```sh
cd dillinger
npm run-script build-docker
```
This will create the dillinger image and pull in the necessary dependencies. Moreover, this uses a _hack_ to get a more optimized `npm` build by copying the dependencies over and only installing when the `package.json` itself has changed.  Look inside the `package.json` and the `Dockerfile` for more details on how this works.

Once done, run the Docker image and map the port to whatever you wish on your host. In this example, we simply map port 8000 of the host to port 80 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart="always" <youruser>/dillinger:latest
```

Verify the deployment by navigating to your server address in your preferred browser.

```sh
127.0.0.1:8000
```

#### Kubernetes + Google Cloud

See [KUBERNETES.md](https://github.com/joemccann/dillinger/blob/master/KUBERNETES.md)




   [good tips]: <http://mainframed767.tumblr.com/post/114411700159/hercules-on-mac-os-x-yosemite>
