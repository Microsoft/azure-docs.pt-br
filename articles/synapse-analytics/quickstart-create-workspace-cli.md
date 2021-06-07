---
title: 'Início Rápido: Criar um workspace do Synapse usando a CLI do Azure'
description: Crie um workspace do Azure Synapse usando a CLI do Azure seguindo as etapas neste guia.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 8a56b325dd5e1180b1229465965167241fab76a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676452"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Início Rápido: Criar um workspace do Azure Synapse com a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode usá-la em seu navegador com o Azure Cloud Shell. Você também pode instalá-lo no Windows, no Linux ou no macOS e executá-lo na linha de comando.

Neste guia de início rápido, você aprenderá a criar um workspace do Synapse usando a CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixar e instalar o [jq](https://stedolan.github.io/jq/download/), um processador JSON de linha de comando leve e flexível
- [Conta de armazenamento do Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > O workspace do Azure Synapse precisa conseguir fazer leituras e gravações na conta do ADLS Gen2 selecionada. Além disso, para qualquer conta de armazenamento vinculada como a conta de armazenamento primária, você precisa ter habilitado **namespace hierárquico** na criação da conta de armazenamento, conforme descrito na página [Criar uma Conta de Armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Criar um workspace do Azure Synapse usando a CLI do Azure

1. Defina as variáveis de ambiente necessárias para criar recursos para o workspace do Azure Synapse.

    | Nome de variável de ambiente | Descrição |
    |---|---|---|
    |StorageAccountName| Nome de sua conta de armazenamento existente do ADLS Gen2.|
    |StorageAccountResourceGroup| Nome do seu grupo de recursos da conta de armazenamento do ADLS Gen2. |
    |FileShareName| Nome do sistema de arquivos de armazenamento existente.|
    |SynapseResourceGroup| Escolha um novo nome para o grupo de recursos do Azure Synapse. |
    |Região| Escolha uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Escolha um nome exclusivo para seu novo workspace do Azure Synapse. |
    |SqlUser| Escolha um valor para um novo nome de usuário.|
    |SqlPassword| Escolha uma senha segura.|
    |||

1. Crie um grupo de recursos como um contêiner para seu workspace do Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```

1. Crie um workspace do Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

1. Obtenha a URL da Web e de desenvolvimento para o workspace do Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

1. Crie uma regra de firewall para permitir o acesso ao workspace do Azure Synapse em seu computador:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

1. Abra o endereço URL da Web do workspace do Azure Synapse armazenado na variável de ambiente `WorkspaceWeb` para acessar seu workspace:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Web do workspace do Azure Synapse](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Limpar os recursos

Siga as etapas abaixo para excluir o workspace do Azure Synapse.
> [!WARNING]
> A exclusão de um workspace do Azure Synapse removerá os mecanismos de análise e os dados armazenados no banco de dados dos metadados do workspace e dos pools de SQL independentes. Não será mais possível se conectar aos pontos de extremidade do SQL ou do Apache Spark. Todos os artefatos de código serão excluídos (consultas, notebooks, definições de trabalho e pipelines).
>
> A exclusão do workspace **não** afetará os dados do Data Lake Storage Gen2 vinculados ao workspace.

Caso deseje excluir o workspace do Azure Synapse, conclua o seguinte comando:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá [criar pools de SQL](quickstart-create-sql-pool-studio.md) ou [criar pools do Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar seus dados.