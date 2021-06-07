---
title: 'Tutorial: Implantar o WordPress no cluster do AKS com o Servidor Flexível MySQL usando a CLI do Azure'
description: Saiba como criar e implantar rapidamente o WordPress no AKS com o Banco de Dados do Azure para MySQL – Servidor Flexível.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: vc, devx-track-azurecli
ms.openlocfilehash: 0c6211f4cd647addd6f1d18a153695d16a9d9952
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770139"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Tutorial: Implantar o aplicativo WordPress no AKS com o Banco de Dados do Azure para MySQL – Servidor Flexível

Neste guia de início rápido, você implanta um aplicativo WordPress no cluster AKS (Serviço de Kubernetes do Azure) com o Banco de Dados do Azure para MySQL – Servidor Flexível (versão prévia) usando a CLI do Azure. 
O **[AKS](../../aks/intro-kubernetes.md)** é um serviço de Kubernetes gerenciado que permite a implantação e o gerenciamento de clusters rapidamente. O **[Banco de Dados do Azure para MySQL – Servidor Flexível (versão prévia)](overview.md)** é um serviço de banco de dados totalmente gerenciado projetado para fornecer controle e flexibilidade mais granulares nas funções de gerenciamento de banco de dados e definições de configuração. No momento, o servidor flexível está em versão prévia.

> [!NOTE]
> - Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública
> - Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes, do WordPress e do MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão mais recente da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Se estiver executando comandos neste início rápido localmente (em vez de no Azure Cloud Shell), execute-os como administrador.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Vamos criar um grupo de recursos, *wordpress-project* usando o comando [az group create][az-group-create] na localização *eastus*.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> A localização do grupo de recursos é onde os metadados do grupo de recursos são armazenados. Também é onde seus recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos.

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar cluster AKS

Use o comando [az aks create](/cli/azure/aks#az_aks_create) para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. Isso levará vários minutos.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
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
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
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

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Criar um Banco de Dados do Azure para MySQL – Servidor Flexível
Crie um servidor flexível com o comando [az mysql flexible-server create](/cli/azure/mysql/flexible-server). O seguinte comando criará um servidor usando valores e padrões de serviço do contexto local da CLI do Azure:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

O servidor criado terá os atributos abaixo:
- Um banco de dados vazio, ```flexibleserverdb```, é criado quando o servidor é provisionado pela primeira vez. Neste início rápido, usaremos este banco de dados.
- Nome do servidor gerado automaticamente, nome de usuário do administrador, senha de administrador, nome do grupo de recursos (caso ainda não tenha sido especificado no contexto local). Além disso, o servidor e o grupo de recursos estarão na mesma localização
- Padrões de serviço para configurações de servidor restantes: nível de computação (Intermitente), tamanho/SKU da computação (B1MS), período de retenção de backup (7 dias) e versão do MySQL (5.7)
- O uso do argumento de acesso público permite que você crie um servidor com acesso público protegido por regras de firewall. Fornecendo seu endereço IP para adicionar a regra de firewall para permitir o acesso de seu computador cliente.
- Como o comando está usando o contexto local, ele criará o servidor no grupo de recursos ```wordpress-project``` e na região ```eastus```.


### <a name="build-your-wordpress-docker-image"></a>Criar sua imagem do docker do WordPress

Baixe a versão [mais recente do WordPress](https://wordpress.org/download/). Crie um diretório ```my-wordpress-app``` para seu projeto e use esta estrutura de pasta simples

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Renomeie ```wp-config-sample.php``` para ```wp-config.php``` e substitua as linhas 21 a 32 por este snippet de código. O snippet de código abaixo está lendo o host de banco de dados, o nome de usuário e a senha do arquivo de manifesto do Kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Criar um Dockerfile
Crie um Dockerfile e copie este snippet de código. Este Dockerfile na configuração do servidor Web Apache com PHP e na habilitação da extensão mysqli.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Criar a sua imagem do Docker
Verifique se você está no diretório ```my-wordpress-app``` em um terminal usando o comando ```cd```. Execute o seguinte comando para criar a imagem:

``` bash

docker build --tag myblog:latest .

```

Implante sua imagem no [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) ou no [Registro de Contêiner do Azure](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se você estiver usando o ACR (Registro de Contêiner do Azure), execute o comando ```az aks update``` para anexar a conta do ACR ao cluster do AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Criar o arquivo de manifesto do Kubernetes

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Vamos criar um arquivo de manifesto chamado `mywordpress.yaml` e copiar na definição YAML a seguir.

>[!IMPORTANT]
> - Substitua ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` pelo nome e pela marca da imagem do docker do WordPress real, por exemplo, ```docker-hub-user/myblog:latest```.
> - Atualize a seção ```env``` abaixo com seu ```SERVERNAME```, ```YOUR-DATABASE-USERNAME``` e ```YOUR-DATABASE-PASSWORD``` do seu servidor flexível MySQL.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Implantar o WordPress no cluster do AKS
Implante o aplicativo usando o comando [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e especifique o nome do manifesto YAML:

```console
kubectl apply -f mywordpress.yaml
```

A seguinte saída de exemplo mostra as Implantações e os Serviços criados com êxito:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *wordpress-blog* é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Procurar WordPress

Abra um navegador da Web para o endereço IP externo do seu serviço a fim de ver a página de instalação do WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Êxito na instalação do WordPress no servidor flexível AKS e MySQL":::

>[!NOTE]
> - No momento, o site do WordPress não está usando HTTPS. É recomendável [HABILITAR o TLS com os próprios certificados](../../aks/ingress-own-tls.md).
> - Você pode habilitar o [roteamento HTTP](../../aks/http-application-routing.md) para o cluster.

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar cobranças do Azure, limpe recursos desnecessários.  Quando o cluster não for mais necessário, use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão](../../aks/kubernetes-service-principal.md#additional-considerations). Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [acessar o painel da Web do Kubernetes](../../aks/kubernetes-dashboard.md) para seu cluster do AKS
- Saiba como [escalar seu cluster](../../aks/tutorial-kubernetes-scale.md)
- Saiba como gerenciar seu [servidor flexível MySQL](./quickstart-create-server-cli.md)
- Saiba como [configurar parâmetros de servidor](./how-to-configure-server-parameters-cli.md) para seu servidor de banco de dados.
