---
title: 'Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio | Microsoft Docs'
description: Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Visual Studio.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 934effe585b85075a80eede4236258d4a428b9ce
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836561"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Criar um pipeline com a Atividade de Cópia usando o Visual Studio
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste artigo, você aprenderá a usar o Microsoft Visual Studio para criar um data factory com um pipeline que copia dados de um armazenamento de blobs do Azure para um Banco de Dados SQL do Azure. Se você ainda está se familiarizando com o Azure Data Factory, leia o artigo [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este tutorial.   

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Leia o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e concluir as etapas de **pré-requisito** .       
2. Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
3. Você deve ter os seguintes itens instalados no seu computador: 
   * Visual Studio 2013 ou Visual Studio 2015
   * Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
   * Baixe o último plug-in do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Atualize também o plug-in executando as seguintes etapas: No menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**.

## <a name="steps"></a>Etapas
Eis as etapas executadas como parte deste tutorial:

1. Crie **serviços vinculados** no data factory. Nesta etapa, você criará dois serviços vinculados dos tipos: Armazenamento do Azure e Banco de Dados SQL do Azure. 
    
    O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Você criou um contêiner e carregou dados nessa conta de armazenamento como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou uma tabela SQL no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).     
2. Crie **conjuntos de dados** de entrada e saída no data factory.  
    
    O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob especifica o contêiner e a pasta que contém os dados.  

    Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados.
3. Crie um **pipeline** na fábrica de dados. Nesta etapa, você cria um pipeline com a atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure em uma tabela no Banco de Dados SQL do Azure. Você pode usar uma atividade de cópia em um pipeline para copiar dados de qualquer fonte com suporte para qualquer destino com suporte. Para obter uma lista de armazenamentos de dados com suporte, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
4. Crie um **data factory** do Azure ao implantar entidades de Data Factory (serviços vinculados, conjuntos de dados/tabelas e pipelines). 

