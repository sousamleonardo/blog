Este repositório contém uma pipeline para implantação de um blog em containeres do OpenShift como entrega do trabalho do módulo 4 do MBA de Arquitetura de Soluções da FIAP (mba6aso-2021-2022) realizado pelo Grupo 19.

A aplicação utilizada foi a https://github.com/openshift-instruqt/blog-django-py que foi clonada para este repositório e o banco de dados utilizado foi o PostgreSQL 10 através da utilização da imagem postgresql:10-el8 disponibilizada pela Red Hat.

# Como funciona a implantação?

A implantação do Projeto é feita em 10 passos:

```
1 - Criação do Volume Persistente para o Banco de Dados
2 - Criação do Secret para armazenar informações de usuário/senha/banco
3 - Deployment do Banco de dados
4 - Criação do serviço de Banco de Dados
5 - Criação de repositório de Imagem para a aplicação
6 - Build da aplicação
7 - Deploy da aplicação no POD e associação com o Banco de dados
8 - Criação do serviço da aplicação
9 - Criação da rota da aplicação
10 - Criação da configuração de autoscaling da aplicação

```

# Implantando manualmente

Para implantar o projeto manualmente deverá ser utilizado o seguinte comando:

```
oc create -f https://raw.githubusercontent.com/sousamleonardo/blog/master/deploy/main.yaml -n "nome do projeto(namespace)"
```

O Arquivo https://raw.githubusercontent.com/sousamleonardo/blog/master/deploy/main.yaml contempla a execução de todos os demais templates na ordem listada acima.

# Implantando automaticamente

Este repositório executa automaticamente todos os templates a partir do github actions.
