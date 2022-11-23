# Atividade Kubernetes

Documentação da Atividade de Kubernetes do Programa de Bolsas Compass - DevSecOps.  
O objetivo desta atividade é realizar o deploy de um site Wordpress utilizando o banco de dados MySQL.  
Esta atividade foi realizada utilizando o Minikube na versão 1.27.1, Kubernetes 1.25.2.  

### 1. Criação do Namespace

A criação do namespace pode ser feita através de um arquivo yaml com a seguinte estrutura:  
```yaml  
apiVersion: v1  
kind: Namespace  
metadata:  
  name:  
```  
Criado o arquivo YAML, basta fazer o apply  
`kubectl apply -f namespace.yaml`

Também pode ser feita pela linha de comando  
`kubectl create ns nome-do-namespace`  

### 2. Criação do Secret  

Para utilizar dados sensíveis, como senhas, de forma segura na aplicação, deve-se fazer o uso de Secrets.  
Para mais informações sobre o Secret, consultar a [documentação oficial do Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) [(também disponível em português)](https://kubernetes.io/pt-br/docs/concepts/configuration/secret/)  
A documentação oficial fornece um modelo de um arquivo de configuração do Secret  
```yaml  
apiVersion: v1  
kind: Secret  
metadata:  
  name: mysecret  
type: Opaque  
data:  
  username: YWRtaW4=  
  password: MWYyZDFlMmU2N2Rm  
```  
Os dados sensíveis são informados como chave: valor. Importante notar que os valores de cada chave são codificados em base64.  
Exemplo de como codificar os dados:  
`echo -n 'string_a_ser_codificada' | base64 -w 0`  

Criado o arquivo YAML, basta aplicá-lo  
`kubectl apply -f secret.yaml`  

### 3. Criação do Service  

Para expor os pods como serviço de rede, utiliza-se o service. Deve ser criado um service para o MySQL e outro para o Wordpress, seguindo a configuração de parâmetros específica de cada serviço. 
Para mais informações sobre o Service, consultar a [documentação oficial do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/).

A criação do service pode ser feita através de um arquivo yaml, e o arquivo referente ao banco de dados Mysql poderia seguir o modelo abaixo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: labwordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 3308
  selector:
    app: wordpress
  clusterIP: None

```
Criado o arquivo YAML, basta fazer o apply  
`kubectl apply -f mysql-svc.yaml`
 
O arquivo referente ao serviço Wordpress seguiria a mesma estrutura, apenas referenciando os parâmetros específicos deste.

### 4. Criação do Persistent Volume Claim

Para a persistência de dados, pode-se utilizar Persistent Volume Claim, que é uma solicitação de armazenamento realizada por um usuário. Os serviços MySQL e Wordpress precisam de um PersistentVolume para armazenar dados, e assim que um PersistentVolumeClaim é criado, um PersistentVolume é provisionado dinamicamente de acordo com a configuração do StorageClass.
Para mais informações sobre o PersistentVolumeClaim, consultar a [documentação oficial do Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims).

A criação do PersistentVolumeClaim pode ser feita através de um arquivo yaml, e o arquivo referente ao banco de dados Mysql poderia seguir o modelo abaixo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: labwordpress
  labels:
    app: wordpress
spec:
  accessModes:
    -  ReadWriteOnce
  resources:
    requests:
      storage: 3Gi 
```
Criado o arquivo YAML, basta fazer o apply  
`kubectl apply -f mysql-pvc.yaml`
 
O arquivo referente ao serviço Wordpress seguiria a mesma estrutura, apenas referenciando os parâmetros específicos deste.


### 5. Criação do Deployment

O último arquivo dos diretórios será o deployment, um objeto de API, que irá assegurar o gerenciamento do conjunto de Pods que foram especificados. A criação do Deployment também pode ser feita através de um arquivo yaml.
Para mais informações sobre o Deployment, consultar a [documentação oficial do Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

Para fazer o apply do [deployment](https://github.com/ricardothadeu/Wordpress-Mysql-Kubernetes/blob/main/wordpress/wordpress-deployment.yaml) do mySQL:
`kubectl apply -f mysql-deployment.yaml`  
Para fazer o apply do [deployment](https://github.com/ricardothadeu/Wordpress-Mysql-Kubernetes/blob/main/mysql/mysql-deployment.yaml) do Wordpress:  
`kubectl apply -f wordpress-deployment.yaml`    
 
### 6. Criação do Ingress

O último arquivo de configuração será o Ingress, um objeto de API, que faz o gerenciamento do acesso externo aos serviços em um cluster.
Para mais informações sobre o Ingress, consultar a [documentação oficial do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/).

A criação do Ingress pode ser feita através de um arquivo yaml, podendo seguir a estrtura abaixo:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wordpress-ingress
  namespace: labwordpress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: 
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wordpress
            port:
              number: 80
```
Criado o arquivo YAML, basta fazer o apply  
`kubectl apply -f ingress.yaml`  


### 7. Verificando o namespace

Para verificar os pods, serviços e deployments do namespace (o namespace desta atividade se chama 'labwordpress'), utilize o comando  
`kubectl get all -n labwordpress`  

Para verificar se os PVCs foram criados corretamente, utilize o comando  
`kubectl get pvc -n labwordpress`

Para verificar o Ingress, utilize o comando  
`kubectl get ingress -n labwordpress`  

Para acessar a aplicação, cole no navegador o ADDRESS exibido pelo comando acima. Se desejar, adicione este endereço IP ao arquivo `/etc/hosts` para associá-lo a uma URL.  