## <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio
1. Inicie o **Visual Studio 2015**. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto** .  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio de Data Factory**.  
   
    ![Caixa de diálogo Novo projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Especifique o nome do projeto, o local e o nome da solução e, em seguida, clique em **OK**.
   
    ![Gerenciador de soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste tutorial, você não usa serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Você usa dois armazenamentos de dados do tipo Armazenamento do Azure (origem) e o banco de dados SQL do Azure (destino). 

Portanto, você criará dois serviços vinculados dos tipos: AzureStorage e AzureSqlDatabase.  

O serviço vinculado ao Armazenamento do Azure vincula sua conta de armazenamento do Azure ao data factory. Essa conta de armazenamento é aquela na qual você criou um contêiner e carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Um serviço vinculado do SQL do Azure vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Confira [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver todas as fontes e coletores com suporte da Atividade de Cópia. Confira os [serviços vinculados de computação](data-factory-compute-linked-services.md) para ver uma lista dos serviços de computação com suporte do Data Factory. Neste tutorial, você não usa nenhum serviço de computação. 

### <a name="create-the-azure-storage-linked-service"></a>Criar o serviço de armazenamento do Azure vinculado
1. No **Gerenciador de Soluções**, clique com botão direito em **Serviços Vinculados**, aponte para **Adicionar** e clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**. 
   
    ![Novo serviço vinculado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Substitua `<accountname>` e `<accountkey>`* pelo nome da conta de armazenamento do Azure e sua chave. 
   
    ![Serviço vinculado de armazenamento do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Salve o arquivo **AzureStorageLinkedService1.json** .

    Para saber mais sobre as propriedades JSON na definição do serviço vinculado, consulte o artigo [Conector do Armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Criar um serviço vinculado do SQL do Azure
1. Clique com o botão direito no nó **Serviços Vinculados** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**. 
2. Desta vez, selecione **Serviço Vinculado SQL do Azure** e clique em **Adicionar**. 
3. No arquivo **AzureSqlLinkedService1.json**, substitua `<servername>`, `<databasename>`, `<username@servername>` e `<password>` pelos nomes do SQL server do Azure, do banco de dados, da conta de usuário e pela senha.    
4. Salve o arquivo **AzureSqlLinkedService1.json** . 
    
    Para saber mais sobre essas propriedades JSON, confira o [Conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties).


## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou serviços vinculados para vincular a conta do Armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você define dois conjuntos de dados, InputDataset e OutputDataset, que representam os dados de entrada/saída armazenados nos repositórios de dados referidos por AzureStorageLinkedService1 e AzureSqlLinkedService1, respectivamente.

O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. E o conjunto de dados de blob de entrada (InputDataset) especifica o contêiner e a pasta que contém os dados.  

Da mesma forma, o serviço vinculado do Banco de Dados SQL especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar ao seu Banco de Dados SQL do Azure. Além disso, o conjunto de dados da tabela SQL de saída (OutputDataset) especifica a tabela no banco de dados na qual os dados do armazenamento de blobs são copiados. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Nesta etapa, você criará um conjunto de dados denominado InputDataset que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado AzureStorageLinkedService1. Se você não especificar um valor para fileName (ou ignorá-lo), os dados de todos os blobs na pasta de entrada serão copiados no destino. Neste tutorial, você deve especificar um valor para o fileName. 

Aqui, você usa o termo "tabelas" em vez de "conjuntos de dados". Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados com suporte no momento. 

1. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
3. Substitua o texto JSON pelo texto a seguir e salve o arquivo **AzureBlobLocation1.json** . 

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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
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

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Nesta etapa, você cria um conjunto de dados de saída denominado **OutputDataset**. Esse conjunto de dados aponta para uma tabela SQL no banco de dados SQL do Azure representada por **AzureSqlLinkedService1**. 

1. Clique com o botão direito do mouse em **Tabelas** no **Gerenciador de Soluções** novamente, aponte para **Adicionar** e clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
3. Substitua o texto JSON pelo JSON a seguir e salve o arquivo **AzureSqlTableLocation1.json** .

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
       "linkedServiceName": "AzureSqlLinkedService1",
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

## <a name="create-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com uma **atividade de cópia** que usa **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Neste tutorial, o conjunto de dados de saída está configurado para produzir uma fatia uma vez a cada hora. O pipeline tem uma hora de início e uma hora de término com um dia de diferença, o que significa 24 horas. Portanto, 24 fatias de conjunto de dados de saída são produzidas pelo pipeline. 

1. Clique com o botão direito do mouse em **Pipelines** no **Gerenciador de Soluções**, aponte para **Adicionar** e clique em **Novo Item**.  
2. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
3. Substitua o JSON pelo JSON a seguir e salve o arquivo **CopyActivity1.json** .

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
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   - Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Nas soluções de Data Factory, você também pode usar [atividades de transformação de dados](data-factory-data-transformation-activities.md).
   - A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. 
   - Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Para ver a lista completa dos armazenamentos de dados com suporte pela atividade de cópia, como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Para aprender a usar um armazenamento de dados com suporte específico como fonte/coletor, clique no link na tabela.  
     
     Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
     
     Ambos os valores de data/hora de início e de término devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo:  2016-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial. 
     
     Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.
     
     No exemplo anterior, há 24 fatias de dados, pois cada fatia de dados é produzida a cada hora.

     Para obter descrições das propriedades JSON em uma definição de pipeline, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). Para obter descrições das propriedades JSON em uma definição de atividade de cópia, consulte [Atividades de movimentação de dados](data-factory-data-movement-activities.md). Para obter descrições das propriedades JSON com suporte pelo BlobSource, consulte o [artigo sobre o conector de blobs do Azure](data-factory-azure-blob-connector.md). Para obter descrições das propriedades JSON com suporte pelo SqlSink, consulte o [artigo sobre o conector do Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Publicar/implantar entidades de data factory
Nesta etapa, você publica as entidades de Data Factory (serviços vinculados, conjuntos de dados e pipeline) criadas anteriormente. Você também pode especificar o nome do novo data factory a ser criado para manter essas entidades.  

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**. 
2. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
3. Você deve ver a caixa de diálogo a seguir:
   
   ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na página Configurar data factory, execute as seguintes etapas: 
   
   1. Selecione a opção **Criar Nova Data Factory** .
   2. Digite **VSTutorialFactory** para **Nome**.  
      
      > [!IMPORTANT]
      > O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber um erro sobre o nome do data factory durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.        
      > 
      > 
   3. Selecione a assinatura do Azure no campo **Assinatura** .
      
      > [!IMPORTANT]
      > Se você não vir nenhuma assinatura, verifique se está conectado usando uma conta que seja administrador ou coadministrador da assinatura.  
      > 
      > 
   4. Selecione o **grupo de recursos** para o data factory a ser criado. 
   5. Selecione a **região** do data factory. Apenas as regiões com suporte pelo serviço Data Factory são mostradas na lista suspensa.
   6. Clique em **Avançar** para alternar para a página **Publicar Itens**.
      
       ![Configurar página de data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.
   
   ![Publicar página de item](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
   
   ![Publicar página de resumo](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Na página **Status da Implantação** , você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado.
 
   ![Página Status da implantação](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Observe os seguintes pontos: 

* Se você receber o erro "Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory", siga um dos seguintes procedimentos e tente a publicação novamente: 
  
  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory. 

    ```powershell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado. 
    
    ```powershell
    Get-AzResourceProvider
    ```
  * Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.
* O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.

> [!IMPORTANT]
> Para criar instâncias do Data Factory, você precisa ser um administrador/coadministrador da assinatura do Azure

## <a name="monitor-pipeline"></a>Monitorar o pipeline
Navegue até a home page do seu data factory:

1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços** no menu à esquerda e clique em **Data factories**.

    ![Procurar data factories](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Comece a digitar o nome do data factory.

    ![Nome do data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Clique no data factory na lista de resultados para ver a home page dele.

    ![Página inicial do data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Siga as instruções de [Monitorar conjuntos de dados e pipeline](data-factory-monitor-manage-pipelines.md) para monitorar o pipeline e os conjuntos de dados criados neste tutorial. Atualmente, o Visual Studio não dá suporte a monitoramento de pipelines do Data Factory. 

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o Visual Studio para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
2. Foram criados **serviços vinculados**:
   1. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta do Armazenamento do Azure que contém os dados de entrada.     
   2. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL do Azure que contém os dados de saída. 
3. Foram criados **conjuntos de dados**que descrevem os dados de entrada e de saída para os pipelines.
4. Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor. 

Para ver como usar uma Atividade do Hive no HDInsight para transformar dados usando um cluster do Azure HDInsight, confira [Tutorial: criar seu primeiro pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 

## <a name="view-all-data-factories-in-server-explorer"></a>Exibir todos os data factories no Gerenciador de Servidores
Esta seção descreve como usar o Gerenciador de Servidores no Visual Studio para exibir todos os data factories na sua assinatura do Azure e criar um projeto do Visual Studio com base em um data factory existente. 

1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todos os data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory** .

    ![Gerenciador de Servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Criar um projeto do Visual Studio para um data factory existente

- Clique com o botão direito do mouse em um data factory no Gerenciador de Servidores e selecione **Exportar Data Factory para Novo Projeto** a fim de criar um projeto do Visual Studio com base em uma data factory existente.

    ![Exportar data factory para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar ferramentas de data factory para o Visual Studio
Para atualizar as ferramentas da Azure Data Factory para o Visual Studio, execute as seguintes etapas:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**. 
2. Selecione **Atualizações** no painel esquerdo e selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se você não vir essa entrada, você já tem a versão mais recente das ferramentas. 

## <a name="use-configuration-files"></a>Usar arquivos de configuração
Você pode usar arquivos de configuração no Visual Studio para configurar propriedades de serviços/tabelas/pipelines vinculados de forma diferente para cada ambiente.

Considere a definição de JSON a seguir para um serviço de Armazenamento do Azure vinculado. Para especificar a **connectionString** com valores diferentes para accountname e accountkey com base no ambiente (Desenvolvimento/Teste/Produção) no qual você está implantando entidades do Data Factory. Você pode obter esse comportamento usando um arquivo de configuração separado para cada ambiente.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Adicionar um arquivo de configuração
Adicione um arquivo de configuração para cada ambiente executando as seguintes etapas:   

1. Clique com botão direito no projeto do Data Factory em sua solução do Visual Studio, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **Config** na lista de modelos instalados no lado esquerdo, selecione **Arquivo de Configuração**, insira um **nome** para o arquivo de configuração e clique em **Adicionar**.

    ![Adicionar arquivo de configuração](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Adicione parâmetros de configuração e seus valores no formato a seguir:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Este exemplo configura a propriedade connectionString de um serviço de Armazenamento do Azure vinculado e um serviço vinculado do Azure SQL. Observe que a sintaxe para especificar o nome é [JsonPath](https://goessner.net/articles/JsonPath/).   

    Se o JSON tiver uma propriedade que tenha uma matriz de valores como mostrado no código a seguir:  

    ```json
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
    ```

    Configure as propriedades como mostrado no seguinte arquivo de configuração (use a indexação baseada zero):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nomes de propriedade com espaços
Se um nome de propriedade tiver espaços, use os colchetes como mostrado no exemplo a seguir (nome do servidor de banco de dados):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Implantar a solução usando uma configuração
Ao publicar entidades do Azure Data Factory no VS, você pode especificar a configuração que deseja usar para essa operação de publicação.

Para publicar as entidades em um projeto do Azure Data Factory usando o arquivo de configuração:   

1. Clique com o botão direito no projeto do Data Factory e clique em **Publicar** para ver a caixa de diálogo **Publicar Itens**.
2. Selecione um data factory existente ou especifique valores para a criação de um na página **Configurar data factory**, em seguida, clique em **Avançar**.   
3. Na página **Publicar Itens**: você vê uma lista suspensa com as configurações disponíveis para o campo **Selecionar Configuração de Implantação**.

    ![Selecionar arquivo de configuração](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selecione o **arquivo de configuração** que você deseja usar e clique em **Avançar**.
5. Confirme se você vê o nome do arquivo JSON na página **Resumo** e clique em **Avançar**.
6. Clique em **Concluir** depois que a operação de implantação for concluída.

Quando você implantar, os valores do arquivo de configuração serão usados para definir valores de propriedades nos arquivos JSON antes que as entidades sejam implantadas no serviço Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Usar o Cofre de Chaves do Azure
Não é aconselhável e costuma ser contra a política de segurança confirmar dados confidenciais, como cadeias de conexão, para o repositório de código. Confira o exemplo [Secure Publish de ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) no GitHub para saber mais sobre como armazenar informações confidenciais no Azure Key Vault e usá-lo durante a publicação de entidades do Data Factory. A extensão Secure Publish para Visual Studio permite que os segredos sejam armazenados no Key Vault e apenas referências a eles sejam especificadas em serviços vinculados/configurações de implantação. Essas referências são resolvidas quando você publica as entidades do Data Factory do Azure. Em seguida, esses arquivos podem ser confirmados para o repositório de origem sem expor nenhum segredo.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela.
