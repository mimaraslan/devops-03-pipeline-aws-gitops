# DevOps Pipeline

## CI/CD Evreni

```

CI/CD:           (Jenkins, Git,  GitHub, GitOps,  GitHub Actions,    GitLab, GitLab CI,    Bitbucket, Bamboo)
Scripting        (Python, Bash, PowerShell)
Containers:      (Docker)
Orchestration:   (Kubernetes, Helm, ArgoCD)
Cloud            (AWS, Azure, GCP)
Virtualization:  (VMware, VirtualBox)
IaC:             (Terraform, Ansible, CloudFormation)
Monitoring:      (Prometheus, Grafana, ELK)
```
![img_1.png](/images/img_1.png)
<hr>



AWS CLI kurulacak.

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```
aws --version
```


#### MacOS

```
ls -la ~/
mv ~/Downloads/MyAWSKeyPair.pem ~/.ssh/
chmod 400 ~/.ssh/MyAWSKeyPair.pem

nano ~/.ssh/config
```

Host MyDevOpsAWS
HostName PUBLIC_IP
User ubuntu
IdentityFile ~/.ssh/MyAWSKeyPair.pem

Ctrl + O
Enter
Ctrl + X

```
ssh MyAWSKeyPair
```

<hr>

## === Makine 1: My Jenkins Master ============================

Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```
isim olarak aşağıdakini yazdık.

My-Jenkins-Master


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

veya

Ctrl + O'ya bas.
En sonda da Enter'a bas.



Makineyi yeniden başlat.

```
sudo init 6     
```
ya da
```
sudo reboot
```

<hr>
===============================

AWS EC2 makinemi dış dünyaya açtık.
Security groups kısmına gittik.
Dışarıdan 8080 portundan erişime izin verdik.

<hr>
======= Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

```
sudo apt install openjdk-21-jre  -y

java --version
```

<hr>
======= Jenkins'i kuracağız.========================

https://www.jenkins.io/doc/book/installing/linux/


```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins  -y
```




Aşağıdaki komutları sırasıyla çalıştıracağız.
Bu makineyi Jenkins'e adıyoruz.
Makineyi kapatıp açtığımızda Jenkins otomatik olarak çalışır durumda olacak.

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```


Bu terminal'i kapatmadım sadece o durumdan çıktım. Terminalim açık.
Ctrl + C

Terminalime bu komutu yazıp Jenkins'in admin parolasını öğrendik.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```



<hr>

## === Makine 2: My Jenkins Agent ============================

Bu makine Docker'a özeldir.


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

<hr>
===============================

İç IP adının yerine bir isim vereceğiz.

```
sudo nano /etc/hostname
```

isim olarak aşağıdakini yazdık.

My-Jenkins-Agent


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.


Makineyi yeniden başlat.

```
sudo init 6     
```

ya da

```
sudo reboot
```


<hr>
=======Java'yı kuracağız.========================

Terminale Java yaz ve enter'a bas. Açılan komutlardan birini al ve çalıştır.

```
sudo apt install openjdk-21-jre  -y

java --version

java -version
```


<hr>
===== Docker kuracağız. ==========================

Terminale gelip sadece docker yaz ve enter'a.

```
sudo apt  install docker.io  -y

sudo usermod -aG docker $USER

sudo reboot
```


<hr>
Makineleri birbirne tanıtacağız.

=== My Jenkins Master için ============================

```
sudo nano  /etc/ssh/sshd_config
```

Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.

<hr>
### Authentication:

```
PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
```

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
sudo service sshd reload
```

<hr>
=== My Jenkins Agent için ============================

```
sudo nano  /etc/ssh/sshd_config
```
Authentication kısmına gel.
Aşağıdaki şu iki satırın önündeki açıklama işaretini # kaldır.


<hr>
### Authentication:

```
PubkeyAuthentication yes

AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
```

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
sudo service sshd reload
```

<hr>
=== My Jenkins Master için ============================

```
pwd

cd /home/ubuntu
```

Master makinenin takip edilebilmesi için bir şifre anahtar oluşturuyorum.

