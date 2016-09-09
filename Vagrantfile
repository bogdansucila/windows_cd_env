# -*- mode: ruby -*-
# vi: set ft=ruby :

$script_tools = <<SCRIPT
echo "Start installing tools..."
#Allow all trafic trough ipotable
sudo echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
sudo sysctl -p
sudo yum -y install net-tools wget zip unzip mc vim gitepel-release epel-release rpm-build rpmdevtools git
echo "Install oracle jdk..."
#Install Oracle JDK
wget -nv \
--no-cookies \
--no-check-certificate \
--header "Cookie: oraclelicense=accept-securebackup-cookie" \
"http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.rpm" \
-O jdk-8u101-linux-x64.rpm
yum -y install jdk-8u101-linux-x64.rpm
rm -rf jdk-8u101-linux-x64.rpm
echo "Done."
SCRIPT

$script_jenkinsMaster = <<SCRIPT
echo "Install Jenkins Master on machine..."
#Installing jenkins
wget -nv -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
yum -y update
yum -y install jenkins
#Installing plugins on jenkins
mkdir -p /var/lib/jenkins/plugins/
wget -nv -O /var/lib/jenkins/plugins/scm-api.hpi https://updates.jenkins-ci.org/download/plugins/scm-api/latest/scm-api.hpi
wget -nv -O /var/lib/jenkins/plugins/git-client.hpi https://updates.jenkins-ci.org/download/plugins/git-client/1.11.1/git-client.hpi
wget -nv -O /var/lib/jenkins/plugins/git.hpi https://updates.jenkins-ci.org/download/plugins/git/2.3/git.hpi
wget -nv -O /var/lib/jenkins/plugins/ws-cleanup.hpi https://updates.jenkins-ci.org/download/plugins/ws-cleanup/0.24/ws-cleanup.hpi
wget -nv -O /var/lib/jenkins/plugins/token-macro.hpi https://updates.jenkins-ci.org/download/plugins/token-macro/1.10/token-macro.hpi
wget -nv -O /var/lib/jenkins/plugins/config-file-provider.hpi http://mirrors.xmission.com/hudson/plugins/config-file-provider/2.7.5/config-file-provider.hpi
wget -nv -O /var/lib/jenkins/plugins/swarm.hpi https://updates.jenkins-ci.org/latest/swarm.hpi
wget -nv -O /var/lib/jenkins/plugins/gradle.hpi https://updates.jenkins-ci.org/latest/gradle.hpi
sudo chown -R jenkins:jenkins /var/lib/jenkins/plugins/
#setting up Jenkins as a service&start it
chkconfig jenkins on
service jenkins restart
echo "Setting up jenkins user for gitlab..."
sed -i "s/jenkins:\/bin\/false/jenkins:\/bin\/bash/g" /etc/passwd
echo "All Done, jenkins master is installed."
SCRIPT


$script_windowsSlave = <<SCRIPT
#Install IIS + ASP.Net
Write-Host "Installing IIS and ASP.Net..."
#Installing IIS and ASP.NET

Import-Module ServerManager
Install-WindowsFeature Web-Server,Web-ASP-Net,Web-ASP-Net45

Write-Host "Installing IIS and ASP.Net Done..."

SCRIPT


$script_installJava = <<SCRIPT
$sourcex86 = 'http://javadl.sun.com/webapps/download/AutoDL?BundleId=79063' #Java 7 u 25 x86
$sourcex64 = 'http://javadl.sun.com/webapps/download/AutoDL?BundleId=79065' #Java 7 u 25 x64

$64bit = $false
if(Test-Path 'C:\Program Files (x86)' -PathType Container){
    $64bit = $true
}

try{
    if(Test-Path 'C:\Download\Java' -PathType Container){
        $destinationx86 ='C:\Download\Java\java7u25x86.exe'
        if($64bit){
            $destinationx64 ='C:\Download\Java\java7u25x64.exe'
        }
    }
    else{
        New-Item -Path 'C:\Download' -Name 'Java' -ItemType directory
        $destinationx86 ='C:\Download\Java\java7u25x86.exe'
        if($64bit){
            $destinationx64 ='C:\Download\Java\java7u25x64.exe'
        }
    }

    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($sourcex86, $destinationx86)
    if($64bit){
        $wc.DownloadFile($sourcex64, $destinationx64)
    }
}
catch{
    $Error[0]
    return (-1)
}


Start-Process -FilePath $destinationx86 -ArgumentList "/S /v /qn"
if($64bit){
    Start-Process -FilePath $destinationx64 -ArgumentList "/S /v /qn"
}
SCRIPT


