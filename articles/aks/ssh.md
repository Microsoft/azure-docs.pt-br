---
title: SSH nos nós de cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar uma conexão SSH com o nós de cluster do Serviço de Kubernetes do Azure (AKS) para tarefas de manutenção e solução de problemas.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/05/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031635"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Conectar com SSH aos nós de cluster do Serviço de Kubernetes do Azure (AKS) para manutenção ou solução de problemas

Em todo o ciclo de vida do seu cluster do Serviço de Kubernetes do Azure (AKS), você precisa acessar um nó do AKS. Esse acesso pode ser para manutenção, coleção de logs ou outras operações de solução de problemas. Os nós do AKS são VMs do Linux, portanto, portanto, você pode acessá-los usando SSH. Para fins de segurança, os nós do AKS não são expostos à internet.

Este artigo mostra como criar uma conexão SSH com um nó do AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você também precisa da CLI do Azure versão 2.0.59 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Adicionar sua chave SSH pública

Por padrão, as chaves SSH são geradas quando você cria um cluster do AKS. Se você não especificar suas próprias chaves SSH quando tiver criado o cluster do AKS, adicione suas chaves SSH públicas aos nós do AKS.

Para adicionar sua chave SSH a um nó do AKS, conclua as seguintes etapas:

1. Obtenha o nome do grupo de recursos para os recursos de cluster do AKS usando [az aks show][az-aks-show]. Forneça seu próprio grupo de recursos de núcleo e o nome do cluster do AKS:

    ```azurecli
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Liste as VMs no grupo de recursos de cluster do AKS usando o comando [az vm list][az-vm-list]. Essas VMs são seus nós do AKS:

    ```azurecli
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_chinaeast -o table
    ```

    O exemplo a seguir mostra a saída dos nós do AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_chinaeast  chinaeast
    ```

1. Para adicionar suas chaves SSH ao nó, use o comando [az vm user update][az-vm-user-update]. Forneça o nome do grupo de recursos e, em seguida, um de nós do AKS obtidos na etapa anterior. Por padrão, o nome de usuário para os nós do AKS é *azureuser*. Forneça o local do seu próprio local de chave pública do SSH, como *~/.ssh/id_rsa.pub* ou cole o conteúdo da sua chave pública SSH:

    ```azurecli
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Obter o endereço do nó do AKS

Os nós do AKS não estão expostos publicamente à internet. Em relação ao SSH para os nós do AKS, você deve usar o endereço IP privado. A próxima etapa, você cria um pod de auxiliar no cluster do AKS que permite que você SSH para esse endereço IP do nó.

Exiba o endereço IP privado de um nó de cluster do AKS usando o comando [az vm list-ip-addresses][az-vm-list-ip-addresses]. Forneça seu próprio nome do grupo de recursos do AKS cluster obtido na etapa [az-aks-show][az-aks-show] anterior:

```azurecli
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_chinaeast -o table
```

A saída de exemplo a seguir mostra os endereços IP privados dos nós do AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Criar a conexão SSH

Para criar uma conexão SSH para um nó do AKS, execute um pod auxiliar no cluster do AKS. Este pod auxiliar fornece acesso de SSH no cluster e depois acesso ao nó SSH adicional. Para criar e usar esse podo auxiliar, conclua as seguintes etapas:

1. Execute uma imagem de contêiner `debian` e anexe uma sessão de terminal a ela. Esse contêiner pode ser usado para criar uma sessão SSH com qualquer nó no cluster do AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. A imagem base do Debian não inclui componentes SSH. Depois que a sessão de terminal estiver conectada ao contêiner, instale um cliente SSH usando `apt-get` da seguinte maneira:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Em uma nova janela de terminal não conectada ao seu contêiner, liste os pods no seu cluster do AKS usando o comando [kubectl get pods][kubectl-get]. O pod criado na etapa anterior começa com o nome *aks-ssh*, conforme mostrado no exemplo a seguir:

    ```
    $ kubectl get pods

    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Na primeira etapa deste artigo, você adicionou sua chave SSH pública no nó do AKS. Agora, copie sua chave SSH privada no pod. Essa chave privada é usada para criar o SSH em nós do AKS.

    Forneça seu próprio nome do pod *aks-ssh* obtido na etapa anterior. Se necessário, altere *~/.ssh/id_rsa* para o local de sua chave SSH privada:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Novamente na sessão de terminal para o seu contêiner, atualize as permissões na chave SSH privada `id_rsa` para que seja somente leitura para o usuário:

    ```console
    chmod 0600 id_rsa
    ```

1. Agora, crie uma conexão SSH para o nó do AKS. Novamente, o nome de usuário para os nós do AKS é *azureuser*. Aceite o prompt para continuar com a conexão quando a chave SSH for definida como confiável pela primeira vez. Você receberá o prompt de bash do seu nó do AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Remover o acesso SSH

Quando terminar, `exit` da sessão SSH e, em seguida, `exit` da sessão interativa do contêiner. Quando essa sessão do contêiner for fechada, o pod usado para acesso de SSH do cluster do AKS será excluído.

## <a name="next-steps"></a>Próximas etapas

Se precisar de dados adicionais de solução de problemas, você poderá [exibir os logs de kubelet][view-kubelet-logs] ou [exibir os logs do nó mestre de Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-vm-list]: https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-list
[az-vm-user-update]: https://docs.azure.cn/zh-cn/cli/vm/user?view=azure-cli-latest#az-vm-user-update
[az-vm-list-ip-addresses]: https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest