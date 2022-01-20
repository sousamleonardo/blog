Este repositório contém uma pipeline para implantação de um blog em containeres do OpenShift como entrega do trabalho do módulo 4 do MBA de Arquitetura de Soluções da FIAP (mba6aso-2021-2022) realizado pelo Grupo 19.

A aplicação utilizada foi a https://github.com/openshift-instruqt/blog-django-py que foi clonada para este repositório e o banco de dados utilizado foi o PostgreSQL 10 através da utilização da imagem postgresql:10-el8 disponibilizada pela Red Hat.

Para os pods da aplicação foi utilizado um dockerfile customizado https://raw.githubusercontent.com/sousamleonardo/blog/master/Dockerfile que utiliza uma imagem da Red Hat: registry.access.redhat.com/ubi8/python-38

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
# Implantando automaticamente

Este repositório executa automaticamente todos os templates a partir do github actions.

Para que a implantação seja realizada em outra plataforma, os seguintes secrets deste repositório precisarão ser alterados, pois os atuais estão com os valores do aluno owner deste repo.

```
(settings->secrets)

OPENSHIFT_PASSWORD
OPENSHIFT_SERVER
OPENSHIFT_USERNAME
```


# Implantando manualmente

Será necessário o client do OC para execução de comandos remotos. Todo este roteiro prevê a existência do client, autenticação e que haja um projeto (namespace), cujo nome será passado como parâmetro em alguns comandos abaixo.

O primeiro passo será executar o seguinte comando:

```
oc create -f https://raw.githubusercontent.com/sousamleonardo/blog/master/deploy/main.yaml -n "nome do projeto(namespace)"
```

O Arquivo https://raw.githubusercontent.com/sousamleonardo/blog/master/deploy/main.yaml contempla a execução de todos os demais templates na ordem listada acima.

Como resultado da execução do comando acima, é esperada a seguinte saída:

```
persistentvolumeclaim/sample-database-pv created
secret/sample-database-secret created
deploymentconfig.apps.openshift.io/sample-database-to-blog created
service/sample-database-to-blog created
imagestream.image.openshift.io/blog created
buildconfig.build.openshift.io/blog-build created
deployment.apps/blog created
service/blog created
route.route.openshift.io/blog-route created
horizontalpodautoscaler.autoscaling/blog-hpa created
```
Aguarde até que o deployment e todas as configurações sejam executadas (aproximadamente 1 minuto) e liste os nomes dos PODs da aplicação a partir do seguinte comando:

```
oc get pods --selector deployment=blog -o name -n "nome do projeto(namespace)"
```

Como resultado da execução do comando acima, é esperada a seguinte saída (exemplo):

```
pod/blog-7779899fd9-9tph9
pod/blog-7779899fd9-qcvc7
```

Então, como último passo pra deixar o seu blog configurado, execute o comando 

```
oc -n "nome do projeto(namespace)" rsh "nome do pod" .s2i/action_hooks/setup
``` 

que irá realizar a configuração da base de dados e criação do usuário master do blog. Segue um exemplo utilizando-se o nome de um dos PODs listados no comando anterior:

```
oc -n "nome do projeto(namespace)" rsh pod/blog-7779899fd9-9tph9 .s2i/action_hooks/setup
```

Como resultado da execução do comando acima, é esperada a seguinte saída (exemplo):

```
-----> Running Django database table migrations.
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  No migrations to apply.
 -----> Creating predefined Django super user
 -----> Pre-loading Django database with blog posts.
Installed 2 object(s) from 1 fixture(s)
```

Se tudo correu bem, você poderá acessar o seu blog a partir do seguinte link:

```
http://blog-route-fiap-grupo19.apps.na46.prod.nextcle.com/
```

(neste exemplo, o nome do projeto é fiap-grupo19, por isso que o link acima ficou desta maneira).

A rota para o blog será blog-route-"nome do projeto".apps.na46.prod.nextcle.com

Para autenticar no blog, utilize as seguintes credenciais:

```
Username: user
Password: mba12345
```

Estas credenciais são definidas como variáveis e são configuradas na etapa 7 - Deploy da aplicação no POD e associação com o Banco de dados.
