**1. Підготуємо в окремий локальний кластер для встановлення ArgoCD.**

$ k3d cluster create argo
... 
INFO[0029] Cluster 'argo' created successfully!

    $ kubectl cluster-info

Kubernetes control plane is running at https://0.0.0.0:43297
CoreDNS is running at https://0.0.0.0:43297/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:43297/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy

**2. створимо неймспейс argocd**
   
   $ kubectl create namespace argocd
   
namespace/argocd created

alias k=kubectl

      $ k get ns
      
NAME              STATUS   AGE

kube-system       Active   80m

default           Active   80m

kube-public       Active   80m

kube-node-lease   Active   80m

argocd            Active   28s

**3. Встановимо ArgoCD через скрипт з офіційного репозиторію**

$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

$ k get pod -n argocd -w

NAME                                                READY   STATUS    RESTARTS   AGE

argocd-redis-b5d6bf5f5-sqlgw                        1/1     Running   0          3m41s

argocd-notifications-controller-589b479947-fm4kp    1/1     Running   0          3m41s

argocd-applicationset-controller-6f9d6cfd58-9rvjf   1/1     Running   0          3m41s

argocd-dex-server-6df5d4f8c4-nd829                  1/1     Running   0          3m41s

argocd-application-controller-0                     1/1     Running   0          3m40s

argocd-server-7459448d56-xnrvf                      1/1     Running   0          3m40s

argocd-repo-server-7b75b45897-qsw9z                 1/1     Running   0          3m41s


**4.Отримаємо доступ до інтерфейсу ArgoCD GUI**

Отримати доступ можна в два шляхи:
Service Type Load Balancer
Ingress
Port Forwarding
Скористаємось Port Forwarding за допомогою локального порта 8080. В команді ми посилаємось на сервіс svc/argocd-server який знаходиться в namespace -n argocd. Kubectl автоматично знайде endpoint сервісу та встановить переадресацію портів з локального порту 8080 на віддалений 443

$ kubectl port-forward svc/argocd-server -n argocd 8080:443

[1] 23820
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080

**5. Отримаємо пароль до AgroCD Gui**
 
Використаємо команду для отримання паролю, вкажемо файл сікрету argocd-initial-admin-secret а також формат виводу jsonpath="{.data.password}". Це поверне нам base64 закодований пароль, після чого використаємо команду base64 -d для повернення паролю в простий текст. Отриманий пароль та логін admin вводимо в Web-інтерфейс ArgoCD

$ k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
Base64_password                                                                                                       
$ k -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"|base64 -d;echo
Plaintext_password

**6. Використаємо у браузері для входу дані**

admin Plaintext_password

за динамічним лінком https://127.0.0.1:8080 

