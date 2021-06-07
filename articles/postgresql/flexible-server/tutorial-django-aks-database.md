---
title: 'Tutorial: Implantar o Django no cluster AKS com o Servidor Flexível do PostgreSQL usando a CLI do Azure'
description: Saiba como criar e implantar rapidamente o Django no AKS com o Banco de Dados do Azure para PostgreSQL – Servidor Flexível.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9315e6fd7dd9880d20108e3f0ed28cd32904f1a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791527"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Tutorial: Implantar o aplicativo Django no AKS com o Banco de Dados do Azure para PostgreSQL – Servidor Flexível

Neste guia de início rápido, você implanta um aplicativo Django no cluster AKS (Serviço de Kubernetes do Azure) com o Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia) usando a CLI do Azure.

O **[AKS](../../aks/intro-kubernetes.md)** é um serviço de Kubernetes gerenciado que permite a implantação e o gerenciamento de clusters rapidamente. O **[Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia)](overview.md)** é um serviço de banco de dados totalmente gerenciado, projetado para proporcionar um controle mais granular e flexibilidade nas funções de gerenciamento de banco de dados e definições de configuração.

> [!NOTE]
> - O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está atualmente na versão prévia pública
> - Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes, do Django e do PostgreSQL.

## <a name="pre-requisites"></a>Pré-requisitos
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Use o [Azure Cloud Shell](../../cloud-shell/quickstart.md) usando o ambiente bash.

   [![Inserir inicialização](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)  
- Se preferir, [instale](/cli/azure/install-azure-cli) a CLI do Azure para executar comandos de referência da CLI.
  - Se estiver usando uma instalação local, entre com a CLI do Azure usando o comando [az login](/cli/azure/reference-index#az_login).  Para concluir o processo de autenticação, siga as etapas exibidas no terminal.  Confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli) para obter outras opções de entrada.
  - Quando solicitado, instale as extensões da CLI do Azure no primeiro uso.  Para obter mais informações sobre extensões, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).
  - Execute [az version](/cli/azure/reference-index?#az_version) para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute [az upgrade](/cli/azure/reference-index?#az_upgrade). Este artigo exige a versão mais recente da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Se estiver executando comandos neste início rápido localmente (em vez de no Azure Cloud Shell), execute-os como administrador.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Vamos criar um grupo de recursos, *django-project* usando o comando [az group create][az-group-create] na localização *eastus*.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> A localização do grupo de recursos é onde os metadados do grupo de recursos são armazenados. Também é onde seus recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos.

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar cluster AKS

Use o comando [az aks create](/cli/azure/aks#az_aks_create) para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. Isso levará vários minutos.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster.

> [!NOTE]
> Durante a criação de um cluster do AKS, um segundo grupo de recursos é criado automaticamente para armazenar os recursos do AKS. Confira [Por que são criados dois grupos de recursos com o AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

> [!NOTE]
> O comando acima usa a localização padrão para o [arquivo de configuração do Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), que é `~/.kube/config`. Especifique outra localização para o arquivo de configuração do Kubernetes usando *--file*.

Para verificar a conexão com o cluster, use o comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para retornar uma lista dos nós de cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó criado nas etapas anteriores. Verifique se o status do nó é *Pronto*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Criar um Banco de Dados do Azure para PostgreSQL – Servidor Flexível
Crie um servidor flexível com o comando [az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create). O seguinte comando criará um servidor usando valores e padrões de serviço do contexto local da CLI do Azure:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

O servidor criado terá os atributos abaixo:
- Um banco de dados vazio, ```postgres```, é criado quando o servidor é provisionado pela primeira vez. Neste início rápido, usaremos este banco de dados.
- Nome do servidor gerado automaticamente, nome de usuário do administrador, senha de administrador, nome do grupo de recursos (caso ainda não tenha sido especificado no contexto local). Além disso, o servidor e o grupo de recursos estarão na mesma localização
- Padrões de serviço para as configurações de servidor restantes: nível de computação (Uso Geral), tamanho da computação/SKU (Standard_D2s_v3 que usa 2vCores), período de retenção de backup (7 dias) e versão do PostgreSQL (12)
- O uso do argumento de acesso público permite que você crie um servidor com acesso público protegido por regras de firewall. Fornecendo seu endereço IP para adicionar a regra de firewall para permitir o acesso de seu computador cliente.
- Como o comando está usando o contexto local, ele criará o servidor no grupo de recursos ```django-project``` e na região ```eastus```.


## <a name="build-your-django-docker-image"></a>Criar sua imagem do Docker do Django

Crie um [aplicativo Django](https://docs.djangoproject.com/en/3.1/intro/) ou use seu projeto Django existente. Verifique se o código está nesta estrutura de pastas.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Atualize ```ALLOWED_HOSTS``` no ```settings.py``` para verificar se o aplicativo Django usa o IP externo que é atribuído ao aplicativo Kubernetes.

```python
ALLOWED_HOSTS = ['*']
```

Atualize a seção ```DATABASES={ }``` no arquivo ```settings.py```. O snippet de código abaixo está lendo o host de banco de dados, o nome de usuário e a senha do arquivo de manifesto do Kubernetes.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Gerar um arquivo requirements.txt
Crie um arquivo ```requirements.txt``` para listar as dependências para o aplicativo Django. Veja um arquivo ```requirements.txt``` de exemplo. Você pode usar [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) para gerar um arquivo requirements.txt para seu aplicativo existente.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Criar um Dockerfile
Crie um arquivo chamado ```Dockerfile``` e copie o snippet de código abaixo. Esse Dockerfile na configuração do Python 3.8 e da instalação de todos os requisitos listados no arquivo requirements.txt.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Crie sua imagem
Verifique se você está no diretório ```my-django-app``` em um terminal usando o comando ```cd```. Execute o seguinte comando para criar sua imagem de painel de anúncios:

``` bash

docker build --tag myblog:latest .

```

Implante sua imagem no [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) ou no [Registro de Contêiner do Azure](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se você estiver usando o ACR (Registro de Contêiner do Azure), execute o comando ```az aks update``` para anexar a conta do ACR ao cluster do AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Criar o arquivo de manifesto do Kubernetes

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Vamos criar um arquivo de manifesto chamado ```djangoapp.yaml``` e copiar na definição YAML a seguir.

>[!IMPORTANT]
> - Substitua ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` pelo nome e pela marca da imagem do Docker do Django real, por exemplo, ```docker-hub-user/myblog:latest```.
> - Atualize a seção ```env``` abaixo com seu ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` e ```YOUR-DATABASE-PASSWORD``` do seu servidor flexível postgres.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Implantar Django no cluster AKS
Implante o aplicativo usando o comando [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e especifique o nome do manifesto YAML:

```console
kubectl apply -f djangoapp.yaml
```

A seguinte saída de exemplo mostra as Implantações e os Serviços criados com êxito:

```output
deployment "django-app" created
service "python-svc" created
```

Uma implantação ```django-app``` permite que você descreva detalhes de sua implantação, como quais imagens usam para o aplicativo, o número de pods e a configuração de pod. Um serviço ```python-svc``` é criado para expor o aplicativo por meio de um IP externo.

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service django-app --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *django-app* de exemplo é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Agora abra um navegador da Web para o endereço IP externo de sua exibição de serviço do aplicativo Django.  

>[!NOTE]
> - No momento, o site do Django não está usando HTTPS. É recomendável [HABILITAR o TLS com os próprios certificados](../../aks/ingress-own-tls.md).
> - Você pode habilitar o [roteamento HTTP](../../aks/http-application-routing.md) para o cluster. Quando o roteamento http está habilitado, ele configura um controlador de entrada em seu cluster AKS. À medida que os aplicativos são implantados, a solução também cria nomes DNS publicamente acessíveis para os terminais de aplicativos.

## <a name="run-database-migrations"></a>Executar migrações de banco de dados

Para qualquer aplicativo Django, você precisaria executar uma migração de banco de dados ou coletar arquivos estáticos. Você pode executar esses comandos do shell Django usando ```$ kubectl exec <pod-name> -- [COMMAND]```.  Antes de executar o comando, você precisa encontrar o nome do pod usando ```kubectl get pods```. 

```bash
$ kubectl get pods
```

Você verá uma saída como esta
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Depois que o nome do pod for encontrado, você poderá executar migrações de banco de dados Django com o comando ```$ kubectl exec <pod-name> -- [COMMAND]```. Observe que ```/code/``` é o diretório de trabalho para o projeto definir no ```Dockerfile``` acima.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

A saída teria uma aparência como 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Se você encontrar problemas, execute ```kubectl logs <pod-name>``` para ver qual exceção é gerada pelo seu aplicativo. Se o aplicativo estiver funcionando com êxito, você verá uma saída como esta ao executar ```kubectl logs```.

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar cobranças do Azure, limpe recursos desnecessários.  Quando o cluster não for mais necessário, use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão](../../aks/kubernetes-service-principal.md#additional-considerations). Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [acessar o painel da Web do Kubernetes](../../aks/kubernetes-dashboard.md) para seu cluster do AKS
- Saiba como [habilitar a implantação contínua](../../aks/deployment-center-launcher.md)
- Saiba como [escalar seu cluster](../../aks/tutorial-kubernetes-scale.md)
- Saiba como gerenciar seu [servidor flexível postgres](./quickstart-create-server-cli.md)
- Saiba como [configurar parâmetros de servidor](./howto-configure-server-parameters-using-cli.md) para seu servidor de banco de dados.