```
ssh-keygen


cd /home/ubuntu/.ssh/

ll


sudo cat  id_ed25519.pub
```

İçindeki böyle yazan satırı alıp kopyalayın.

```
ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master
```


Sonuna kadar enter'a basıp geç.


<hr>
=== My Jenkins Agent için ============================

```
cd /home/ubuntu/.ssh/

ll

sudo cat authorized_keys
```

Bu dosyanın için aç.
```
sudo nano authorized_keys
```

Master'dan aldığın şu satırı en alta yapıştır.

ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master

<hr>
==== Agent Takip eden taraf ===

ssh-rsa BBBBBBBBBBBBBBBBBB MyAWSKeyPair

<hr>
==== Master'dan getirdiğim keygen anahtar takip edilecek taraf ===

ssh-ed25519 AAAAAAAAAAAAAAAAA ubuntu@My-Jenkins-Master

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

```
cd /home/ubuntu/.ssh/

sudo cat authorized_keys
```

<hr>

Master ve Agent makinelerini yeniden başlat.

```
sudo reboot
```

<hr>
======================================
http://PUBLIC_IP:8080/computer/(built-in)/configure

Jenkins'i aç.
Nodes kısmına gel.

Built-In Node makinesinin içine gir.

Nodes -> Built-In Node -> Configure

Number of executors kısmını SIFIR 0 yap.



Agent makineyi Jenkins'e eklemek için

Nodes -> New node

http://PUBLIC_IP:8080/computer/new

Ona "My-Jenkins-Agent" diye bir isim verdik.
Permanent Agent olduğunu seçtik.


Jenkins'te Agent'ı eklerken onun kendi iç IP'sini alacaksın.

<hr>
====== Master Makinedeki bu anahtarı okuyup kopyalayın ve Jenkins'e gelin. Credentials için ====

```
cd  /home/ubuntu/.ssh/
sudo cat id_ed25519
```

-----BEGIN OPENSSH PRIVATE KEY-----
CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC
-----END OPENSSH PRIVATE KEY-----




<hr>
GitHub Token oluşturacağız.

https://github.com/settings/tokens

MyGitHubTokenForAWS

ghp_ABCABCABCABCABCABCABCABCABCABC


<hr>

## === Makine 3: SonarQube kurulumu ==========


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```

isim olarak aşağıdakini yazdık.

My-SonarQube

<hr>

### ÖDEV : hostname'i tek komutla değiştirmeyi bulun.

```
sudo hostname My-SonarQube
```
<hr>


Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

Makineyi yeniden başlat.
```
sudo reboot
```

<hr>
====  PostgreSQL kurulumu  =====

```

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null



sudo apt update

sudo apt-get -y install postgresql postgresql-contrib




sudo systemctl enable postgresql

sudo systemctl status postgresql


sudo passwd postgres
```
parola: 123456789


<hr>
=== Veritabanına terminalden en baş yetkili olarak giriş yapmak istiyorum.

```
su - postgres
```

parola: 123456789



```
createuser sonar

psql

ALTER USER sonar WITH ENCRYPTED password 'sonar';

CREATE DATABASE sonarqube OWNER sonar;

grant all privileges on DATABASE sonarqube to sonar;

\q

exit
```




<hr>
==== Adoptium repository ====

```
sudo bash

wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc

echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list



sudo apt update

sudo apt     install temurin-17-jdk  -y

```

Aşağıdaki komutta aynı işi yapar.

```
sudo apt-get install temurin-17-jdk  -y
```

<hr>

JDK zaten var. JRE bunu sadece örnek olsun diye kuracağız.

```
sudo apt install openjdk-17-jre -y
```

<hr>
Java'nın alternatif sürümleri seçmek için

```
sudo update-alternatives --config java

