# Atividade Kubernetes

Documentação da Atividade de Kubernetes do Programa de Bolsas Compass - DevSecOps.  
O objetivo desta atividade é realizar o deploy de um site Wordpress utilizando o banco de dados MySQL.  

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




