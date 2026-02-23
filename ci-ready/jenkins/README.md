#Afhultin
Jenkins Setup

The Jenkinsfile in this folder is the pipeline config. It goes in the root of the repo and tells Jenkins what to do when it builds (compile, test, docker build).


Installing Jenkins

We recommend running Jenkins inside an Ubuntu Server VM. This keeps it isolated from whatever machine is hosting it and gives us a Linux environment which is what most CI servers run in production.

VM setup:
- Mac: use UTM, download the Ubuntu Server ARM64 ISO from https://ubuntu.com/download/server/arm
- Windows: use VirtualBox (https://www.virtualbox.org/wiki/Downloads), download the Ubuntu Server amd64 ISO from https://ubuntu.com/download/server
- Create the VM with 4 GB RAM, 2 CPUs, 30 GB disk
- Install Ubuntu with all the defaults, just make sure to check "Install OpenSSH server" when it asks
- On VirtualBox set networking to Bridged Adapter so the VM gets its own IP on the network

Once Ubuntu is installed, log in and run ip addr to find the VM's IP address. Then SSH in from your regular machine (ssh username@ip) so you can copy paste commands easier.

Install Java, Maven, Docker, and Jenkins by running these in order:

sudo apt update && sudo apt upgrade -y
sudo apt install -y openjdk-17-jdk maven docker.io
sudo usermod -aG docker $USER

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install -y jenkins
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

After that Jenkins should be running. Open http://(IP OF THE VIRTUAL MACHINE):8080 in your browser to check.


Setting up Jenkins

1. It'll ask for an admin password. Get it by running this in the VM:
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword

2. Paste it in, click Install suggested plugins, wait for those to finish, then create an admin user.

3. Configure the build tools so Jenkins knows where Java and Maven are. Go to Manage Jenkins then Tools:
   - Add JDK: name it JDK-17, uncheck Install automatically
     JAVA_HOME: /usr/lib/jvm/java-17-openjdk-arm64 (Mac VM) or /usr/lib/jvm/java-17-openjdk-amd64 (Windows VM)
   - Add Maven: name it Maven-3.9, uncheck Install automatically
     MAVEN_HOME: /usr/share/maven
   - Save. The names here have to match what's in the Jenkinsfile or it won't work.

4. Create the pipeline job that watches our repo:
   - Go to New Item, name it DevOpps-CI, pick Pipeline, click OK
   - Under Build Triggers check Poll SCM, set the schedule to H/2 * * * * (this makes Jenkins check GitHub for new commits every 2 minutes)
   - Under Pipeline set it to Pipeline script from SCM, pick Git
     Repo URL: https://github.com/2026-Spring-A451-Wolfe/DevOpps.git
     Branch: */main
     Script Path: Jenkinsfile
   - Save

5. Click Build Now to test it. You can click the build number then Console Output to watch it run. You should see the compile, tests, and docker build all pass.

After that first test Jenkins will automatically pick up new commits every 2 minutes and run the pipeline on its own. The machine hosting the VM just needs to stay on.


Useful commands

Start/stop/restart Jenkins:   sudo systemctl start/stop/restart jenkins
Check Jenkins logs:           sudo journalctl -u jenkins -f
Jenkins URL:                  http://<vm-ip>:8080
SSH into VM:                  ssh username@<vm-ip>