java --version
```



<hr>

### === Linux kernel sınırlarını genişleteceğiz. ===

<hr>

== Vim ve Nano terminalden dosyaların içine yazı yazmak içindir.

```
sudo vim /etc/security/limits.conf
```

Bir şey eklemek için önce klavyeden i tuşuna bas.

<hr>
== Nano ile çalışmak daha kolaydır.

```
sudo nano /etc/security/limits.conf
```

<hr>
== Bu iki satırı dosyanın en altına ekle yapıştır.

```
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096
```

çıkış için ESC tuşuna bas.
:wq  yaz ve enter'a bas.



```
sudo vim /etc/sysctl.conf
```
Bir şey eklemek için önce klavyeden i tuşuna bas.
Eklenecek bilgi aşağıdaki satır.
```
vm.max_map_count = 262144
```

Çıkış için ESC tuşuna bas.
:wq  yaz


Makineyi yeniden başlat.
```
sudo reboot
```

<hr>

### ==== Sonarqube kurulumu  =====

```
pwd

cd /opt

sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.9.0.112764.zip
```

== Ziplenmiş bir dosyayı ubuntuda açkmak için bu uygulamayı indirdim.
```
sudo apt install unzip


sudo unzip sonarqube-25.9.0.112764.zip -d/opt

pwd

sudo mv   /opt/sonarqube-25.9.0.112764    /opt/sonarqube

```


<hr>
=== sonar kullanıcı oluşturulacak ve haklar verilecek

```
sudo groupadd sonar

sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar

sudo chown sonar:sonar /opt/sonarqube -R
```

<hr>
=== Veritabanıyla bu sonar kullanıcısı konuştur

```
sudo vim /opt/sonarqube/conf/sonar.properties

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar

sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

```

<hr>
=== Sonar servisini oluşturacağız.

```
sudo vim /etc/systemd/system/sonar.service
```

Aşağıdaki kodları olduğu gibi bu dosyanın içine yapıştır.


```

