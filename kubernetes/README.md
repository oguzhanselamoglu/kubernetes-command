## minikube komuttları

- **minikube get nodes**: (aktif node bilgsini verir)
- **minikube start**: minikube kubernetes i local ortamda aktif eder ve geçerli docker üzerinde bir node acar
- **minikube stop**: mevcut node durdurur
- **minikube delete**: Herşeyi siler baştan başlamanın en kolay yolu sonrasında start diyeceğiz
- **minikube service <servicename> --url**

## kubectl ile ilgili bir çok komut var bunları öğrenmek ve nasıl kullanıldıgını görmek için --help komutu bize kullanım şekli ve acıklamalrı veriyor

    kubectl cp --help

## Kubectl in baktıgı config dosyasındaki Context leri listeler

## Yanında \* olan default kullanılan context aynı docker context deki gibi

    kubectl config get-contexts

## Varsayılan contexti verir

    kubectl config current-context

## kubernetes config dosyasını görüntüleme

    kubectl config view

## kubernetes config dosyasını taşımak için tüm içeriği elde ediyoruz

    kubectl config view --flatten

## Başka bir sunucu da işlem yapmak istersem docker da yaptıgımız gibi context değiştirmek gerekiyor. Docker daki karşılığı

    docker context use <contextname>'

## Bundan sonrakiş tüm komutlar seçili context de çalışır

    kubectl config use-context <contextname>

## cluster-info -> Üzerinde işlem yaptıgımız cluster hakkında temel bilgiler verir

    kubectl cluster-info

## kubectl deki her bir komut default namespace üzerinden çalışır, fakat biz istersek çalışacak komut için namespace belirtebiliriz -n <namespace>

## -A tüm namespace leri kapsar

    kubectl get pods -n <namespacename>
    kubectl get pod -A

## kubectl için ekran cıktısını -o ile farklı bir output olarak özelleştirebiliriz. pipe da kullanabiliyoruz jq

    kubectl get pods - o json|yaml|table|wide|custom-columns

## explain -> kubectl objeleri için bilgi veren komut explain --help den fark help komutların bilgisini veriyor

    kubectl explain pod

## POD

- kubertenes üzerinde objeler yaratılır pod bir nevi container gibi düşünebiliriz hatta container collection
- her bir pod un eşsiz bir uid si vardır
- her bir pod un eşsiz bir ip adresi vardır
- podlar kubernates de oluşturup yönetebileceğimiz en kücük birimdir
- aynı pod üzerinde birden falza container çalıştırılabilir ama çoğu durumda tek container barındırır

## docker run gibi kubectl run ile de yeni bir pod image belirtilerek oluşturulabiliyor, kapatıldıgında da yeniden başlamıyacak

     kubectl run firstpod --image=ngnx --restart=Never

## describe objenin detayını verir, Pod ne zaman oluşmuş, pod içinde çalışan container id si yada pod uzerindeki event ları verir,pod üzerinde uygulanan tüm işlemleri buradan takip edebiliriz

    kubectl describe <object> <name>
    kubectl describe pods firstpod

## Logs -> pod yada ilgili obje üzerindeki logları verir

    kubectl logs firstpod
    # -f ile canlı log yapısına geçilebiliyor, sürekli aynı komutu calıstırıp son duruma bakmaya gerek yok

## exec -> pod üzerinde komut çalıştırmaya yarıyor, docker exec ile aynı

    kubectl exec -it firstpod -- /bin/sh

## delete , production ortamında dikatli kullanmak lazım onay mekanizması yok

    kubectl delete pods firstpod

## apply -> el ile manuel pod oluşturmak yerine yaml dosyası üzerinden olası tüm config leri girerek te oluşturabiliriz

## oluşan file çalıştırmak için apply komutu kullanılır

    kubectl apply -f pod1.yaml
    # edit -> kubectl edit pods firstpod dediğimizde mevcut pod üzerinde editleme yapılabiliyor, pod un yaml hali ekrana gelir

## kubectl get pods -w watch modda sürekli izler

## kubectl port-forward firstpod 8080:80 (Port yönlendirmeyi yapar)

## LABEL

    kubectl get pods -l "app" --show-labels (podlar arasında lable  a göre filtreleme yapabiliyoruz -l ifadesi ile)
    kubectl get pods -l "app=firstapp" --show-labels
    kubectl get pods -l "app=firstapp,tier=backend" --show-labels (selector de , ve anlamına geliyor)
    kubectl get pods -l 'app in (firstapp,secondapp)' --show-labels
    kubectl get pods -l 'app notin (firstapp)' --show-labels (olmayanları listele)
    kubectl label pods pod9 app=thirdapp (mevcut bir pod a label ekleme)
    kubectl label pods pod9 app- (label silme)
    kubectl delete -f .\podlabel.yaml (dosya içindeki tüm podları siler)

