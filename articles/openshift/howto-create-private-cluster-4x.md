---
title: Criar um cluster privado do Red Hat OpenShift 4 no Azure
description: Saiba como criar e gerenciar um cluster privado do Red Hat OpenShift no Azure executando o OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 5a6eafa6b2380ee61c0de2e329a1751ec7d59c1f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216064"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Criar um cluster privado do Red Hat OpenShift 4 no Azure

Neste artigo, você vai preparar seu ambiente para criar clusters privados do Red Hat OpenShift no Azure executando o OpenShift 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as sub-redes necessárias
> * Implantar um cluster com um ponto de extremidade privado do servidor de API e um controlador de entrada privado

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

### <a name="register-the-resource-providers"></a>Registrar os provedores de recursos

1. Se você tem várias assinaturas do Azure, especifique a ID de assinatura relevante:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre o provedor de recursos `Microsoft.RedHatOpenShift`:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. Registre o provedor de recursos `Microsoft.Compute`:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. Registre o provedor de recursos `Microsoft.Storage`:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obter um segredo de pull do Red Hat (opcional)

Um segredo de pull do Red Hat permite que o cluster acesse registros de contêiner do Red Hat juntamente com conteúdo adicional. Esta etapa é opcional, mas recomendada.

1. **[Navegue até o portal do gerenciador de clusters do Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça logon.**

   Você precisará fazer logon em sua conta do Red Hat ou criar uma conta do Red Hat com seu email empresarial e aceitar os termos e condições.

2. **Clique em Baixar segredo de pull.**

Mantenha o arquivo `pull-secret.txt` salvo em algum lugar seguro; ele será usado em cada criação de cluster.

Quando executar o comando `az aro create`, você pode fazer referência ao segredo de pull usando o parâmetro `--pull-secret @pull-secret.txt`. Execute `az aro create` no diretório em que você armazenou o arquivo `pull-secret.txt`. Caso contrário, substitua `@pull-secret.txt` por `@<path-to-my-pull-secret-file`.

Se você estiver copiando o segredo de pull ou fazendo referência a ele em outros scripts, o segredo de pull deverá ser formatado como uma cadeia de caracteres JSON válida.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Em seguida, você criará uma rede virtual contendo duas sub-redes vazias.

1. **Defina as variáveis a seguir.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Criar um grupo de recursos**

    Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o comando [az group create][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Crie uma rede virtual.**

    Os clusters do Red Hat OpenShift no Azure que executam o OpenShift 4 exigem uma rede virtual com duas sub-redes vazias para os nós mestre e de trabalho.

    Crie uma rede virtual no mesmo grupo de recursos que você criou anteriormente.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    A seguinte saída de exemplo mostra a rede virtual criada com êxito:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Adicione uma sub-rede vazia para os nós mestres.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Adicione uma sub-rede vazia para os nós de trabalho.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Desabilite as políticas de ponto de extremidade privado de sub-rede](../private-link/disable-private-link-service-network-policy.md) na sub-rede mestra.** Isso é necessário para conectar e gerenciar o cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Criar o cluster

Execute o comando a seguir para criar um cluster. Opcionalmente, você pode [passar o segredo de pull do Red Hat](#get-a-red-hat-pull-secret-optional) que permite que o cluster acesse registros de contêiner do Red Hat juntamente com conteúdo adicional.

>[!NOTE]
> Se você estiver copiando/colando comandos e usando um dos parâmetros opcionais, exclua as hashtags iniciais e o texto do comentário à direita. Além de isso, feche o argumento na linha anterior do comando com uma barra invertida à direita.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Depois de executar o comando `az aro create`, normalmente leva cerca de 35 minutos para criar um cluster.

>[!IMPORTANT]
> Se você optar por especificar um domínio personalizado, por exemplo **foo.example.com**, o console do OpenShift estará disponível em uma URL como `https://console-openshift-console.apps.foo.example.com`, em vez de no domínio interno `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Por padrão, o OpenShift usa certificados autoassinados para todas as rotas criadas em `*.apps.<random>.<location>.aroapp.io`.  Se você optar pelo DNS personalizado após a conexão com o cluster, será necessário seguir a documentação do OpenShift para [configurar uma AC personalizada para o controlador de entrada](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e uma [AC personalizada para o servidor de API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Conectar ao cluster privado

Você pode fazer logon no cluster usando o usuário `kubeadmin`.  Execute o comando a seguir para localizar a senha para o usuário `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A saída de exemplo a seguir mostra que a senha estará em `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Você pode encontrar a URL do console de cluster executando o comando a seguir, que será semelhante a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Para se conectar a um cluster privado do Red Hat OpenShift no Azure, você precisará executar a etapa a seguir de um host que esteja na rede virtual criada ou em uma rede virtual [emparelhada](../virtual-network/virtual-network-peering-overview.md) com a rede virtual na qual o cluster foi implantado.

Inicie a URL do console em um navegador e faça logon usando as credenciais `kubeadmin`.

![Instantâneo que mostra a tela de logon do Azure Red Hat OpenShift.](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instalar a CLI do OpenShift

Quando você estiver conectado ao console Web do OpenShift, clique no **?** no canto superior direito e então em **Ferramentas de Linha de Comando**. Baixe a versão apropriada para seu computador.

![Imagem mostra a tela de logon do Azure Red Hat OpenShift](media/aro4-download-cli.png)

Você também pode baixar a versão mais recente da CLI apropriada para seu computador de <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

## <a name="connect-using-the-openshift-cli"></a>Conectar-se usando a CLI do OpenShift

Recupere o endereço do servidor de API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Para se conectar a um cluster privado do Red Hat OpenShift no Azure, você precisará executar a etapa a seguir de um host que esteja na rede virtual criada ou em uma rede virtual [emparelhada](../virtual-network/virtual-network-peering-overview.md) com a rede virtual na qual o cluster foi implantado.

Faça logon no servidor de API do cluster OpenShift usando o comando a seguir. Substitua **\<kubeadmin password>** pela senha que você recuperou.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, foi implantado um cluster do Red Hat OpenShift no Azure que executa o OpenShift 4. Você aprendeu a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as sub-redes necessárias
> * Implantar um cluster
> * Conectar ao cluster com o usuário `kubeadmin`

Prossiga para o próximo artigo para saber como configurar o cluster para autenticação usando o Azure Active Directory.


* [Configurar a autenticação no Azure Active Directory usando a linha de comando](configure-azure-ad-cli.md)


* [Configurar a autenticação com o Azure Active Directory usando o Portal do Azure e o console Web do OpenShift](configure-azure-ad-cli.md)