$script_gitInstall = <<SCRIPT
echo "Installing Git..."
yum -y install curl openssh-server openssh-clients postfix cronie 
service postfix start
chkconfig postfix on
lokkit -s http -s ssh
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
yum -y install gitlab-ce
gitlab-ctl reconfigure
#Credentials: root/5iveL!fe
echo "Installing Git DONE..."
SCRIPT


$script_gradleInstall = <<SCRIPT
echo "Install Gradle to machine..."
gradle_version=2.11
wget -nv -O /opt/gradle-${gradle_version}-all.zip https://services.gradle.org/distributions/gradle-${gradle_version}-all.zip
unzip /opt/gradle-${gradle_version}-all.zip -d /opt/gradle
ln -s /opt/gradle/gradle-${gradle_version} /opt/gradle/latest
touch /etc/profile.d/gradle.sh
echo 'export GRADLE_HOME=/opt/gradle/latest' >> /etc/profile.d/gradle.sh
echo 'export PATH=$PATH:/opt/gradle/latest/bin' >> /etc/profile.d/gradle.sh
chmod +x /etc/profile.d/gradle.sh
. /etc/profile.d/gradle.sh
# check installation
gradle -v
rm -rf /opt/*.zip
echo "Gradle install finish..."
SCRIPT


$script_mvnInstall = <<SCRIPT
echo "Install Maven 3 to machine..."
cd /opt/
mkdir -p /opt/maven/
wget -nv -O /opt/apache-maven-3.3.9-bin.tar.gz http://mirrors.hostingromania.ro/apache.org/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
tar -xzf apache-maven-3.3.3-bin.tar.gz -C /opt/maven/
ln -s /opt/maven/apache-maven-3.3.9 /opt/maven/latest
touch /etc/profile.d/maven.sh
echo 'export MAVEN_HOME=/opt/maven/latest' >> /etc/profile.d/maven.sh
echo 'export PATH=$PATH:/opt/maven/latest/bin' >> /etc/profile.d/maven.sh
chmod +x /etc/profile.d/maven.sh
. /etc/profile.d/maven.sh
# check installation
mvn -v
echo "Install done..."
SCRIPT


$script_nexusInstall = <<SCRIPT
echo "Installing Nexus..."
rm -rf /var/lib/nexus /home/nexus
userdel nexus
wget -nv -O /var/lib/nexus-2.12.0-01-bundle.tar.gz http://download.sonatype.com/nexus/oss/nexus-2.12.0-01-bundle.tar.gz
cd /var/lib/;tar xvzf nexus-2.12.0-01-bundle.tar.gz;rm -rf nexus-2.12.0-01-bundle.tar.gz
mv nexus-2.12.0-01 nexus
cd nexus;chmod -R a+x bin
sudo adduser nexus
chown -R nexus:nexus /var/lib/nexus
chown -R nexus:nexus /var/lib/sonatype-work
ln -s /var/lib/nexus/bin/nexus /etc/init.d/nexus
sed -i 's/#RUN_AS_USER=/RUN_AS_USER=nexus/g' /var/lib/nexus/bin/nexus
chkconfig --add nexus
chkconfig --levels 345 nexus on
service nexus start
echo "Installing yum plugin..."
yum -y install createrepo
sudo /etc/init.d/iptables stop
chkconfig iptables off
echo "Installation of Nexus Sonatype is done..."
SCRIPT


$script_installSonar = <<SCRIPT
echo "Installing SonarQube server..."
wget -nv -O /etc/yum.repos.d/sonar.repo http://downloads.sourceforge.net/project/sonar-pkg/rpm/sonar.repo
yum -y install sonar
service sonar start
echo "Installation of SonarQube is done..."
SCRIPT


$script_setupNetwork = <<SCRIPT
echo "Setting up hostname..."
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
mv /etc/hosts /etc/hosts.old
touch /etc/hosts
echo "192.168.56.2 jenkinsMaster" >> /etc/hosts
echo "192.168.56.3 WindowsSlave" >> /etc/hosts
echo "192.168.56.4 toolsVM" >> /etc/hosts
echo "192.168.56.5 chefServer" >> /etc/hosts
echo "192.168.56.6 appNode1" >> /etc/hosts
echo "192.168.56.7 appNode2" >> /etc/hosts
echo "127.0.0.1 localhost" >> /etc/hosts
#Disable IPV6
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
#Disable frewall
service iptables stop
chkconfig iptables off
echo "Networking/Hosts setup done..."
SCRIPT

$script_setupNetwork_windows = <<SCRIPT
#PS script to add hosts file entries

function add-hostfilecontent {            
 [CmdletBinding(SupportsShouldProcess=$true)]            
 param (            
  [parameter(Mandatory=$true)]            
  [string]$IPAddress,            
              
  [parameter(Mandatory=$true)]            
  [string]$computer            
 )            
 $file = Join-Path -Path $($env:windir) -ChildPath "system32\\drivers\\etc\\hosts"            
            
 $data = Get-Content -Path $file             
 $data += "$IPAddress  $computer"            
 Set-Content -Value $data -Path $file -Force -Encoding ASCII             
}

#Actually adding the entries
add-hostfilecontent 192.168.56.2 jenkinsMaster
add-hostfilecontent 192.168.56.3 WindowsSlave
add-hostfilecontent 192.168.56.4 toolsVM
add-hostfilecontent 192.168.56.5 chefServer
add-hostfilecontent 192.168.56.6 appNode1
add-hostfilecontent 192.168.56.7 appNode2
add-hostfilecontent 127.0.0.1 localhost

#Disable frewall
Get-NetFirewallProfile | Set-NetFirewallProfile -Enabled False

SCRIPT



#Provisioning the VM's
Vagrant.configure("2") do |config|

    config.ssh.username = "vagrant"
    config.ssh.password = "vagrant"

      config.vm.provider "virtualbox" do |vb|
         vb.gui = true
      end
    	  
  config.winrm.retry_limit = 30
  config.winrm.retry_delay = 10

	config.vm.define "jenkinsMaster" do |jenkinsMaster|
    jenkinsMaster.vm.box = "nrel/CentOS-6.7-x86_64"
	jenkinsMaster.vm.hostname = "jenkinsMaster"
	jenkinsMaster.vm.provision :shell, :inline => $script_tools
    jenkinsMaster.vm.provision :shell, :inline => $script_setupNetwork
    jenkinsMaster.vm.provision :shell, :inline => $script_jenkinsMaster
    jenkinsMaster.vm.provision :shell, :inline => $script_gradleInstall
    jenkinsMaster.vm.provision :shell, :inline => $script_mvnInstall
		jenkinsMaster.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsMaster.vm.network "private_network", ip: "192.168.56.2", virtualbox__hostonly: true
		jenkinsMaster.vm.network "forwarded_port", guest: 22, host: 2022, id: "ssh", auto_correct: true
		jenkinsMaster.vm.provider "virtualbox" do |vm|
      vm.name = "JenkinsMaster"
			vm.customize [
        'modifyvm', :id,
        '--memory', '768'
							
      ]
		end
	end


    config.vm.define "windowsSlave" do |windowsSlave|
    windowsSlave.vm.box = "kensykora/windows_2012_r2_standard"
        windowsSlave.vm.hostname = "WindowsSlave"
        windowsSlave.vm.network "private_network", ip: "192.168.56.3", virtualbox__hostonly: true
		windowsSlave.vm.network "forwarded_port", guest: 3839, host: 2839, id: "RDP", auto_correct: true
		windowsSlave.vm.network "forwarded_port", guest: 80, host: 2080, id: "http", auto_correct: true
		windowsSlave.vm.network "forwarded_port", guest: 443, host: 4443, id: "https", auto_correct: true
		windowsSlave.vm.communicator = "winrm"
		windowsSlave.vm.provider "virtualbox" do |vm|
		    vm.name = "WindowsSlave"
		    vm.customize [
							'modifyvm', :id,
							'--memory', '2048'
							
						]
			end			
	    windowsSlave.vm.provision :shell, :inline => $script_setupNetwork_windows
	    windowsSlave.vm.provision :shell, :inline => $script_windowsSlave
	    windowsSlave.vm.provision :shell, :inline => $script_installJava
	end	


	config.vm.define "toolsVM" do |toolsVM|
    toolsVM.vm.box = "nrel/CentOS-6.7-x86_64"
		toolsVM.vm.hostname = "toolsVM"
    toolsVM.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		toolsVM.vm.provision :shell, :inline => $script_tools
    toolsVM.vm.provision :shell, :inline => $script_setupNetwork
    toolsVM.vm.provision :shell, :inline => $script_nexusInstall
    toolsVM.vm.provision :shell, :inline => $script_gitInstall
    toolsVM.vm.provision :shell, :inline => $script_installSonar
		toolsVM.vm.network "private_network", ip: "192.168.56.4", virtualbox__hostonly: true
		toolsVM.vm.network "forwarded_port", guest: 22, host: 4022, id: "ssh", auto_correct: true
		toolsVM.vm.provider "virtualbox" do |vm|
            vm.name = "toolsVM"
			vm.customize [
							'modifyvm', :id,
							'--memory', '1388'
							
						]
		end
	end

end 

