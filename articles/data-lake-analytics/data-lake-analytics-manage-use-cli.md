---
title: Gerenciar Azure Data Lake Analytics usando CLI do Azure
description: Este artigo descreve como usar a CLI do Azure para gerenciar trabalhos, fontes de dados e usuários do Data Lake Analytics.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220917"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Gerenciar o Azure Data Lake Analytics usando a CLI (interface de linha de comando) do Azure

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando a CLI do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter os seguintes recursos:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- CLI do Azure. Consulte [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli).

  - Baixe e instale o **pré-lançamento das** [ferramentas de CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.

- Autentique usando o comando `az login` e selecione a assinatura que você deseja usar. Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Agora você pode acessar os comandos do Data Lake Analytics e Data Lake Store. Execute o seguinte comando para listar os comandos do Data Lake Store e Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Gerenciar Contas

Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você não paga por uma conta da Análise quando ela não estiver executando um trabalho. Você paga apenas pelo tempo em que um trabalho é executado.  Para saber mais, consulte [Visão geral sobre a Análise Azure Data Lake](data-lake-analytics-overview.md).

### <a name="create-accounts"></a>Criar contas

Execute o comando a seguir para criar uma conta Data Lake,

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Atualizar contas

O comando a seguir atualiza as propriedades de uma conta existente da Análise Data Lake 

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Listar contas

Listar contas da Análise Data Lake em um grupo de recursos específico

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Obtenha detalhes de uma conta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Excluir uma conta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gerenciar fontes de dados

No momento, o Data Lake Analytics dá suporte às duas fontes de dados a seguir:

- [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta de armazenamento padrão do Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta da Análise, é possíveis adicionar outras contas do Armazenamento do Data Lake e/ou uma conta do Armazenamento do Azure.

### <a name="find-the-default-data-lake-store-account"></a>Encontrar a conta padrão do Repositório Data Lake

Você pode exibir a conta do Data Lake Store padrão usada ao executar o comando `az dla account show`. Nome de conta padrão é listado sob a propriedade defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Adicionar outras contas do Armazenamento de Blobs

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Há suporte apenas para nomes curtos do Armazenamento de Blobs. Não use o FQDN, por exemplo "myblob.blob.core.windows.net".
>

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar outras contas do Repositório Data Lake

O comando a seguir atualiza a conta do Data Lake Analytics especificada com uma conta do Data Lake Store adicional:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Atualizar a fonte de dados existente

Para atualizar uma chave de conta do Armazenamento de Blobs existente:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Listar fontes de dados

Para listar as contas do Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Para listar as contas do Armazenamento de Blobs:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Captura de tela que mostra o I L do Azure C com as informações "dataLakeStoreAccounts:" realçadas.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Excluir fontes de dados

Para excluir uma conta do Repositório do Data Lake:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Para excluir uma conta do Armazenamento de Blobs:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Gerenciar trabalhos

Você deve ter uma conta da Análise Data Lake antes de criar um trabalho.  Para saber mais, consulte [Gerenciar contas da Análise Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Listar trabalhos

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Fonte de dados de lista da Análise Data Lake  ](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Exibir detalhes do trabalho

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Enviar trabalhos

> [!NOTE]
> A prioridade padrão de um trabalho é de 1.000 e o nível padrão de paralelismo de um trabalho é 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Cancelar trabalhos

Use o comando list para localizar a ID do trabalho e, em seguida, use Cancel para cancelar o trabalho.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines e recorrências

### <a name="get-information-about-pipelines-and-recurrences"></a>Obter informações sobre pipelines e recorrências

Utilize os comandos `az dla job pipeline` para consultar as informações de pipeline para trabalhos enviados anteriormente.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Utilize os comandos `az dla job recurrence` para consultar as informações de recorrência para trabalhos enviados anteriormente.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Análise Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introdução ao Data Lake Analytics usando portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerenciar Azure Data Lake Analytics usando portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorar e solucionar problemas de trabalhos do Azure Data Lake Analytics usando portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
