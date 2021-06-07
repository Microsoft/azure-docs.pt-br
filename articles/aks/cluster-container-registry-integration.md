---
title: Integrar o registro de contêiner do Azure com o serviço kubernetes do Azure
description: Saiba como integrar o AKS (serviço de kubernetes do Azure) com o ACR (registro de contêiner do Azure)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 19ece696dabc81e643e8a904d506d22e40eaa099
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499145"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure

Quando você estiver usando o ACR (Registro de Contêiner do Azure) com o AKS (Serviço de Kubernetes do Azure), um mecanismo de autenticação precisará ser estabelecido. Essa operação é implementada como parte da experiência da CLI e do portal, concedendo as permissões necessárias ao seu ACR. Este artigo fornece exemplos para configurar a autenticação entre esses dois serviços do Azure. 

Você pode configurar o AKS para a integração de ACR em alguns comandos simples com o CLI do Azure. Essa integração atribui a função AcrPull à identidade gerenciada associada ao cluster AKS.

> [!NOTE]
> Este artigo aborda a autenticação automática entre AKS e ACR. Se você precisar efetuar pull de uma imagem de um registro externo privado, use um [segredo de extração de imagem][Image Pull Secret].

## <a name="before-you-begin"></a>Antes de começar

Esses exemplos precisam de:

* Função de **administrador da conta do Azure** ou **proprietário** na **assinatura do Azure**
* CLI do Azure versão 2.7.0 ou posterior

Para evitar a necessidade de uma função de **administrador de conta do Azure** ou **proprietário** , você pode configurar uma identidade gerenciada manualmente ou usar uma identidade gerenciada existente para autenticar o ACR de AKs. Para obter mais informações, consulte [usar uma identidade gerenciada do Azure para autenticar em um registro de contêiner do Azure](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Criar um novo cluster AKS com integração com ACR

Você pode configurar a integração de AKS e ACR durante a criação inicial do cluster AKS.  Para permitir que um cluster AKS interaja com o ACR, é usada uma Azure Active Directory **identidade gerenciada** . O comando da CLI a seguir permite autorizar um ACR existente em sua assinatura e configurar a função **ACRPull** apropriada para a identidade gerenciada. Forneça valores válidos para os parâmetros abaixo.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Como alternativa, você pode especificar o nome do ACR usando uma ID de recurso ACR, que tem o seguinte formato:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Se você estiver usando um ACR que está localizado em uma assinatura diferente do seu cluster AKS, use a ID de recurso ACR ao anexar ou desanexar de um cluster AKS.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Esta etapa pode levar vários minutos para ser concluída.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurar a integração do ACR para clusters AKS existentes

Integre um ACR existente a clusters AKS existentes fornecendo valores válidos para **ACR-Name** ou **ACR-Resource-ID** como mostrado abaixo.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

ou

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Você também pode remover a integração entre um ACR e um cluster AKS com o seguinte

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

ou

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Trabalhando com ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importar uma imagem para o ACR

Importe uma imagem do Hub do Docker para o ACR executando o seguinte:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implantar a imagem de exemplo do ACR para o AKS

Verifique se você tem as credenciais de AKS adequadas

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Crie um arquivo chamado **ACR-nginx. YAML** que contenha o seguinte. Substitua o nome do recurso do registro por **ACR-Name**. Exemplo: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Em seguida, execute esta implantação em seu cluster AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Você pode monitorar a implantação executando:

```console
kubectl get pods
```

Você deve ter dois pods em execução.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Solução de problemas
* Execute o comando [AZ AKs check-ACR](/cli/azure/aks#az_aks_check_acr) para validar se o registro pode ser acessado do cluster AKs.
* Saiba mais sobre o [diagnóstico ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Saiba mais sobre a [integridade do ACR](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/