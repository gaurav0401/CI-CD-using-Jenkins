<p>Jenkins is an automation platform which allows us to build , test and deploy software using pipelines.</p>

<h3>Jenkins Infrastructures</h3>

<ul>
    <p>It consists of:</p>
    <li><b>Master Server:</b>It is responsible to controls pipelines and schedules builds.</li>
    <li><b>Agents/Minions:</b>An agent is a service that runs the jobs/builds defined in our pipeline.</li>
    <li>There are two type of agents:
        <ul>
            <li><b>Permanent agents: </b>They are dedicated servers for running jobs.</li>
            <li><b>Cloud agents: </b>
                <ul>
                    <li> In Jenkins, a cloud agent is a Jenkins agent that is launched dynamically in a cloud
                        environment.</li>
                    <li> Cloud agents are useful when you need to scale your build capacity up or down based on demand .
                    </li>
                </ul>
            </li>
        </ul>
    </li>
</ul>


<ul>
    <li><b>Freestyle builds:</b>are the simplest method to create a build.A freestyle project in Jenkins is a type of
        build job that allows users to automate simple tasks such as running tests, creating and packaging applications,
        producing reports, or executing commands .</li>
    <li><b>Pipelines:</b>are the set of automated tasks which helps in to develop and deploy software faster. </li>
</ul>



# Installation
## Build the Jenkins BlueOcean Docker Image (or pull and use the one I built)
```
docker build -t myjenkins-blueocean:2.414.2 .

#IF you are having problems building the image yourself, you can pull from my registry (It is version 2.332.3-1 though,
the original from the video)

docker pull jenkins/jenkins
```

## Create the network 'jenkins'
```
docker network create jenkins
```



## Run the Container

### Windows
```
docker run --name jenkins-blueocean --restart=on-failure --detach `
--network jenkins --env DOCKER_HOST=tcp://docker:2376 `
--env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
--volume jenkins-data:/var/jenkins_home `
--volume jenkins-docker-certs:/certs/client:ro `
--publish 8080:8080 --publish 50000:50000 jenkins/jenkins
```

<p>Note:Now run run 'docker ps' you will see our jenkins container is running there on port localhost:8080</p>

## Get the Password
```
docker exec 'jenkin container name ' cat /var/jenkins_home/secrets/initialAdminPassword
```

## Connect to the Jenkins
```
https://localhost:8080/
```


<h3>Jenkins commands</h3>
<ul>
    <li><b> docker exec -it 'container name' bash :</b>used to run Jenkins workspace in bash mode.</li>
    <li><b>cd /var/jenkins_home :</b> used to move to the jenkin home page.</li>
    <li><b>cd ~ :</b> go back to the home directory</li>
</ul>



<h3>python Installation in jenkins</h3>
<ul>
    <li>docker exec -u 0 'container name' apt update</li>
    <li>docker exec -u 0 'container name' apt install python3-pip -y</li>
</ul>
## Installation Reference:
https://www.jenkins.io/doc/book/installing/docker/



<h3>Creating agents in Jenkins</h3>

<ul>
    <li>Install any cloud plugin example docker plugin .</li>
    <li>Go to manage jenkins-> System Configuration -> Add cloud -> Choose cloud plugin (i.e docker in our case).</li>
    <li>If you are running jenkins as container, in the docker host uri field you have to enter unix or tcp address of
        the docker host. But since you are running jenkins as container, the container can't reach docker host unix
        port.</li>
    <li>So, we have to run another container that can mediate between docker host and jenkins container. It will public
        docker host's unix port as its tcp port. Follow the instructions to create socat container
        https://hub.docker.com/r/alpine/socat/</li>
    <li>
        <b>alpine/socat container to forward traffic from Jenkins to Docker Desktop on Host Machine</b>
        <ul>
            <li> docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.so alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock</li>
            <li> docker inspect 'container_id' | grep IPAddress</li>
        </ul>
     </li>
    <li>After creating the socat container, you can go back the docker configuration in jenkins and enter
        tcp://socat-container-ip:2375</li>
    <li><b>Note: Make sure that you have enabled CSRF Protection from global configuration.</b></li>
    <li>Test Connection should succeed now.</li>
</ul>


<h3>Creating pipelines</h3>
<ul>
    <li>There 2  ways to run pipeline in Jenkins:
        <ol>
            <li>By writing pipeline script in pipeline configuration.</li>
            <li>By creating seperate 'Jekinfile' and then adding that file in pipeline configuration using git.</li>
        </ol>
    </li>
</ul>