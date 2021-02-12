---
title: Implantar um aplicativo Java com o Liberty aberto ou o WebSphere Liberty em um cluster do AKS (serviço kubernetes do Azure)
description: Implante um aplicativo Java com o Liberty ou o WebSphere Liberty aberto em um cluster do AKS (serviço kubernetes do Azure).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, Jacarta, Java, microprofile, Open-Liberty, WebSphere-Liberty, AKs, kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007118"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Implantar um aplicativo Java com o Liberty aberto ou o WebSphere Liberty em um cluster do AKS (serviço kubernetes do Azure)

Este artigo demonstra como:  
* Execute seu aplicativo Java, Java EE, Jacarta EE ou microprofile no tempo de execução do Liberty ou do WebSphere Liberty aberto.
* Crie a imagem do Docker de aplicativo usando imagens de contêiner do Liberty abertas.
* Implante o aplicativo em contêineres em um cluster AKS usando o operador Open Liberty.   

O operador Open Liberty simplifica a implantação e o gerenciamento de aplicativos executados em clusters kubernetes. Com o operador Open Liberty, você também pode executar operações mais avançadas, como a coleta de rastreamentos e despejos. 

Para obter mais detalhes sobre o Liberty aberto, consulte [a página Abrir projeto do Liberty](https://openliberty.io/). Para obter mais detalhes sobre o IBM WebSphere Liberty, consulte [a página do produto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Este artigo exige a versão mais recente da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.
* Se estiver executando os comandos neste guia localmente (em vez de Azure Cloud Shell):
  * Prepare um computador local com o sistema operacional semelhante ao Unix instalado (por exemplo, Ubuntu, macOS, subsistema do Windows para Linux).
  * Instale uma implementação Java SE (por exemplo, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Instale o [Maven](https://maven.apache.org/download.cgi) 3.5.0 ou superior.
  * Instale o [Docker](https://docs.docker.com/get-docker/) para o seu sistema operacional.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados.  

Crie um grupo de recursos chamado *Java-Liberty-Project* usando o comando [AZ Group Create](/cli/azure/group#az_group_create) no local *eastus* . Esse grupo de recursos será usado posteriormente para criar a instância do ACR (registro de contêiner do Azure) e o cluster AKS. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Criar uma instância de ACR

Use o comando [AZ ACR Create](/cli/azure/acr#az_acr_create) para criar a instância do ACR. O exemplo a seguir cria uma instância de ACR chamada *youruniqueacrname*. Certifique-se de que *youruniqueacrname* seja exclusivo no Azure.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Após um curto período, você deverá ver uma saída JSON que contém:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Conectar-se à instância do ACR

Você precisará entrar na instância do ACR antes de poder enviar uma imagem por push a ela. Execute os seguintes comandos para verificar a conexão:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Você deverá ver `Login Succeeded` no final da saída do comando se tiver feito logon na instância do ACR com êxito.

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Use o comando [az aks create](/cli/azure/aks#az_aks_create) para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. Isso levará vários minutos.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster, incluindo o seguinte:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Conectar-se ao cluster AKS

Para gerenciar um cluster do Kubernetes, use [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> O comando acima usa a localização padrão para o [arquivo de configuração do Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), que é `~/.kube/config`. Especifique outra localização para o arquivo de configuração do Kubernetes usando *--file*.

Para verificar a conexão com o cluster, use o comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para retornar uma lista dos nós de cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó criado nas etapas anteriores. Verifique se o status do nó é *Pronto*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Instalar o operador Open Liberty

Depois de criar e conectar-se ao cluster, instale o [operador Open Liberty](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) executando os comandos a seguir.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Criar imagem de aplicativo

Para implantar e executar seu aplicativo do Liberty no cluster AKS, use o contêiner para colocar seu aplicativo como uma imagem do Docker usando imagens de contêiner do Liberty ou [imagens de contêiner do WebSphere Liberty](https://github.com/WASdev/ci.docker) [abertas](https://github.com/OpenLiberty/ci.docker) .

1. Clone o código de exemplo deste guia. O exemplo está no [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Altere o diretório para `javaee-app-simple-cluster` de seu clone local.
1. Execute `mvn clean package` para empacotar o aplicativo.
1. Execute `mvn liberty:dev` para testar o aplicativo. Você deverá ver `The defaultServer server is ready to run a smarter planet.` na saída do comando se tiver êxito. Use `CTRL-C` para interromper o aplicativo.
1. Execute um dos comandos a seguir para criar a imagem do aplicativo e enviá-la por push para a instância do ACR.
   * Crie com a imagem de base do Liberty aberta se preferir usar o Liberty aberto como um tempo de execução simples de™ de código aberto Java:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Crie com a imagem base do WebSphere Liberty se preferir usar uma versão comercial do Open Liberty:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Implantar aplicativo no cluster AKS

Siga as etapas abaixo para implantar o aplicativo Liberty no cluster AKS.

1. Crie um segredo de pull para que o cluster AKS seja autenticado para extrair a imagem da instância do ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Verifique se o diretório de trabalho atual é `javaee-app-simple-cluster` do seu clone local.
1. Execute os comandos a seguir para implantar seu aplicativo Liberty com 3 réplicas no cluster AKS. A saída do comando também é mostrada em linha.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Aguarde até que você veja `3/3` sob a `READY` coluna e `3` , na `AVAILABLE` coluna, use `CTRL-C` para interromper o processo de `kubectl` inspeção.

### <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de balanceador de carga kubernetes expõe o front-end do aplicativo à Internet. Esse processo pode demorar um pouco para ser concluído.

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Depois que o endereço *IP externo* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o `kubectl` processo de inspeção.

Abra um navegador da Web para o endereço IP externo do seu serviço ( `52.152.189.57` para o exemplo acima) para ver o home page do aplicativo. Você deve ver o nome do pod de suas réplicas de aplicativo exibidas na parte superior esquerda da página. Aguarde alguns minutos e atualize a página para ver um nome de Pod diferente exibido devido ao balanceamento de carga fornecido pelo cluster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Aplicativo de Liberty Java implantado com êxito em AKS":::

>[!NOTE]
> - Atualmente, o aplicativo não está usando HTTPS. É recomendável [HABILITAR o TLS com os próprios certificados](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar cobranças do Azure, você deve limpar recursos desnecessários.  Quando o cluster não for mais necessário, use o comando [AZ Group Delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o serviço de contêiner, o registro de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre as referências usadas neste guia:

* [Serviço de Kubernetes do Azure](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Abra o Liberty](https://openliberty.io/)
* [Abrir operador de Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Abrir configuração do servidor do Liberty](https://openliberty.io/docs/ref/config/)
* [Plug-in do Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Abrir imagens de contêiner do Liberty](https://github.com/OpenLiberty/ci.docker)
* [Imagens de contêiner do WebSphere Liberty](https://github.com/WASdev/ci.docker)