## NAMESPACE

    kubectl create namespace app1 ( app1 adında namespace yaratır)
    kubectl get namespaces (namespace listesi verir)
    ubectl get pods -n <namespacename> (ilgili namespace deki pod ları listeler)

## Deployment

    kubectl create deployment firstdeployment --image=nginx:latest --replicas=2
    kubectl set image deployment/firstdeployment nginx=httpd (mevcut deployment daki image guncellemeye yarıyor)

# deployment ile pod sayısını ayarlıyoruz

    kubectl scale deployment firstdeployment --replicas=5 (mevcut delpoyment da pod sayısını 5 e cıkartır)

# rollout -> en son yapılan değişikliğği geri alır

    kubectl rollout undo deployment firstdeployment

- iki önemli konu var recreate ve rolling , deployment stretejisi olarak bu iki özellikten birini kullanırız
- recreate tüm podları siler yeniden oluşturur, kısa süreli bir geçikme olabilir, iki versiyonun sıkıntılı olabileceği durumlarda tercih edilir
- rolling -> herhangi bir streteji belirtmessek default uygulanır
- aşamalı olarak değişiklik yapar, eş zamanlı silinip oluşturulacak pod sayısını belirtebiliyoruz
- sistemde kesiti olmadan devam eder
- rolout history de yapılan tüm değişiklikleri görüyoruz
  kubectl rollout history deployment rolldeployment

## REPLICASET

- ReplicaSet in amacı herhangi bir zamanda çalışan kararlı bir recpica Pod setini sürdürmek. Bu nedenle belirli sayıda Pod un kullanılabilirliğini garanti eder
- Deployment recplicaSetin bir üst objesidir
- Podları replicaSet yönetir
- replicaSet üzerinde image de yapılan değişiklik hemen Pod lara yansımaz, bu yuzden replicaSet üzerinden değil de deployment üzerinden değişiklik yapıyoruz

## Network

- Aynı cluster içindeki tüm pod lar varsayılan olarak birbiriyle haberleşir herhangi bir kısıtlama olmaz
- service tarafında ClusterIp, NodePortIp, LoadBalancer 3 tip var
  kubectl get endpoints -> endpoint üzerinden oluşan ipleri ve yönlenen portları görebiliriz

## Liveness probe

- kubelet normalde çalışmayan hataya düşmüş yada durmuş pod ları yeniden başlatarak yada yenisini üreterek çalışmaya zorlar
- sürekliliği sağlar fakat pod düzgün çalışır ama içindeki uygulama düzgün çalışmaz ise bunu yakalayamaz
- bunu ise LivenessProbe lar vasıtasıyla farkedebiliriz, pod içinden bir uygulama claıstırarak içerdeki uygulamanın durumunu gözlemleyebiliriz
- uygulama içindeki bir endpointe http istek atarak heltcheck yapılarak kontrol sağlanabilir
- httpGet ve tcp yada herhangi bir dosya kontrolü yapılabilir
  spec:
  livenesProbe:
  httpGet:
  path: /heltcheck
  port: 8080
  periodSeconds: 3
  initialDelaySeconds: 5 //5 sn bekler 3 sn aralıkla dener

## Readiness probe

- Uygulama ayağa kalkar fakat bazı verileri cekebilir ve ayağa kalkar kalkmaz service hazır hale gelmeyebilir
- Readiness check eder ve pod hazır olana kadar bekler, hazır olduktan sonra trafik akmaya başlar
- Pod un service sunmaya hazır hale gelmesini garanti eder, buradaki pod hazır hale geldikten sonra eski pod terminete eder

## Persistent volume cluster dışında tutulur bu sayede pod yaşam süresinden bağımsız olur (Azur Disk,aws, google depolama çözümleriyle konuşabilir)

## emptyDir -> Geçici volum ilk olarak bir Pod bir noda atandıgında oluşturulur ve u Pod o node da çalıştığı sürece var olur. Başlangıçta boştur Pod içindeki containerlar emptyDir volume deki dosyaları okuyabilir ve yazabilir ancak bu birim her kapsayıcıda farklı yollara maplenmiş olabilir pod silindiğinde volume de gider

    volumeMounts:
    - name: cache-vol
      mounthPath: /temp/log
    volumes:
    - name: cache-vol
    emptydir: {}

## hostPath -> Folder bazlı maplenir container silinsede halen durur

