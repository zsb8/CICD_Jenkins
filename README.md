
![1677953660422](https://user-images.githubusercontent.com/75282285/222922507-253adba3-1a58-43bf-9eb3-dbdfb82a6b77.jpeg)

# Project Name: Jenkins CICD Pipeline
My project setuped a Jenkins CICD pipeline, which pulled the source code from github and re-deployed the docker container in local host.

# Project Goal
Setup/configure Jenkins as CICD pipeline. Familiarize with Jenkinsfile.

# Table of Contents
1. [Prerequisites](#prerequisites)
2. [Project Steps](#project_steps)
3. [Troubleshooting](#troubleshooting)
4. [Reference](#reference)

# Prerequisites  <a name="prerequisites"></a>
- Ubuntu 20.04 OS
- Docker
- Docker Compose

# Project Steps <a name="project_steps"></a>
1. Deploy a Jenkins docker container with **docker-compose**
```
git clone https://github.com/zsb8/Devops.git
cd devops/002-JenkinsCICD
docker-compose up -d
```
![image](https://user-images.githubusercontent.com/75282285/197062611-663e9413-4f07-4da2-b43b-e573ae691240.png)

2. Open my **browser** and go to the Jenkins UI website (http://192.168.1.164). If install the Jenkins in local host, you can go [here](http://127.0.0.1). Login to the Jenkins with the username/password defined in your `docker-compose.yaml`.     
![image](https://user-images.githubusercontent.com/75282285/197063165-7085353f-183d-499a-b66a-cb317462cea2.png)


3. Go to **"Manage Jenkins"** in the left lane and go to **"Manage Plugins"**. Click **"Available"** tab in the **"Plugin Manager"** page and type **"pipeline"** in the search bar. Select **"Pipeline"** and click **"Download now and install after restart"**.       
![image](https://user-images.githubusercontent.com/75282285/197073805-5b7cdf88-530b-42dd-9e5f-c90b856f1b92.png)


4. Follow the same step above and install **"git"** and **"Docker Pipeline"** as plugins.      
 ![image](https://user-images.githubusercontent.com/75282285/197074173-a4e3c580-1c95-4cd5-afe0-161508e1d3a7.png)
![image](https://user-images.githubusercontent.com/75282285/197074358-9b155a71-32d9-4735-9c54-aabc3be26bec.png)
![image](https://user-images.githubusercontent.com/75282285/197074417-d3772790-31a7-4c6b-9226-f3958fd91d27.png)
check it       
![image](https://user-images.githubusercontent.com/75282285/197074612-b47d2086-fdb6-4568-9f32-8655cbd32e5d.png)


5. Once the plugin is installed and the Jenkins is restarted, go to the main page of the Jenkins website and click **"New Item"** in the left. Type the name of your project (i.g. first-project) and select **"Pipeline"** and click **"OK"**. In the Configuration page, make sure below fields in **"Pipeline"** section are filled:
![image](https://user-images.githubusercontent.com/75282285/197074925-52498c66-cccd-4a6b-bfb4-ff72dce09a77.png)

    a. **"Definition"**: select "Pipeline script from SCM" 
![image](https://user-images.githubusercontent.com/75282285/197089896-2f3801a6-94b1-47ea-a428-cc1c01aaf5d3.png)

    b. **"SCM"**: select "Git" 

    c. **"Repository URL"**: Enter the URL of the repo which has Jenkinsfile, for example, [https://github.com/zsb8/devopsdaydayup](https://github.com/zsb8/devopsdaydayup). (Note: You can fork "devopsdaydayup" repo to your github account and enter the URL accordingly)

    d. **"Credentials"**: If you don't have any credential, click "Add" -> "Jenkins", in "Kind" field select "Username with password". In "Scope" field select "Global(Jenkins, nodes, items, all child items, etc)". In "Username", type your github account username. In "Password", type your github account token. 
    Notes! The "Password" is not your Github password, it really is your token which you set in your Github account and has the time expiration.
    ![image](https://user-images.githubusercontent.com/75282285/197088182-180504e5-8ea9-40aa-9b24-247c21014ab8.png)


    **Note**: In order to get a github token, you can go to your github account and select "Setting" once you click your account icon in the top right. Go to "Developer settings" in the very bottom left lane and go to "Personal access tokens", and then click "Generate new token" to create a new token for above step. The permission, you choose repo and write permission at least.     
![image](https://user-images.githubusercontent.com/75282285/197076823-0bc35501-6a42-4937-a5e2-172eb4b358af.png)
Notes! Then, I need to go to my repo(such as https://github.com/zsb8/Devops.git), click the `Settings`, then click `Secrets`, into `Actions secrets`, click `New repository secret` buttom. Past your token on the 'Secret' form.
![image](https://user-images.githubusercontent.com/75282285/197086704-fb7b7d12-93a7-477c-9cea-1bbcb8f6d433.png)


    e. **"ID"**: Enter the name of this credential, which will be referred in the Pipeline later. For example, zsb8token.

    f. **"Branch Specifier (blank for 'any')"**: Enter the branch which has your Jenkinsfile, for example, `*/main`

    g. **"Script Path"**: Enter the relative path for the Jenkinsfile in your repo, for example, 002-JenkinsCICD/Jenkinsfile

    h. Unselect **"Lightweight checkout"**

![image](https://user-images.githubusercontent.com/75282285/197087433-27680006-3675-4c3f-a714-2a422d940fa9.png)

Save above change.

Then go to the code file (002-JenkinsCICD/Jenkinsfile), update it to the same items as you set.
![image](https://user-images.githubusercontent.com/75282285/197087862-754cb126-4ea0-436a-ba8e-dfc5650f9b04.png)


6. Run a container **before** trigger your pipeline
```
docker build -t color-web:init .
docker run -d -p 8080:8080 --name color-web color-web:init
```
![image](https://user-images.githubusercontent.com/75282285/197077813-6c572a99-7ef6-4eed-a5a5-e8c9b69989bf.png)

7. Click **"Build"** to trigger your first pipeline
> Note: If you encounter with any docker execute permission issue, you may need to run below command to apply a proper permission on `docker.sock` 
```
docker exec <jenkinsContainerID> chmod 777 /var/run/docker.sock
```

8. You should be able to see the hello world page in [here](http://192.168.1.164:8080)
![image](https://user-images.githubusercontent.com/75282285/197078170-3a12f2d4-83a7-4375-bb40-e6c14b746eb3.png)


9. Make a change into `app.py`. For example, change "Hello world" to "Hello world 2". And then click "Build" again to trigger the pipeline to deploy the change. Once it is done, you should be able to see your change in [here](http://localhost:8080)
![image](https://user-images.githubusercontent.com/75282285/197089040-180b9f4d-fbad-4904-a9b0-d92a85b49266.png)

![image](https://user-images.githubusercontent.com/75282285/197088969-cf2f649c-f58f-486a-bb1c-53d01007dcee.png)

# Troubleshooting <a name="troubleshooting"></a>
## Issue 1:  No such property: docker for class
When you run the pipeline, it fails with below error:
```
groovy.lang.MissingPropertyException: No such property: docker for class: groovy.lang.Binding
	at groovy.lang.Binding.getVariable(Binding.java:63)
	at org.jenkinsci.plugins.scriptsecurity.sandbox.groovy.SandboxInterceptor.onGetProperty(SandboxInterceptor.java:251)
	at org.kohsuke.groovy.sandbox.impl.Checker$7.call(Checker.java:353)
	at org.kohsuke.groovy.sandbox.impl.Checker.checkedGetProperty(Checker.java:357)
	at org.kohsuke.groovy.sandbox.impl.Checker.checkedGetProperty(Checker.java:333)
	at org.kohsuke.groovy.sandbox.impl.Checker.checkedGetProperty(Checker.java:333)
	at org.kohsuke.groovy.sandbox.impl.Checker.checkedGetProperty(Checker.java:333)
	at com.cloudbees.groovy.cps.sandbox.SandboxInvoker.getProperty(SandboxInvoker.java:29)
	at com.cloudbees.groovy.cps.impl.PropertyAccessBlock.rawGet(PropertyAccessBlock.java:20)
	at WorkflowScript.run(WorkflowScript:16)
	at ___cps.transform___(Native Method)
	at com.cloudbees.groovy.cps.impl.PropertyishBlock$ContinuationImpl.get(PropertyishBlock.java:74)
	at com.cloudbees.groovy.cps.LValueBlock$GetAdapter.receive(LValueBlock.java:30)
	at com.cloudbees.groovy.cps.impl.PropertyishBlock$ContinuationImpl.fixName(PropertyishBlock.java:66)
	at jdk.internal.reflect.GeneratedMethodAccessor118.invoke(Unknown Source)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at com.cloudbees.groovy.cps.impl.ContinuationPtr$ContinuationImpl.receive(ContinuationPtr.java:72)
	at com.cloudbees.groovy.cps.impl.ConstantBlock.eval(ConstantBlock.java:21)
	at com.cloudbees.groovy.cps.Next.step(Next.java:83)
	at com.cloudbees.groovy.cps.Continuable$1.call(Continuable.java:174)
	at com.cloudbees.groovy.cps.Continuable$1.call(Continuable.java:163)
	at org.codehaus.groovy.runtime.GroovyCategorySupport$ThreadCategoryInfo.use(GroovyCategorySupport.java:136)
	at org.codehaus.groovy.runtime.GroovyCategorySupport.use(GroovyCategorySupport.java:275)
	at com.cloudbees.groovy.cps.Continuable.run0(Continuable.java:163)
	at org.jenkinsci.plugins.workflow.cps.SandboxContinuable.access$001(SandboxContinuable.java:18)
	at org.jenkinsci.plugins.workflow.cps.SandboxContinuable.run0(SandboxContinuable.java:51)
	at org.jenkinsci.plugins.workflow.cps.CpsThread.runNextChunk(CpsThread.java:187)
	at org.jenkinsci.plugins.workflow.cps.CpsThreadGroup.run(CpsThreadGroup.java:420)
	at org.jenkinsci.plugins.workflow.cps.CpsThreadGroup.access$400(CpsThreadGroup.java:95)
	at org.jenkinsci.plugins.workflow.cps.CpsThreadGroup$2.call(CpsThreadGroup.java:330)
	at org.jenkinsci.plugins.workflow.cps.CpsThreadGroup$2.call(CpsThreadGroup.java:294)
	at org.jenkinsci.plugins.workflow.cps.CpsVmExecutorService$2.call(CpsVmExecutorService.java:67)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at hudson.remoting.SingleLaneExecutorService$1.run(SingleLaneExecutorService.java:139)
	at jenkins.util.ContextResettingExecutorService$1.run(ContextResettingExecutorService.java:30)
	at jenkins.security.ImpersonatingExecutorService$1.run(ImpersonatingExecutorService.java:70)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
	at java.base/java.lang.Thread.run(Thread.java:829)
Finished: FAILURE
```
**Solution:**
Make sure "Docker-Pipeline" plugin is installed

## Issue 2: Error response from daemon: No such container: color-web
When running the pipeline, below error is showed
```
+ docker stop color-web
Error response from daemon: No such container: color-web
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Clean Up)
Stage "Clean Up" skipped due to earlier failure(s)
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
ERROR: script returned exit code 1
Finished: FAILURE
```

**Solution:**
Make sure the `color-web` container is running
```
docker run -p 8080:8080 --name color-web color-web:init
```
## Issue 3: Cannot run docker inside of Jenkins pipeline job container

**Solution:**
Run below command to grant access to `docker.sock` for the Jenkins pipeline job container
```
docker exec <jenkinsContainerID> chmod 777 /var/run/docker.sock
```

## Issue 4:  Jenkins error buildind : docker: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.32' not found (required by docker)
When running Jenkins pipeline to build docker image, it shows below error
```
Jenkins error buildind : docker: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.32' not found (required by docker)
```
**Cause:**
The issue will occur when you are using Ubuntu 22.02 (Ubuntu 20.02 is working fine). you’ve having shared-library issues because you’re injecting a docker binary that depends on a different version of GNU libc than exists in the image.

**Solution:**
You can build a customed image with seperated docker component/lib installed and then use this image for your Jenkins deployment.

Dockerfile
```
FROM jenkins/jenkins:lts-jdk17
USER root
RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive \
    apt-get install --no-install-recommends --assume-yes \
      docker.io
USER jenkins
```

docker-compose.yaml
```
version: '3.8'
services:
  jenkins-container:
    build: .
    # no image:
    volumes:
      - ./data:/var/jenkins_home
      # but not /usr/bin/docker
```

> Refer to https://jtuto.com/getting-glibc_2-32-and-glibc_2-34-not-found-in-jenkins-docker-with-dind-on-ubuntu-22-04/

# Reference <a name="reference"></a>
https://www.jenkins.io/doc/book/pipeline/

https://www.jenkins.io/doc/book/pipeline/docker/

https://hub.docker.com/r/bitnami/jenkins/

https://www.jenkins.io/doc/book/pipeline/getting-started/

https://www.jenkins.io/doc/book/pipeline/jenkinsfile/
