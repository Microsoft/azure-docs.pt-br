---
title: 'Tutorial: Criar um pipeline do Azure Data Factory para copiar dados (portal do Azure) | Microsoft Docs'
description: Neste tutorial, você pode usar o portal do Azure para criar um pipeline do Azure Data Factory com uma atividade de cópia a fim de copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82e8fe26cc58117bc6249f8a7e87612dabc5f438
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839502"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Usar o portal do Azure para criar um pipeline do Data Factory para copiar dados 
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

> [!WARNING]
> O editor de JSON no portal do Azure para criação e implantação de pipelines do ADF v1 será DESATIVADO em 31 de julho de 2019. Após o dia 31 de julho de 2019, você poderá continuar usando [cmdlets do PowerShell para o ADF v1](https://docs.microsoft.com/powershell/module/az.datafactory/?view=azps-2.4.0&viewFallbackFrom=azps-2.3.2), o [SDK do .NET para o ADF v1](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.datafactories.models?view=azure-dotnet) e as [APIs REST do ADF v1](https://docs.microsoft.com/rest/api/datafactory/) para criar e implantar seus pipelines do ADF v1.

Neste artigo, você aprenderá a usar o [portal do Azure](https://portal.azure.com) para criar um data factory com um pipeline que copia dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. Se você ainda está se familiarizando com o Azure Data Factory, leia o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este tutorial.   

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos
Conclua os pré-requisitos listados no artigo [Pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) antes de executar este tutorial.

## <a name="steps"></a>Etapas
Eis as etapas executadas como parte deste tutorial:

1. Crie um **data factory** do Azure. Nesta etapa, você cria um data factory denominado ADFTutorialDataFactory. 
2. Crie **serviços vinculados** no data factory. Nesta etapa, você criará dois serviços vinculados dos tipos: Armazenamento do Azure e Banco de Dados SQL do Azure. 
    
    O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Você criou um contêiner e carregou dados nessa conta de armazenamento como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou uma tabela SQL no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   
3. Crie **conjuntos de dados** de entrada e saída no data factory.  
    
    O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob especifica o contêiner e a pasta que contém os dados.  

    Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.
4. Crie um **pipeline** na fábrica de dados. Nesta etapa, você cria um pipeline com a atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure em uma tabela no Banco de Dados SQL do Azure. Você pode usar uma atividade de cópia em um pipeline para copiar dados de qualquer fonte com suporte para qualquer destino com suporte. Para obter uma lista de armazenamentos de dados com suporte, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitore o pipeline. Nesta etapa, você **monitora** fatias de conjuntos de dados de entrada e de saída usando o portal do Azure. 

## <a name="create-a-data-factory"></a>Criar uma data factory
> [!IMPORTANT]
> Complete os [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) se você ainda não fez isso.   

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar um script do Hive para transformar os dados de entrada em dados de saída do produto. Vamos começar com a criação do data factory nesta etapa.

1. Depois de fazer logon no [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. Na folha **Nova data factory** :
   
   1. Digite **ADFTutorialDataFactory** como **nome**. 
      
         ![Folha Nova data factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nome da data factory indisponível](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
   3. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
      
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
          Algumas das etapas deste tutorial pressupõem que você esteja usando o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../../azure-resource-manager/resource-group-overview.md).  
   4. Selecione o **local** do data factory. Apenas as regiões com suporte pelo serviço Data Factory são mostradas na lista suspensa.
   5. Selecione **Fixar no painel**.     
   6. Clique em **Criar**.
      
      > [!IMPORTANT]
      > Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
      > 
      > O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.                
      > 
      > 
3. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY** , conforme mostrado na imagem.
   
   ![Página inicial do data factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você não usa serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Você usa dois armazenamentos de dados do tipo Armazenamento do Azure (origem) e o banco de dados SQL do Azure (destino). 

Portanto, você criará dois serviços vinculados chamados AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Essa conta de armazenamento é aquela na qual você criou um contêiner e carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).  

### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção.  

1. Na folha **Data Factory**, clique no bloco **Criar e implantar**.
   
   ![Bloco Criar e implantar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Você vê o **Editor Data Factory** conforme mostrado na imagem abaixo: 

    ![Editor Data Factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. No editor, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento vinculado do Azure no painel à direita. 
   
    ![Botão Novo repositório de dados do editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Substitua `<accountname>` e `<accountkey>` pelos valores de nome e de chave da sua conta de armazenamento do Azure. 
   
    ![JSON do armazenamento de Blob do editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Clique em **Implantar** na barra de ferramentas. Você deve ver o **AzureStorageLinkedService** implantado na árvore de exibição agora. 
   
    ![Implantar armazenamento de Blob do editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Para saber mais sobre as propriedades JSON na definição do serviço vinculado, consulte o artigo [Conector do Armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-azure-sql-database"></a>Criar um serviço vinculado para o Banco de Dados SQL do Azure
Nesta etapa, você vincula o banco de dados SQL do Azure ao data factory. Especifique o nome do SQL Server do Azure, nome do banco de dados, nome de usuário e senha de usuário nesta seção. 

1. No **Editor do Data Factory**, clique no botão **Novo repositório de dados** na barra de ferramentas e selecione **Banco de dados SQL do Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço vinculado do SQL do Azure no painel à direita.
2. Substitua `<servername>`, `<databasename>`, `<username>@<servername>` e `<password>` pelos nomes de seu servidor, banco de dados, conta de usuário e senha SQL do Azure. 
3. Clique em **Implantar** na barra de ferramentas para implantar o **AzureSqlLinkedService**.
4. Confirme que você vê **AzureSqlLinkedService** na exibição de árvore em **Serviços vinculados**.  

    Para saber mais sobre essas propriedades JSON, confira o [Conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou serviços vinculados para vincular a conta do Armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você define dois conjuntos de dados, chamados InputDataset e OutputDataset, que representam os dados de entrada e saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService e AzureSqlLinkedService, respectivamente.

O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob de entrada (InputDataset) especifica o contêiner e a pasta que contém os dados.  

Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída (OutputDataset) especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados. 

### <a name="create-an-input-dataset"></a>Criar um conjunto de dados de entrada
Nesta etapa, você criará um conjunto de dados denominado InputDataset que aponta para um arquivo de blob (emp.txt) na pasta raiz de um contêiner de blob (adftutorial) no Armazenamento do Azure representado pelo serviço vinculado AzureStorageLinkedService. Se você não especificar um valor para fileName (ou ignorá-lo), os dados de todos os blobs na pasta de entrada serão copiados no destino. Neste tutorial, você deve especificar um valor para o fileName. 

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **Armazenamento de Blobs do Azure** no menu suspenso. 
   
    ![Menu Novo conjunto de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Substitua JSON no painel direito pelo snippet JSON a seguir: 
   
    ```json
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    | Propriedade | DESCRIÇÃO |
    |:--- |:--- |
    | type | A propriedade type é definida como **AzureBlob** porque os dados residem no armazenamento de blobs do Azure. |
    | linkedServiceName | Refere-se ao **AzureStorageLinkedService** que você criou anteriormente. |
    | folderPath | Especifica o **contêiner** e a **pasta** de blob que contém blobs de entrada. Neste tutorial, adftutorial é o contêiner de blob e pasta é a pasta raiz. | 
    | fileName | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Neste tutorial, **emp.txt** é especificado como fileName e, portanto, apenas esse arquivo é selecionado para processamento. |
    | format -> type |O arquivo de entrada está no formato de texto e, portanto, usamos **TextFormat**. |
    | columnDelimiter | As colunas no arquivo de entrada são delimitadas por **caractere de vírgula (`,`)** . |
    | frequency/interval | A frequência é definida como **Hora** e o intervalo é definido como **1**, o que significa que as fatias de entrada ficam disponíveis **a cada hora**. Melhor dizendo, o serviço Data Factory procurará dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado. Ele procura os dados nas horas de início e término do pipeline, não antes ou depois delas.  |
    | external | Essa propriedade é definida como **true** se os dados não são gerados pelo pipeline. Os dados de entrada neste tutorial estão no arquivo emp.txt, que não é gerado pelo pipeline e, portanto, definimos essa propriedade como true. |

    Para saber mais sobre essas propriedades JSON, confira o [artigo sobre o conector do Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).      
3. Clique em **Implantar** na barra de ferramentas para criar e implantar o conjunto de dados **InputDataset**. Confirme que você vê o **InputDataset** na exibição de árvore.

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
O serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. O conjunto de dados da tabela SQL de saída (OutputDataset) criado nesta etapa especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **SQL do Azure** no menu suspenso. 
2. Substitua JSON no painel direito pelo snippet JSON a seguir:

    ```json   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```     

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    | Propriedade | DESCRIÇÃO |
    |:--- |:--- |
    | type | A propriedade type é definida como **AzureSqlTable** porque os dados são copiados para uma tabela em um banco de dados SQL do Azure. |
    | linkedServiceName | Refere-se ao **AzureSqlLinkedService** que você criou anteriormente. |
    | tableName | Especifica a **tabela** para a qual os dados são copiados. | 
    | frequency/interval | A frequência é definida como **Hora** e o intervalo é de **1**, o que significa que as fatias de saída são produzidas **a cada hora** entre as horas de início e término do pipeline, não antes ou depois delas.  |

    Há três colunas (**ID**, **FirstName** e **LastName**) na tabela emp no banco de dados. ID é uma coluna de identidade. Portanto, você precisa especificar somente **FirstName** e **LastName** aqui.

    Para saber mais sobre essas propriedades JSON, confira o [artigo sobre o conector do SQL](data-factory-azure-sql-connector.md#dataset-properties).
3. Clique em **Implantar** na barra de ferramentas para criar e implantar o conjunto de dados **OutputDataset**. Confirme que você vê o **OutputDataset** na exibição de árvore em **Conjuntos de Dados**. 

## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com uma **atividade de cópia** que usa **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Neste tutorial, o conjunto de dados de saída está configurado para produzir uma fatia uma vez a cada hora. O pipeline tem uma hora de início e uma hora de término com um dia de diferença, o que significa 24 horas. Portanto, 24 fatias de conjunto de dados de saída são produzidas pelo pipeline. 

1. No **Editor** do Data Factory, clique em **... Mais** e clique em **Novo pipeline**. Como alternativa, você pode clicar com o botão direito do mouse em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.
2. Substitua JSON no painel direito pelo snippet JSON a seguir: 

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Observe os seguintes pontos:
   
   - Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Nas soluções de Data Factory, você também pode usar [atividades de transformação de dados](data-factory-data-transformation-activities.md).
   - A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. 
   - Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para ver a lista completa dos armazenamentos de dados com suporte pela atividade de cópia, como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Para aprender a usar um armazenamento de dados com suporte específico como fonte/coletor, clique no link na tabela.
   - Ambos os valores de data/hora de início e de término devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo:  2016-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
     
     No exemplo anterior, há 24 fatias de dados, pois cada fatia de dados é produzida a cada hora.

     Para obter descrições das propriedades JSON em uma definição de pipeline, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). Para obter descrições das propriedades JSON em uma definição de atividade de cópia, consulte [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Para obter descrições das propriedades JSON com suporte pelo BlobSource, consulte o [artigo sobre o conector de blobs do Azure](data-factory-azure-blob-connector.md). Para obter descrições das propriedades JSON com suporte pelo SqlSink, consulte o [artigo sobre o conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md).
3. Clique em **Implantar** na barra de ferramentas para criar e implantar o **ADFTutorialPipeline**. Confirme que você vê o pipeline no modo de exibição de árvore. 
4. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para ver a home page **Data Factory** do **ADFTutorialDataFactory**.

**Parabéns!** Você criou com êxito um data factory do Azure, com um pipeline que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure. 


## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
Nesta etapa, você utiliza o portal do Azure para monitorar o que está acontecendo em um data factory do Azure.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorar o pipeline usando o aplicativo Monitorar e Gerenciar
As etapas a seguir mostram como monitorar pipelines no data factory usando o aplicativo Monitorar e gerenciar: 

1. Clique no bloco **Monitorar e Gerenciar** na home page do seu data factory.
   
    ![Bloco Monitorar e Gerenciar](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Você deverá ver **Monitorar e gerenciar aplicativo** em uma guia separada. 

    > [!NOTE]
    > Se você vir que o navegador da Web está travado em "Autorizando...", faça o seguinte: desmarque a caixa de seleção **Bloquear cookies de terceiros e dados de site** (ou) crie uma exceção para **login.microsoftonline.com** e tente iniciar o aplicativo novamente.

    ![Aplicativo Monitorar e Gerenciar](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Altere a **Hora de início** e a **Hora de término** para corresponder às horas de início (2017-05-11) e de término (2017-05-12) do seu pipeline e clique em **Aplicar**.       
3. Você verá a **janela de atividade** associada a cada hora entre as horas de início e término do pipeline na lista no painel central. 
4. Para ver detalhes sobre uma janela de atividade, selecione-a na lista **Janelas de Atividade**. 
    ![Detalhes da janela Atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    No Gerenciador da Janela de Atividade à direita, você verá que as fatias até a hora UTC atual (20h12) estão todas processadas (em verde). As fatias de 20h às 21h, 21h às 22h, 22h às 23h e 23h às 00h ainda não foram processadas.

    A seção **Tentativas** no painel direito fornece informações sobre a execução da atividade da fatia de dados. Se houver um erro, ele fornecerá detalhes sobre o erro. Por exemplo, se a pasta de entrada ou o contêiner não existir e o processamento da fatia falhar, você verá uma mensagem de erro informando que o contêiner ou pasta não existe.

    ![Tentativas de execução da atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Inicie o **SQL Server Management Studio**, conecte-se ao Banco de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** do banco de dados.
    
    ![resultados da consulta sql](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Para obter informações detalhadas sobre como usar esse aplicativo, confira [Monitorar e gerenciar pipelines do Azure Data Factory usando o aplicativo Monitorar e Gerenciar](data-factory-monitor-manage-app.md).

### <a name="monitor-the-pipeline-using-diagram-view"></a>Monitorar o pipeline usando a Exibição de Diagrama
Você também pode monitorar pipelines de dados usando o modo de exibição de diagrama.  

1. Na folha **Data Factory**, clique em **Diagrama**.
   
    ![Folha Data Factory — bloco Diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Você deverá ver o diagrama semelhante à imagem abaixo: 
   
    ![Modo de Exibição de Diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. Na exibição de diagrama, clique duas vezes em **InputDataset** para ver as fatias do conjunto de dados.  
   
    ![Conjuntos de dados com InputDataset selecionado](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Clique no link **Ver mais** para ver todas as fatias de dados. Você verá 24 fatias horárias entre as horas de início e término do pipeline. 
   
    ![Todas as fatias de dados de entrada](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Observe que, até o momento, todas as fatias de dados até o horário UTC atual estão no estado**Pronto**, pois o arquivo **emp.txt** sempre existe no contêiner de blob: **adftutorial\input**. As fatias para horários futuros ainda não estarão com status pronto. Confirme que nenhuma fatia apareça na seção **Fatias com falha recente** , na parte inferior.
6. Feche as folhas até ver a exibição de diagrama (ou) role para a esquerda a fim de ver a exibição de diagrama. Em seguida, clique duas vezes em **OutputDataset**. 
8. Clique no link **Ver mais**na folha **Tabela** de **OutputDataset** para ver todas as fatias.

    ![folha fatias de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Observe que todas as fatias até a hora UTC atual se movem entre os status **Aguardando execução** = > **Em andamento** ==> **Pronto**. As fatias do passado (antes da hora atual) são processadas da mais recente à mais antiga por padrão. Por exemplo, se a hora atual é 20h12 UTC, a fatia para 19h-20h é processada à frente da fatia 18h-19h. Por padrão, a fatia de 20h-21h é processada no final do intervalo de tempo, ou seja, depois das 21h.  
10. Clique em qualquer fatia de dados na lista e você deverá ver a folha **FATIA DE DADOS** . Uma parte dos dados associados a uma janela de atividade é chamada de fatia. Uma fatia pode ser um ou vários arquivos.  
    
     ![folha fatia de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.
11. Na folha **FATIA DE DADOS** , você deve ver que toda a atividade é executada na lista, na parte inferior. Clique em uma **execução de atividade** para ver a folha **Detalhes de execução da atividade**. 
    
    ![Detalhes da execução da atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    Nessa folha, é possível ver quanto tempo a operação de cópia levou, a taxa de transferência, quantos bytes de dados foram lidos e gravados, as horas de início e término da execução, etc.  
12. Clique em **X para fechar todas as folhas até** voltar à folha inicial de **ADFTutorialDataFactory**.
13. (opcional) clique no bloco **Conjuntos de Dados** ou no bloco **Pipelines** para acessar as folhas que você viu anteriormente. 
14. Inicie o **SQL Server Management Studio**, conecte-se ao Banco de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** do banco de dados.
    
    ![resultados da consulta sql](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Portal do Azure para criar o data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
2. Foram criados **serviços vinculados**:
   1. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta do Armazenamento do Azure que contém os dados de entrada.     
   2. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL do Azure que contém os dados de saída. 
3. Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4. Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor.  

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela.
