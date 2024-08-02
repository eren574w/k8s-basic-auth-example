# k8s-basic-auth-example

Bu proje, Kubernetes ortamında temel kimlik doğrulama (Basic Authentication) ile güvenliği sağlamak için bir örnek uygulamadır. Bu rehber, Kubernetes üzerinde basit bir kimlik doğrulama mekanizmasının nasıl kurulacağını ve yapılandırılacağını gösterir. Uygulama, temel bir kullanıcı adı ve parola ile erişimi sınırlayan bir yapı oluşturur, bu sayede belirli servislere yalnızca yetkilendirilmiş kullanıcıların erişmesi sağlanır.

##  Adım 1: Proje kodları indirme 

```
git clone https://github.com/eren574w/k8s-basic-auth-example.git
cd k8s-basic-auth-example
```

## Adım 2: Minikube başlatma 

```
minikube start 
```

## Adım 3: Dosyaları uygulama 

```
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
```

#### kontrol edelim 

```
kubectl get pods 
kubectl get svc
```

bu kodu çalıştırdığınız zaman şunu fark edeceksiniz ki podlarda `status` durumu `ContainerCreating` olabilir bunun nedeni nginx containerları yüklendiği için podlar hemen çalışmıyor. 

biraz bekledikten sonra yeniden podları çalıştırırsak

```
kubectl get pod
```

podların status durumu `Running` olarak değişti ve artık podumuz hazır 


## Adım 4 htpasswd Dosyasını Oluşturma: 

Eğer htpasswd aracı sistemde yüklü değilse yükelyelim ü

```
sudo apt-get install apache2-utils  
```

şimdi ise bu doyayı kuralım 

```
htpasswd -c auth user1 
```
bu komut, dosya adı `auth` olan ve kullanıcı ismi `user1` olan bir dosya oluşturdu, komutu çalışıldığı zaman sizden bu kullanıcı ismine bir sefre koymanızı isteyecektir. hem kulklanıcı ismini hem de şifreyi akılda tutuyoruz

## Adım 5: Secret oluşturma 

```
kubectl create secret generic basic-auth --from-file=auth 
```

secreti kontol etmek için 

```
kubectl get pods 
```

secretin içeriğini görmek istiyorsak 

```
kubectl describe secret basic-auth 
```

## Adım 6: Ingress dosyasını uygulama: 

```
kubectl apply -f nginx-ingress.yaml
```

kontrol edelim 

```
kubectl get ingress
```

orada `HOSTS` yazan yerde domain ismimizi görüyoruz: `your-domain.com`, bu ismi ingress dosyasında verdik 

## Adım 7: Host belirleme

şimdi ise bu hostu ve minikube ip'sini cihazımızda hosta kaydetmeliyiz 

ilk `minikube ip` adresine bakalım 

```
minikube ip 
```

bu adresi öğrendikten sonra hostumuza gidelim 

```
sudo nano /etc/hosts
```

en aşağıya, `ip adresimizi` ve `host ismimizi` girelim 

```
<ip adresi> your-domain.com
```

kaydedip çıkalım 

### Ingress aktif edelim 

```
minikube addons enable ingress
```

şimdi ise sadece host ismiyle sitemize girelim 

`your-domain.com`


siteye girerken bizden bir giriş ekranı isteyecek, buraya kullanıcı ismi olarak berlirlediğimiz `user1` ve girmiş olduğunuz şifreyi girin. böylelikle siteye giriş yapmış olacağız.