---

## env değerlerini listeler pod içindeki

    kubectl exec <podname> --printenv

## kendi bilgisayarımızdaki portu pod daki uygulamanın portuna gönderir

    kubectl port-forward veboni 8080:80 (Port yönlendirmeyi yapar)


    kubectl describe secret -n kube-system

    kubectl proxy

## Kubernetes deki node ların kaynak kullanımını verir

    kubectl top nodes

## Kubernetes autoscale listesini verir

    kubectl get hpa -A

## Kubernetes tarafında pod ları listelerken filtreleme özelliği

    kubectl get pods --field-selector status.phase=Running -o json

## Kubernetes tarafında pod ları listelerken filtreleme ve filtreleni silme

    kubectl get pods --field-selector status.phase=Failed -o json | kubectl delete -f -

## Kubernetes oluşan tüm eventları listeleme

    kubectl get events --sort-by='.metadata.creationTimestamp' -A

## pod üzerinde komut çalıştırma bash ile

    kubectl exec -it podname -- \bin\sh
    kubectl exec -it identityapi-deployment-798cc98bb8-9hnzn -- printenv
    apt install  postgresql-client
    psql -h serveraddress -d dbname -U username
    psql -h 172.22.226.100 -d veboni-0277acab-c91a-42d4-9556-bc77826b4915 -U postgres

## kubeconfig dosyasını merge etme

    cp config config_bk
    KUBECONFIG=config:kodcluster.yaml kubectl config view --flatten > config_tmp
    kubectl config get-clusters --kubeconfig=config_tmp
    kubectl config get-contexts --kubeconfig=config_tmp
    mv config_tmp config

# make a backup

cd ~/.kube/
cp config config.bak

# merge both kube config files

$ENV:KUBECONFIG = "C:\Users\username\.kube\config;C:\Users\username\.kube\config-bk"

# verify that the variable is set

$ENV:KUBECONFIG

# output to temp file

kubectl config view --flatten > config-merged

# verify that config-merged is correct

kubectl --kubeconfig=config-merged config get-clusters

# delete backup

rm config

# move merged file to config

mv config-merged config

# remove (optional)

rm config.bak

# delete config

kubectl config unset contexts.kubernetes-admin@kubernetes
kubectl config unset users.kubernetes-admin
kubectl config unset clusters.kubernetes

Error from server (InternalError): error when creating benoni-ingress.yaml: Internal error occurred: failed calling webhook validate.nginx.ingress.kubernetes.io: Post https://rke2-ingress-nginx-controller-admission.kube-system.svc:443/networking/v1beta1/ingresses?timeout=10s: dial tcp 10.43.217.216:443: connect: connection refused

hatası

    kubectl get validatingwebhookconfigurations
    kubectl delete validatingwebhookconfigurations [configuration-name]`

ssh ile bağlanırken finger print oluşuyor
mevcut finger printleri silmek için
rm -f .ssh/known_hosts

kubectl get ingress -A
iptables -L

kubectl get pod -n ingress
kubectl get svc -n ingress

# namespace listesi

kubectl get ns

apt install iputils-ping

kubectl delete ingress veboniingress
kubectl edit ingressclass nginx -n ingress
kubectl get ingressclass
kubectl describe svc ingress-nginx-ingress -n ingress
apt install net-tools

# sudo yetkisi

sudo su

# Helm üzerinden ingress kurulumu

    helm install ingress nginx-stable/nginx-ingress -n ingress --set controller.service.loadBalancerIP=,rbac.create=true
    helm delete ingress -n ingress
    kubectl delete ns metallb-system
    kubectl get adresspool -A
     helm ls -n ingress
     apt install vim
     helm repo update
     sudo apt-get install helm
     sudo apt-get install apt-transport-https --yes
    curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null


       openssl rsa -in private-enc.key -out private.key
       openssl pkcs12 -in blabla.com.pfx -nocerts -out private-enc.key


       >docker container exec -it 8531cf4e3139 /bin/bash

## get secrets

kubectl get secrets

## Export secrets

kubectl get secret my-secret-name -o yaml > my-secret-name.yaml

## Create secret

kubectl create secret docker-registry <secret-name> \
 --namespace <namespace> \
 --docker-server=<container-registry-name>.azurecr.io \
 --docker-username=<service-principal-ID> \
 --docker-password=<service-principal-password>

    kubectl create secret docker-registry veboniDevOps\
    --namespace dev \
    --docker-server=veboniacr.azurecr.io \
    --docker-username=veboniacr \
    --docker-password=sV2/RWItkJhd2XoimKZ5vHaPsruNQWhI
