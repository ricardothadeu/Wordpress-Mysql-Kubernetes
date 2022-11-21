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
Criado o arquivo yaml, basta fazer o apply  
`kubectl apply -f namespace.yaml`

Também pode ser feita pela linha de comando  
`kubectl create ns nome-do-namespace`  