[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target

```

Makine açıldığında sonarqube otomatik olarak çalıştırma komutları

```
sudo systemctl enable sonar

sudo systemctl start sonar

sudo systemctl status sonar
```



<hr>
=== Log takibi ===

```
sudo tail -f /opt/sonarqube/logs/sonar.log
```

Makinenin public ip değerini al ve 9000 portundan giriş yap.
```
kullanıcı: admin
parola: admin
```
Yeni parloyı verdim.

Adana_01Adana_01


<hr>
Jenkins için token oluştur.

Administrator  -> Security

http://SONARQUBE_MAKINENIN_PUBLIC_IPsi:9000/account/security



jenkins-sonarqube-token

sqa_EEEEEEEEEEEEEEEEEEEEEEEEEEE



Jenkins içinde jenkins-sonarqube-token'ı Security Text olarak kaydettir.


SonarQube pluginlerini kur.

System tarafına gir ve SonarQube ayarlarını yap.

Tool olarak Sonar Scanner'i tanıt.

Sonar'ın kurulduğu makinenin Private IPv4 addresses değerini kopayla.


http://JENKINS_MASTER_MAKINENIN_PUBLIC_IPsi:9000/account/security






### Docker'ı Jenkins'e entegre edeceğiz.

Jenkins ->  Manage Jenkins  -> Plugins



MyDockerHubTokenForAWS

docker login -u mimaraslan  -p  dckr_pat_DDDDDDDDDDDDDDDDDDDDDDDDD




Trivy  ile imajları kontrol ediyoruz.
Jenkinsfile içine bu komutları ekledik.

        stage("Trivy Scan") {
            steps {
                script {
                    if (isUnix()) {
                        sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mimaraslan/devops-03-pipeline-aws:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                     } else {
                        bat ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mimaraslan/devops-03-pipeline-aws:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
                    }
                }
            }
        }




### Docker imageleri birikiyor. Onları Agent makineden temizlemek lazım.






// Agent makinesi zamanla dolacak. Docker şişecek dolacak. Temizlik yapmanız lazım.
// Agent makinede temizlik için yeriniz azalmışsa şu komutları kulanın lütfen.
// Hatta mümkünse bu kodları buraya uyarlayın lütfen.


Elimizle terminalden imajları tek tek silmek komutları

					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.10
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.11
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.12
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.13
					docker rmi mimaraslan/devops-03-pipeline-aws:1.0.14
					docker rmi mimaraslan/devops-03-pipeline-aws:latest
					
					
                    docker rmi   $(docker images --format '{{.Repository}}:{{.Tag}}' | grep 'devops-03-pipeline-aws')
                    docker container rm -f $(docker container ls -aq)
                    docker volume prune -f 







## === Makine 4: My EKS Master Server ============================
K8s için bir makine oluşturduk. İçine Kubernetes kuracağız.


Windows
MobaXterm üzerinden Session -> SSH oluşturacağız.


Terminalden bu 2 komutu sırayla çalıştıracağız.

```
sudo apt update

sudo apt upgrade  -y
```

===============================

İç IP adının yerine bir isim vereceğiz.
```
sudo nano /etc/hostname
```
isim olarak aşağıdakini yazdık.

My-EKS-Master-Server

Ctrl + X'e bas.
Onaylamak için Y harfine bas.
En sonda da Enter'a bas.

veya

Ctrl + O'ya bas.
En sonda da Enter'a bas.



Makineyi yeniden başlat.

```
sudo init 6     
```
ya da
```
sudo reboot
```

<hr>
===============================


### AWS CLI

Web Sayfası
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Root kullanıcısı en yetkili yönetici moduna terminalden geç.
```
sudo su
```

1.komut
```
apt install unzip -y 
```


2.komut
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```



###  kubectl

Web Sayfası
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#linux_amd64_kubectl

Kubernetes 1.33 seçtik.
```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.4/2025-08-20/bin/linux/amd64/kubectl

chmod +x ./kubectl

mv kubectl /bin

kubectl version 

kubectl version  --output=yaml
```
<hr>
===============================

### eksctl

Web Sayfası
https://docs.aws.amazon.com/eks/latest/eksctl/installation.html



Docker Hub Token oluştur.
MyDockerHubToken terminalden Token ile DockerHub'a bağlan.
```
docker login -u mimaraslan  -p  dckr_pat_123456789
```

Jenkinse DockerHub Token'ı tanıt ekle.





Agent makinesi zamanla dolacak. Docker şişecek dolacak. Temizlik yapmanız lazım.
Agent makinede temizlik için yeriniz azalmışsa şu komutları kulanın lütfen.
```
docker rmi $(docker images --format '{{.Repository}}:{{.Tag}}' | grep 'devops-03-pipeline-aws')

docker container rm -f $(docker container ls -aq)

docker volume prune -f
```




## === Makine 4:  My EKS (Elastic Kubernetes Service) Server  =====

```
sudo apt update

sudo apt upgrade -y
```

Makinenin hostname kısmını aç ve adını güncelle.
```
sudo nano /etc/hostname
```

My-EKS-Bootstrap-Server  
Bu ismi makineye ver.

Ctrl + x 'e bas.
Onaylamak için Y harfine bas.
En sonda ise Enter'a bas.


Makineyi yeniden başlat.
```
sudo reboot
```


### AWS CLI kurulumu


WEB PAGE
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#cliv2-linux-install

```
sudo su

pwd


curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

apt install unzip

unzip awscliv2.zip

sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```


### Kubectl kurulumu
```
sudo su

pwd
```

Web Sayfası  
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.30.4/2024-09-11/bin/linux/amd64/kubectl


ll

chmod +x ./kubectl

ll 


mv kubectl /bin 


export PATH=$HOME/bin:$PATH
                                  
kubectl version --output=yaml
```


### eksctl

Web Sayfası
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#linux_amd64_kubectl

```
sudo su

pwd


curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

cd /tmp

ll 

sudo   mv    /tmp/eksctl     /bin

eksctl version
```

Makineyi yeniden başlat.
```
sudo reboot
```

Kubectl sürümü bilgisini al.
```
kubectl version --client
```
![img_2.png](/images/img_2.png)
![img_3.png](/images/img_3.png)
### EKS makinesi için us-east-2 bölgesinden 2 tane node oluşturacağız.
```
eksctl create cluster --name my-workspace-cluster \
--region us-east-2 \
--node-type t3.large \
--nodes 2 
```

Makineyi yeniden başlat.
```
sudo reboot
```

EKS makinesinin terminalinde Node ve Pod bilgilerini öğren.
```
kubectl get nodes

kubectl get pods
kubectl get pod
kubectl get po
```


### ArgoCD

Web Sayfası
https://argo-cd.readthedocs.io/en/stable/getting_started/

```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get pods -n argocd
```

Yönetici moduna geç.
```
sudo su

pwd
```


### ArgoCD sürümünü çekeceğiz.

Son sürümü çekmek için bu 3 satır gerekli.

```
curl -L -s https://raw.githubusercontent.com/argoproj/argo-cd/stable/VERSION

VERSION=$(curl -L -s https://raw.githubusercontent.com/argoproj/argo-cd/stable/VERSION)

curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v$VERSION/argocd-linux-amd64
```


OR

Sürümün elle yazılmışı
```
curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.12.3/argocd-linux-amd64
```    

ArgoCD kullanım hakkını alıyoruz.
```	
chmod +x /usr/local/bin/argocd
```


Normal kullanıcı moduna geç. Terminale bunu yaz.
```
exit 
```


EKS makinesinde dış servis ayarlarını yapıyoruz.
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl get svc -n argocd
```

ArgoCD dış dünyaya böyle bir adres ile açılıyor.

a5b3d196d6343444dbd692184429ca6b-117814533.us-east-1.elb.amazonaws.com


Admin şifresini almak bu komutu kullan
```
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

Açıklama kısmındaki şifreli halde duran parolayı alıyoruz ve çözüyoruz.
```
echo     ellOckVqVzZEQ3RCb2toRg==  | base64 --decode
```

Size böyle bir sonuç verecek.

zYNrEjW6DCtBokhF ubuntu@My-EKS-Master-Server:~$

Admin şifresi budur.

zYNrEjW6DCtBokhF



ArgoCD'ye web tarayıcısı üzerinden giriş yapıyoruz.

Giriş yap ve "User Info" menüsünden parolayı hemen güncelle.



Terminalden ArgoCD kullanımı
```
argocd login ad22e3a897977425fb3d85ffd2fc1183-1747909152.us-east-2.elb.amazonaws.com   --username admin

argocd cluster list
```



Çalışılan K8s cluster adını alıyoruz.
```
kubectl config  get-contexts
```

K8s cluster adını ArgoCD'ye veriyoruz.
```
argocd cluster add i-01b7b0cfff36e42d5@my-workspace2-cluster.us-east-2.eksctl.io  --name my-workspace2-cluster
```

EKS servis bilgisi komutları
```
kubectl get svc
kubectl get service
```

MyGitHubToken
ghp_123456789123456789123456789



GitOps projesini tetikleme yetkisi için Jenkins Admin token'ı oluşturuyoruz.

JENKINS_API_TOKEN

123456789123456789123456789



Pod içinde çalışan uygulamayı web tarayıcısı üzerinden görebiliriz.
```
kubectl get service  
```

http://a72903a0997564a328c47bc910f75aa8-253506306.us-east-1.elb.amazonaws.com:8080



#### Agent makinedeki Docker image dosyalarını temizleme.

Agent makinesi zamanla dolacak. Docker şişecek dolacak. Temizlik yapmanız lazım.
Agent makinede temizlik için yeriniz azalmışsa şu komutları kulanın lütfen

```
docker rmi $(docker images --format '{{.Repository}}:{{.Tag}}' | grep 'devops-003-pipeline-aws')

docker container rm -f $(docker container ls -aq)

docker volume prune -f
```






## EKS nodelarını silme süreci

https://docs.aws.amazon.com/eks/latest/userguide/delete-cluster.html

```
eksctl version
```

Çalışan tüm servisleri listele
```
kubectl get svc --all-namespaces
```

Sadece bir servisi silmek için bu komutu kullanıyoruz ama şimdi servisleri tek tek silmeyeceğiz!!!
```
kubectl delete svc SERVISIN_ADI
```


Cluster adını ve detaylarını görmek için
```
kubectl config view
```


Silmeden önce konumu mutlaka belirtmek lazım.
```
export AWS_DEFAULT_REGION=us-east-2
```

Nodeları ve servislerin hepsini cluster adını vererek toplu halde sileceğiz.
```
eksctl delete cluster  --name  my-workspace2-cluster
```
