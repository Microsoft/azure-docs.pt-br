---
title: Gerenciar o Azure Data Lake Analytics usando a Interface de Linha de Comando do Azure | Microsoft Docs
description: "Saiba como gerenciar contas, fontes de dados, trabalhos e os usuários da Análise Data Lake  usando a CLI do Azure"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: f90bada3572c0ed40b07d76ec02c1b499bbd1428
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Gerenciar a Análise Azure Data Lake usando a CLI (interface de linha de comando) do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando a CLI do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.


**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure**. Consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
  * Baixe e instale o **pré-lançamento das** [ferramentas de CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.
* **Autenticação**, usando o seguinte comando:
  
        azure login
    Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).
* **Alterne para o modo Gerenciador de Recursos do Azure**usando o seguinte comando:
  
        azure config mode arm

**Para listar os comandos do Repositório Data Lake e da Análise Data Lake:**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Gerenciar contas
Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você não paga por uma conta da Análise quando ela não estiver executando um trabalho.  Você paga apenas pelo tempo em que um trabalho é executado.  Para saber mais, consulte [Visão geral sobre a Análise Azure Data Lake](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>Atualizar contas
O comando a seguir atualiza as propriedades de uma conta existente da Análise Data Lake 

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>Listar contas
Listar contas da Análise Data Lake 

    azure datalake analytics account list

Listar contas da Análise Data Lake em um grupo de recursos específico

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Obter detalhes de uma conta específica da Análise Data Lake

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Excluir contas da Análise Data Lake
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar as fontes de dados da conta
No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta de armazenamento do ADL padrão é usada para armazenar os logs de auditoria de trabalho e os metadados de trabalho. Depois de criar uma conta da Análise, é possíveis adicionar outras contas do Armazenamento do Data Lake e/ou uma conta do Armazenamento do Azure. 

### <a name="find-the-default-adl-storage-account"></a>Localize a conta de armazenamento padrão do ADL
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

O valor é listado em properties:datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Adicionar outras contas de armazenamento de Blob do Azure
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> Há suporte apenas para nomes curtos do Armazenamento de Blobs.  Não use o FQDN, por exemplo "myblob.blob.core.windows.net".
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar outras contas do Repositório Data Lake
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] é uma opção para indicar se o Data Lake que está sendo adicionado é a conta padrão do Data Lake. 

### <a name="update-existing-data-source"></a>Atualizar a fonte de dados existente
Para definir uma conta existente do Repositório do Data Lake padrão:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Para atualizar uma chave de conta do Armazenamento de Blobs existente:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Listar fontes de dados:
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Fonte de dados de lista da Análise Data Lake  ](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Excluir fontes de dados:
Para excluir uma conta do Repositório do Data Lake:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Para excluir uma conta do Armazenamento de Blobs:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Gerenciar trabalhos
Você deve ter uma conta da Análise Data Lake antes de criar um trabalho.  Para saber mais, consulte [Gerenciar contas da Análise Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Listar trabalhos
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Fonte de dados de lista da Análise Data Lake  ](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Exibir detalhes do trabalho
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>Enviar trabalhos
> [!NOTE]
> A prioridade padrão de um trabalho é de 1.000 e o nível padrão de paralelismo de um trabalho é 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Cancelar trabalhos
Use o comando “list” para localizar o ID do trabalho e use “cancel” para cancelar o trabalho.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Gerenciar o catálogo
O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. Para saber mais, consulte [Usar o Catálogo do U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Listar itens do catálogo
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

Os tipos incluem database, schema, assembly, external data source, table, table valued function ou table statistics.

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Usar grupos ARM
Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de Análise Data Lake pode incluir os seguintes componentes:

* Conta da Análise Azure Data Lake
* Conta padrão do Armazenamento do Azure Data Lake obrigatória
* Adicionar outras contas do Armazenamento do Azure Data Lake
* Contas do Armazenamento do Azure adicionais

Você pode criar todos esses componentes em um grupo ARM para torná-los mais fáceis de serem gerenciados.

![Conta e armazenamento da Análise Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta da Análise Data Lake e as contas de armazenamento dependentes devem ser colocadas no mesmo data center do Azure.
No entanto, o grupo ARM pode estar localizado em um data center diferente.  

## <a name="see-also"></a>Confira também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

