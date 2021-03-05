---
title: Criar workspaces com a CLI do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar a extensão de CLI do Azure para o aprendizado de máquina para criar um novo espaço de trabalho Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8ea3f36d4e63b3439691772c25e7b3af77dcdb02
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210029"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Criar um workspace para o Azure Machine Learning com a CLI do Azure


Neste artigo, você aprenderá a criar um workspace do Azure Machine Learning usando a CLI do Azure. A CLI do Azure fornece comandos para gerenciar recursos do Azure. A extensão de machine learning para a CLI fornece comandos para trabalhar com recursos do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](/cli/azure/install-azure-cli).

    Se você usar o [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), a CLI será acessada por meio do navegador e residirá na nuvem.

## <a name="limitations"></a>Limitações

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à assinatura do Azure

> [!IMPORTANT]
> Se você estiver usando o Azure Cloud Shell, ignore esta seção. O Cloud Shell autentica automaticamente você usando a conta que você faz logon em sua assinatura do Azure.

Há várias maneiras de se autenticar em sua assinatura do Azure por meio da CLI. O mais simples é autenticar-se interativamente usando um navegador. Para se autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Para outros métodos de autenticação, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="install-the-machine-learning-extension"></a>Instalar a extensão de machine learning

Para instalar a extensão de machine learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Criar um workspace

O workspace do Azure Machine Learning se baseia nos seguintes serviços ou nas seguintes entidades do Azure:

> [!IMPORTANT]
> Se você não especificar um serviço existente do Azure, será criado um automaticamente durante a criação do workspace. Você sempre precisa especificar um grupo de recursos. Ao anexar sua própria conta de armazenamento, verifique se ela atende aos seguintes critérios:
>
> * A conta de armazenamento _não_ é uma conta premium (Premium_LRS e Premium_GRS)
> * O blob do Azure e os recursos de arquivo do Azure habilitados
> * O namespace hierárquico (ADLS Gen 2) está desabilitado
>
> Esses requisitos são apenas para a conta de armazenamento _padrão_ usada pelo espaço de trabalho.

| Serviço | Parâmetro usado para especificar uma instância existente |
| ---- | ---- |
| **Grupo de recursos do Azure** | `-g <resource-group-name>`
| **Conta de Armazenamento do Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Cofre da Chave do Azure** | `--keyvault <service-id>` |
| **Registro de Contêiner do Azure** | `--container-registry <service-id>` |

