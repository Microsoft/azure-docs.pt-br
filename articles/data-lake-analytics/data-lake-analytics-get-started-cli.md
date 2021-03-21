---
title: Criar & Azure Data Lake Analytics de consulta-CLI do Azure
description: Aprenda a usar a Interface de linha de comando do Azure para criar uma conta do Azure Data Lake Analytics e enviar uma tarefa do U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/18/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9123ca6a1bfa90737bb1ce83ee365d1ecf514e1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220985"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Introdução ao Azure Data Lake Analytics usando a CLI do Azure

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Este artigo descreve como usar a interface de linha de comando da CLI do Azure para criar contas do Analytics do Azure Data Lake, enviar trabalhos do USQL e catálogos. O trabalho lê um arquivo TSV (Valores Separados por Tabulação) e os converte em um arquivo CSV (valores separados por vírgula).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa dos seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar em sua assinatura do Azure:

```azurecli
az login
```

Você precisará navegar até uma URL e inserir um código de autenticação.  E siga as instruções para inserir suas credenciais.

Uma vez feito o logon, o comando de logon listará suas assinaturas.

Para usar uma assinatura específica:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake

Você precisa ter uma conta do Data Lake Analytics antes de executar trabalhos. Para criar uma conta do Data Lake Analytics, você deve especificar os seguintes itens:

* **Grupo de recursos do Azure**. É necessário criar uma conta do Data Lake Analytics em um grupo de Recursos do Azure. [Azure Resource Manager](../azure-resource-manager/management/overview.md) permite que você trabalhe com os recursos em seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.  

Para listar os grupos de recursos existentes em sua assinatura:

```azurecli
az group list
```

Para criar um novo grupo de recursos:

```azurecli
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Nome da conta de data Lake Analytics**. Cada conta do Data Lake Analytics tem um nome.
* **Localização**. Use um dos datacenters do Azure que dá suporte ao Data Lake Analytics.
* **Conta padrão do Data Lake Store**: cada conta do Data Lake Analytics tem uma conta padrão do Data Lake Store.

Para listar a conta existente do Data Lake Store:

```azurecli
az dls account list
```

Para criar uma nova conta do Azure Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Use o seguinte código para criar uma conta do Data Lake Analytics:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Depois de criar uma conta, você pode usar os seguintes comandos para listar as contas e mostrar seus detalhes:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"
```

## <a name="upload-data-to-data-lake-store"></a>Carregar dados no Repositório Data Lake

Neste tutorial, você processa alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no Repositório Data Lake ou no Armazenamento de Blob do Azure.

O portal do Azure fornece uma interface do usuário para copiar alguns arquivos de dados de exemplo para a conta padrão do Data Lake Store, o que inclui um arquivo de log de pesquisa. Consulte [Preparar dados de origem](data-lake-analytics-get-started-portal.md) para carregar os dados na conta do Repositório Data Lake.

Para carregar arquivos usando a CLI do Azure, use os seguintes comandos:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Análise Data Lake também pode acessar o armazenamento de Blob do Azure.  Para carregar dados no Armazenamento de Blob do Azure, consulte [Usando a CLI do Azure com o Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar trabalhos da Análise Data Lake

Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, confira [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](/u-sql/).

### <a name="to-create-a-data-lake-analytics-job-script"></a>Para criar um script de trabalho da Análise Data Lake

Crie um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:

```usql
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**, em seguida, cria um arquivo csv usando **Outputters.Csv()**.

Não modifique os dois caminhos, a menos que você copie o arquivo de origem para um local diferente.  O Data Lake Analytics criará a pasta de saída se ela não existir.

É mais simples usar caminhos relativos para arquivos armazenados em contas padrão do Data Lake Store. Você também pode usar caminhos absolutos.  Por exemplo:

```usql
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Você deve usar caminhos absolutos para acessar os arquivos em contas do Armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:

```usql
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Não há suporte para o contêiner de Blob do Azure com blobs públicos.
> Não há suporte para o contêiner de Blob do Azure com contêineres públicos.
>

### <a name="to-submit-jobs"></a>Para enviar trabalhos

Use a sintaxe a seguir para enviar um trabalho.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Por exemplo:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

### <a name="to-list-jobs-and-show-job-details"></a>Para listar trabalhos e mostrar detalhes do trabalho

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

### <a name="to-cancel-jobs"></a>Para cancelar trabalhos

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Recuperar os resultados do trabalho

Depois que um trabalho é concluído, use os seguintes comandos para listar os arquivos de saída e baixá-los:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Por exemplo:

```azurecli
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Próximas etapas

* Para ver o documento de referência da CLI do Data Lake Analytics do Azure, consulte [Data Lake Analytics](/cli/azure/dla).
* Para ver o documento de referência da CLI do Azure do Armazenamento do Data Lake, consulte [Armazenamento do Data Lake](/cli/azure/dls).
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).