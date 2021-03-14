---
title: Usar Azure Active Directory identidades gerenciadas por pod no serviço kubernetes do Azure (versão prévia)
description: Saiba como usar identidades gerenciadas gerenciadas pelo Pod do AAD no serviço de kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 8b94c859800c3757842ad56df6e20f215bb13a7d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233489"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Usar Azure Active Directory identidades gerenciadas por pod no serviço kubernetes do Azure (versão prévia)

Azure Active Directory identidades gerenciadas pelo Pod usam primitivos kubernetes para associar [identidades gerenciadas para recursos][az-managed-identities] e identidades do Azure no Azure Active Directory (AAD) com pods. Os administradores criam identidades e associações como primitivos kubernetes que permitem aos pods acessar os recursos do Azure que dependem do AAD como um provedor de identidade.

> [!NOTE]
> Se você tiver uma instalação existente do AADPODIDENTITY, deverá remover a instalação existente. Habilitar esse recurso significa que o componente MIC não é necessário.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de começar

Você deve ter o seguinte recurso instalado:

* O CLI do Azure, versão 2.20.0 ou posterior
* A `azure-preview` versão de extensão 0.5.5 ou posterior

### <a name="limitations"></a>Limitações

* Um máximo de 200 identidades de Pod são permitidas para um cluster.
* Um máximo de 200 exceções de identidade de Pod são permitidas para um cluster.
* As identidades gerenciadas por Pod estão disponíveis somente em pools de nós do Linux.

### <a name="register-the-enablepodidentitypreview"></a>Registrar o `EnablePodIdentityPreview`

Registrar o recurso `EnablePodIdentityPreview`:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalar o `aks-preview` CLI do Azure

Você também precisa do *AKs-preview* CLI do Azure versão 0.4.64 ou posterior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades gerenciadas

Crie um cluster AKS com identidade gerenciada e identidade gerenciada por Pod habilitada. Os comandos a seguir usam [AZ Group Create][az-group-create] para criar um grupo de recursos chamado *MyResource* Group e o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs chamado *myAKSCluster* no grupo de recursos *MyResource* Group.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

Use [AZ AKs Get-Credentials][az-aks-get-credentials] para entrar no seu cluster do AKS. Esse comando também baixa e configura o certificado do `kubectl` cliente em seu computador de desenvolvimento.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Criar um cluster AKS com o plug-in de rede Kubenet

Crie um cluster AKS com o plug-in de rede Kubenet e a identidade gerenciada por Pod habilitada.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Atualizar um cluster AKS existente com o plug-in de rede Kubenet

Atualize um cluster AKS existente com o plug-in de rede Kubnet para incluir a identidade gerenciada por Pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade usando [AZ Identity Create][az-identity-create] e defina as variáveis *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_ID* .

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Atribuir permissões para a identidade gerenciada

A identidade gerenciada *IDENTITY_CLIENT_ID* deve ter permissões de leitor no grupo de recursos que contém o conjunto de dimensionamento de máquinas virtuais do cluster AKs.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Criar uma identidade de Pod

Crie uma identidade de pod para o cluster usando `az aks pod-identity add` .

> [!IMPORTANT]
> Você deve ter as permissões apropriadas, como `Owner` , em sua assinatura para criar a identidade e a associação de função.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Quando você habilita a identidade gerenciada por pod em seu cluster AKS, um AzurePodIdentityException chamado *AKs-addon-Exception* é adicionado ao namespace *Kube-System* . Um AzurePodIdentityException permite que os pods com determinados rótulos acessem o ponto de extremidade do serviço de metadados de instância do Azure (IMDS) sem ser interceptado pelo servidor de identidade gerenciada por nó (NMI). O *AKs-addon-Exception* permite que AKs Complementos de terceiros, como a identidade gerenciada pelo Pod do AAD, operem sem precisar configurar manualmente um AzurePodIdentityException. Opcionalmente, você pode adicionar, remover e atualizar um AzurePodIdentityException usando `az aks pod-identity exception add` ,, `az aks pod-identity exception delete` `az aks pod-identity exception update` ou `kubectl` .

## <a name="run-a-sample-application"></a>Executar um aplicativo de exemplo

Para que um pod use a identidade gerenciada pelo Pod do AAD, o Pod precisa de um rótulo *aadpodidbinding* com um valor que corresponda a um seletor de um *AzureIdentityBinding*. Para executar um aplicativo de exemplo usando a identidade gerenciada pelo Pod do AAD, crie um `demo.yaml` arquivo com o conteúdo a seguir. Substitua *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* pelos valores das etapas anteriores. Substitua *SUBSCRIPTION_ID* pela sua ID de assinatura.

> [!NOTE]
> Nas etapas anteriores, você criou as variáveis *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* . Você pode usar um comando como `echo` para exibir o valor definido para variáveis, por exemplo `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Observe que a definição de Pod tem um rótulo *aadpodidbinding* com um valor que corresponde ao nome da identidade do pod que você executou `az aks pod-identity add` na etapa anterior.

Implante no `demo.yaml` mesmo namespace que a sua identidade de Pod usando `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Verifique se o aplicativo de exemplo é executado com êxito usando `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Verifique se os logs mostram que um token foi adquirido com êxito e que a operação *Get* foi bem-sucedida.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Limpeza

Para remover a identidade gerenciada pelo Pod do AAD do cluster, remova o aplicativo de exemplo e a identidade do Pod do cluster. Em seguida, remova a identidade.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre identidades gerenciadas, consulte [Identidades gerenciadas para recursos do Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