O ACR (registro de contêiner do Azure) atualmente não dá suporte a caracteres Unicode em nomes de grupos de recursos. Para atenuar esse problema, use um grupo de recursos que não contenha esses caracteres.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O workspace do Azure Machine Learning precisa ser criado dentro de um grupo de recursos. É possível usar um grupo de recursos existente ou criar um novo. Para __criar um grupo de recursos__, use o comando a seguir. Substitua `<resource-group-name>` pelo nome a ser usado para esse grupo de recursos. Substitua `<location>` pela região do Azure a ser usada para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região na qual o Azure Machine Learning está disponível. Para obter informações, veja [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta desse comando é semelhante ao JSON a seguir:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre como trabalhar com grupos de recursos, confira [az group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Criar automaticamente os recursos necessários

Para criar um workspace no qual os __serviços são criados automaticamente__, use o seguinte comando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> O nome do workspace não diferencia maiúsculas de minúsculas.

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Rede virtual e ponto de extremidade privado

> [!IMPORTANT]
> O uso de um espaço de trabalho Azure Machine Learning com o link privado não está disponível nas regiões do Azure governamental ou na 21Vianet do Azure na China.

Se você quiser restringir o acesso ao seu espaço de trabalho a uma rede virtual, poderá usar os seguintes parâmetros:

* `--pe-name`: O nome do ponto de extremidade privado que é criado.
* `--pe-auto-approval`: Se as conexões de ponto de extremidade privado com o espaço de trabalho devem ser aprovadas automaticamente.
* `--pe-resource-group`: O grupo de recursos para o qual criar o ponto de extremidade privado. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente na qual criar o ponto de extremidade privado.
* `--pe-subnet-name`: O nome da sub-rede para a qual criar o ponto de extremidade privado. O valor padrão é `default`.

Para obter mais informações sobre como usar um ponto de extremidade privado e uma rede virtual com seu espaço de trabalho, consulte [visão geral de isolamento e privacidade de rede virtual](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Espaço de trabalho de chave gerenciada pelo cliente e alto impacto nos negócios

Por padrão, os metadados para o espaço de trabalho são armazenados em uma instância Azure Cosmos DB que a Microsoft mantém. Esses dados são criptografados usando chaves gerenciadas pela Microsoft.

> [!NOTE]
> Azure Cosmos DB __não__ é usado para armazenar informações como o desempenho do modelo, informações registradas por experimentos ou informações registradas de suas implantações de modelo. Para obter mais informações sobre como monitorar esses itens, consulte a seção [monitoramento e registro em log](concept-azure-machine-learning-architecture.md) do artigo arquitetura e conceitos.

Em vez de usar a chave gerenciada pela Microsoft, você pode usar o fornecer sua própria chave. Isso cria a instância de Azure Cosmos DB que armazena metadados em sua assinatura do Azure. Use o `--cmk-keyvault` parâmetro para especificar o Azure Key Vault que contém a chave e `--resource-cmk-uri` para especificar a URL da chave no cofre.

Antes de usar `--cmk-keyvault` os `--resource-cmk-uri` parâmetros e, você deve primeiro executar as seguintes ações:

1. Autorize o __aplicativo Machine Learning__ (no gerenciamento de identidade e acesso) com permissões de colaborador em sua assinatura.
1. Siga as etapas em [Configurar chaves gerenciadas pelo cliente](../cosmos-db/how-to-setup-cmk.md) para:
    * Registrar o provedor de Azure Cosmos DB
    * Criar e configurar um Azure Key Vault
    * Gerar uma chave

Você não precisa criar manualmente a instância de Azure Cosmos DB, uma será criada para você durante a criação do espaço de trabalho. Esta instância de Azure Cosmos DB será criada em um grupo de recursos separado usando um nome com base neste padrão: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Para limitar os dados que a Microsoft coleta em seu espaço de trabalho, use o `--hbi-workspace` parâmetro. 

> [!IMPORTANT]
> A seleção de alto impacto nos negócios só pode ser feita durante a criação de um espaço de trabalho. Você não pode alterar essa configuração após a criação do espaço de trabalho.

Para obter mais informações sobre chaves gerenciadas pelo cliente e espaço de trabalho de alto impacto nos negócios, consulte [segurança da empresa para Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Usar recursos existentes

Para criar um workspace que usa os recursos existentes, você precisará fornecer a ID dos recursos. Use os seguintes comandos para obter a ID dos serviços:

> [!IMPORTANT]
> Você não precisa especificar todos os recursos existentes. Você pode especificar um ou mais. Por exemplo, especifique uma conta de armazenamento existente e o workspace criará os outros recursos.

+ **Conta do Armazenamento do Azure**: `az storage account show --name <storage-account-name> --query "id"`

    A resposta desse comando é semelhante ao seguinte texto e é a ID da sua conta de armazenamento:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Se você quiser usar uma conta de armazenamento do Azure existente, ela não poderá ser uma conta Premium (Premium_LRS e Premium_GRS). Ele também não pode ter um namespace hierárquico (usado com Azure Data Lake Storage Gen2). Não há suporte para armazenamento Premium ou namespace hierárquico com a conta de armazenamento _padrão_ do espaço de trabalho. Você pode usar o armazenamento Premium ou o namespace hierárquico com contas _de armazenamento não padrão_ .

+ **Azure Application Insights**:

    1. Instale a extensão do Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Obtenha a ID do serviço do Application Insights:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A resposta desse comando é semelhante ao seguinte texto e é a ID do serviço do Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    A resposta desse comando é semelhante ao seguinte texto e é a ID do cofre de chaves:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registro de Contêiner do Azure**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A resposta desse comando é semelhante ao seguinte texto e é a ID do registro de contêiner:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > O registro de contêiner precisa ter a [conta do administrador](../container-registry/container-registry-authentication.md#admin-account) habilitada para ser usada com um workspace do Azure Machine Learning.

Depois que você tiver as IDs para os recursos que deseja usar com o workspace, use o comando `az workspace create -w <workspace-name> -g <resource-group-name>` base e adicione os parâmetros e as IDs para os recursos existentes. Por exemplo, o seguinte comando cria um workspace que usa um registro de contêiner existente:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Listar os workspaces

Para listar todos os workspaces da sua assinatura do Azure, use o seguinte comando:

```azurecli-interactive
az ml workspace list
```

A saída desse comando é semelhante ao JSON a seguir:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Para obter mais informações, confira a documentação de [az ml workspace list](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list).

## <a name="get-workspace-information"></a>Obter informações do workspace

Para obter informações sobre um workspace, use o seguinte comando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para obter mais informações, confira a documentação de [az ml workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show).

## <a name="update-a-workspace"></a>Atualizar um workspace

Para atualizar um workspace, use o seguinte comando:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A saída desse comando é semelhante ao JSON a seguir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Para obter mais informações, confira a documentação de [az ml workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update).

## <a name="share-a-workspace-with-another-user"></a>Compartilhar um workspace com outro usuário

Para compartilhar um workspace com outro usuário na sua assinatura, use o seguinte comando:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Para obter mais informações sobre o controle de acesso baseado em função do Azure (RBAC do Azure) com Azure Machine Learning, consulte [gerenciar usuários e funções](how-to-assign-roles.md).

Para obter mais informações, confira a documentação de [az ml workspace share](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share).

## <a name="sync-keys-for-dependent-resources"></a>Sincronizar chaves para recursos dependentes

Se você alterar as chaves de acesso de um dos recursos usados pelo espaço de trabalho, levará cerca de uma hora para que o espaço de trabalho seja sincronizado com a nova chave. Para forçar o espaço de trabalho a sincronizar as novas chaves imediatamente, use o seguinte comando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obter mais informações, confira [Regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

Para obter mais informações, confira a documentação de [az ml workspace sync-keys](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys).

## <a name="delete-a-workspace"></a>Excluir um workspace

Para excluir um workspace depois que ele não for mais necessário, use o seguinte comando:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A exclusão de um workspace não exclui o Application Insights, a conta de armazenamento, o cofre de chaves nem o registro de contêiner usado pelo workspace.

Exclua também o grupo de recursos, o que exclui o workspace e todos os outros recursos do Azure no grupo de recursos. Para excluir o grupo de recursos, use o seguinte comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para obter mais informações, confira a documentação de [az ml workspace delete](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Como mover o workspace

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

### <a name="deleting-the-azure-container-registry"></a>Como excluir o Registro de Contêiner do Azure

O workspace do Azure Machine Learning usa o ACR (Registro de Contêiner do Azure) para algumas operações. Ele criará automaticamente uma instância do ACR quando precisar de uma pela primeira vez.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a extensão da CLI do Azure para machine learning, confira a documentação de [az ml](/cli/azure/ext/azure-cli-ml/ml).