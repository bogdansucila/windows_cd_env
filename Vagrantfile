# -*- mode: ruby -*-
# vi: set ft=ruby :


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
wget -nv -O /var/lib/jenkins/plugins/gitlab-plugin.hpi https://updates.jenkins-ci.org/latest/gitlab-plugin.hpi
wget -nv -O /var/lib/jenkins/plugins/ws-cleanup.hpi https://updates.jenkins-ci.org/download/plugins/ws-cleanup/0.24/ws-cleanup.hpi
wget -nv -O /var/lib/jenkins/plugins/token-macro.hpi https://updates.jenkins-ci.org/download/plugins/token-macro/1.10/token-macro.hpi
wget -nv -O /var/lib/jenkins/plugins/config-file-provider.hpi http://mirrors.xmission.com/hudson/plugins/config-file-provider/2.7.5/config-file-provider.hpi
wget -nv -O /var/lib/jenkins/plugins/msbuild.hpi https://updates.jenkins-ci.org/latest/msbuild.hpi
sudo chown -R jenkins:jenkins /var/lib/jenkins/plugins/
#setting up Jenkins as a service&start it
chkconfig jenkins on
service jenkins restart
echo "Setting up jenkins user for gitlab..."
sed -i "s/jenkins:\/bin\/false/jenkins:\/bin\/bash/g" /etc/passwd
echo "All Done, jenkins master is installed."
SCRIPT

$script_jenkinsSlave = <<SCRIPT
#Install Chocolatey
Write-Host "Installing Chocolatey..."
iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
Write-Host "Finished installing Chocolatey..."

# Install JDK 8
Write-Host "Installing JDK 8..."
choco install jdk8 -y
Write-Host "Finished installing JDK..."
 
#Install Git
Write-Host "Installing Git..."
choco install git.install -y
Write-Host "Finished installing Git..."

#Install MSBuild
Write-Host "Installing MSBuild..."
choco install -y microsoft-build-tools -allowEmptyChecksums
Write-Host "finished installing MSBuild..."

# Install ASP.Net
Write-Host "Installing .net..."
Install-WindowsFeature Web-ASP-Net,Web-ASP-Net45
Write-Host "Finished installing .net..."
SCRIPT

$script_IISandDotNetInstall = <<SCRIPT
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
echo "192.168.56.3 jenkinsSlave" >> /etc/hosts
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
Write-Host "Adding host file entries..."
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

#Re-creating hosts file to avoid duplicate entries
del $file
file $file

#Actually adding the entries
add-hostfilecontent 192.168.56.2 jenkinsMaster
add-hostfilecontent 192.168.56.3 jenkinsSlave
add-hostfilecontent 192.168.56.4 toolsVM
add-hostfilecontent 192.168.56.5 chefServer
add-hostfilecontent 192.168.56.6 appNode1
add-hostfilecontent 192.168.56.7 appNode2
add-hostfilecontent 127.0.0.1 localhost

Write-Host "Finished adding host file entries..."

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
    jenkinsMaster.vm.provision :shell, :inline => $script_jenkinsMaster
	  jenkinsMaster.vm.provision :shell, :inline => $script_setupNetwork
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


  config.vm.define "jenkinsSlave" do |jenkinsSlave|
    jenkinsSlave.vm.box = "mwrock/Windows2012R2"
    jenkinsSlave.vm.hostname = "jenkinsSlave"
    jenkinsSlave.vm.provision :shell, :inline => $script_jenkinsSlave 
    #jenkinsSlave.vm.provision :shell, :inline => $script_installJava 
    jenkinsSlave.vm.provision :shell, :inline => $script_setupNetwork_windows
    jenkinsSlave.vm.provision :shell, :inline => $script_IISandDotNetInstall
    jenkinsSlave.vm.synced_folder ".", "C:\\vagrant", disabled: true
    jenkinsSlave.vm.network "private_network", ip: "192.168.56.3", virtualbox__hostonly: true
		jenkinsSlave.vm.network "forwarded_port", guest: 3839, host: 33839, id: "RDP", auto_correct: true
		jenkinsSlave.vm.network "forwarded_port", guest: 80, host: 880, id: "http", auto_correct: true
		jenkinsSlave.vm.network "forwarded_port", guest: 443, host: 4443, id: "https", auto_correct: true
	  jenkinsSlave.vm.communicator = "winrm"
		jenkinsSlave.vm.provider "virtualbox" do |vm|
		    vm.name = "jenkinsSlave"
		    vm.customize [
							'modifyvm', :id,
							'--memory', '2048'
							
						]
		end	
  end	


	config.vm.define "toolsVM" do |toolsVM|
    toolsVM.vm.box = "nrel/CentOS-6.7-x86_64"
		toolsVM.vm.hostname = "toolsVM"
		toolsVM.vm.provision :shell, :inline => $script_tools
    toolsVM.vm.provision :shell, :inline => $script_setupNetwork
    toolsVM.vm.provision :shell, :inline => $script_gitInstall
    toolsVM.vm.provision :shell, :inline => $script_installSonar
    toolsVM.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
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

