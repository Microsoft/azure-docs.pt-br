---
title: Criar seu primeiro data factory (Visual Studio) | Microsoft Docs
description: Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando o Visual Studio.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7398c0c9-7a03-4628-94b3-f2aaef4a72c5
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f3c4fc379ac932e66c5d02e08e72ef4d16db638b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836696"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Tutorial: Criar um data factory usando o Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Visão geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Caso esteja usando a versão atual do serviço Data Factory, consulte [Início Rápido: Criar um data factory usando o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Este tutorial mostra como criar um Azure data factory usando o Visual Studio. Crie um projeto do Visual Studio usando o modelo de projeto de Data Factory, defina as entidades da Data Factory (serviços vinculados, conjuntos de dados e pipeline) no formato JSON e, em seguida, publique/implante essas entidades à nuvem. 

O pipeline deste tutorial tem uma atividade: **Atividade do Hive no HDInsight**. Esta atividade executa um script de hive em um cluster do HDInsight do Azure que transforma os dados de entrada para gerar dados de saída. O pipeline é agendado para ser executado uma vez por mês entre os horários de início e término especificados. 

> [!NOTE]
> Este tutorial não mostra como copiar dados usando Azure Data Factory. Para obter um tutorial sobre como copiar dados usando o Azure Data Factory, confira [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Passo a passo: Criar e publicar entidades da Data Factory
Eis as etapas executadas como parte deste tutorial:

1. Criar dois serviços vinculados: **AzureStorageLinkedService1** e **HDInsightOnDemandLinkedService1**. 
   
    Neste tutorial, os dados de entrada e saída da atividade de hive estão no mesmo armazenamento de Blobs do Azure. Você pode usar um cluster do HDInsight sob demanda para processar dados de entrada existentes para gerar dados de saída. O cluster do HDInsight sob demanda é criado automaticamente para você pela Azure Data Factory em tempo de execução quando os dados de entrada estão prontos para serem processados. Você deve vincular seus armazenamentos de dados ou computadores à data factory para que o serviço Data Factory possa se conectar com eles durante tempo de execução. Ou seja, vincule sua conta de armazenamento do Azure à data factory usando o AzureStorageLinkedService1, e um cluster do HDInsight sob demanda com o HDInsightOnDemandLinkedService1. Ao publicar, especifique o nome da data factory a ser criado ou existente.  
2. Crie dois conjuntos de dados: **InputDataset** e **OutputDataset**, que representam os dados de entrada/saída armazenados no Armazenamento de Blobs do Azure. 
   
    Essas definições de conjunto de dados se referem ao serviço vinculado do armazenamento do Azure que você criou na etapa anterior. Para o InputDataset, você pode especificar o contêiner de blobs (adfgetstarted) e a pasta (inptutdata) que contém um blob com os dados de entrada. Para o OutputDataset, você pode especificar o contêiner de blobs (adfgetstarted) e a pasta (partitioneddata) que contém os dados de saída. Você também pode especificar outras propriedades, como estrutura, disponibilidade e política.
3. Crie um pipeline chamado **MyFirstPipeline**. 
  
    Neste passo a passo, o pipeline tem apenas uma atividade: **Atividade do Hive no HDInsight**. Essa atividade transforma dados de entrada para gerar dados de saída ao executar um script de hive em um cluster do HDInsight sob demanda. Para saber mais sobre a atividade de hive, confira [Atividade de Hive](data-factory-hive-activity.md) 
4. Crie uma data factory denominada **DataFactoryUsingVS**. Implante o data factory e todas as entidades de Data Factory (serviços vinculados, tabelas e pipeline).
5. Depois de publicar, use as folhas do portal do Azure e aplicativos de gerenciamento e monitoramento para monitorar o pipeline. 
  
### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Leia o artigo [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de **pré-requisito** . Você também pode selecionar a opção **Visão geral e Pré-requisitos** na lista suspensa, na parte superior, para alternar para o artigo. Depois de concluir os pré-requisitos, alterne de volta para este artigo, selecionando a opção **Visual Studio** na lista suspensa.
2. Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.  
3. Você deve ter os seguintes itens instalados no seu computador:
   * Visual Studio 2013 ou Visual Studio 2015
   * Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
   * Baixe o último plug-in do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Atualize também o plug-in executando as seguintes etapas: No menu, clique em **Ferramentas** -> **Extensões e Atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **Atualizar**.

Agora, vamos usar o Visual Studio para criar um data factory do Azure.

### <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio
1. Inicie o **Visual Studio 2013** ou o **Visual Studio 2015**. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**. Você deverá ver a caixa de diálogo **Novo Projeto** .  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto Vazio de Data Factory**.   

    ![Caixa de diálogo Novo projeto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Insira um **nome** para o projeto, o **local**, um nome para a **solução** e clique em **OK**.

    ![Gerenciador de soluções](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, você criará dois serviços vinculados: **Armazenamento do Azure** e **HDInsight sob demanda**. 

O serviço vinculado ao armazenamento do Azure vincula sua conta de armazenamento do Azure à data factory, fornecendo as informações de conexão. O serviço Data Factory usa a cadeia de conexão da configuração de serviço vinculado para se conectar ao armazenamento do Azure em tempo de execução. Esse armazenamento possui dados de entrada e saída para o pipeline e o arquivo de script do hive usado pela atividade de hive. 

Com o serviço vinculado HDInsight sob demanda, o cluster do HDInsight é criado automaticamente em tempo de execução quando os dados de entrada estão prontos para serem processados. O cluster é excluído após a conclusão do processamento e se mantém ocioso durante o período especificado. 

> [!NOTE]
> Você pode criar um data factory, especificando seu nome e as configurações no momento da publicação da solução da sua Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
1. Clique com o botão direito do mouse em **Serviços Vinculados** no Gerenciador de Soluções, aponte para **Adicionar** e clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Vinculado de Armazenamento do Azure** na lista e clique em **Adicionar**.
    ![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Substitua `<accountname>` e `<accountkey>` pelo nome e chave da conta de armazenamento do Azure. Para saber como conseguir sua chave de acesso de armazenamento, consulte as informações sobre como exibir, copiar e regenerar chaves de acesso de armazenamento em [Gerenciar sua conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).
    ![Serviço vinculado de armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Salve o arquivo **AzureStorageLinkedService1.json** .

#### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço vinculado do Azure HDInsight
1. No **Gerenciador de Soluções**, clique com botão direito em **Serviços Vinculados**, aponte para **Adicionar** e clique em **Novo Item**.
2. Selecione **erviço Vinculado Sob Demanda do HDInsight**  e clique em **Adicionar**.
3. Substitua o **JSON** pelo seguinte JSON:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    Propriedade | DESCRIÇÃO
    -------- | ----------- 
    clusterSize | Especifica o tamanho do cluster do HDInsight Hadoop.
    timeToLive | Especifica que o tempo ocioso do cluster HDInsight antes de ser excluído.
    linkedServiceName | Especifica a conta de armazenamento usada para armazenar os logs gerados pelo cluster do HDInsight Hadoop. 

    > [!IMPORTANT]
    > O cluster do HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (linkedServiceName). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster do HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (timeToLive). O cluster será excluído automaticamente quando o processamento for concluído.
    > 
    > Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

    Para obter mais informações sobre as propriedades JSON, confira o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Salve o arquivo **HDInsightOnDemandLinkedService1.json** .

### <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService1** que você criou anteriormente neste tutorial. O serviço vinculado aponta para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada e de saída.   

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Gerenciador de Soluções**, clique com botão direito em **Tabelas**, aponte para **Adicionar**e clique em **Novo Item**.
2. Selecione o **Blob do Azure** na lista, altere o nome do arquivo para **InputDataSet.json** e clique em **Adicionar**.
3. Substitua o **JSON** no editor pelo seguinte snippet de código JSON:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Este snippet de JSON define um conjunto de dados chamado **AzureBlobInput** que representa dados de entrada para a atividade de hive no pipeline. Além disso, você especifica que os dados de entrada estão localizados no contêiner de blobs chamado `adfgetstarted` e na pasta chamada `inputdata`.

    A tabela a seguir fornece descrições das propriedades de JSON usadas no snippet de código:

    Propriedade | DESCRIÇÃO |
    -------- | ----------- |
    Tipo |A propriedade de tipo é definida como **AzureBlob** porque os dados residem no armazenamento de blobs do Azure.
    linkedServiceName | Refere-se ao AzureStorageLinkedService1 que você criou anteriormente.
    fileName |Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Nesse caso, somente o input.log será processado.
    type | Os arquivos de log estão em formato de texto, então utilizaremos TextFormat. |
    columnDelimiter | as colunas nos arquivos de log são delimitadas pelo caractere de vírgula (`,`)
    frequency/interval | a frequência é definida como Mês e o intervalo como 1, o que significa que as fatias de entrada estão disponíveis mensalmente.
    external | Se os dados de entrada para a atividade não forem gerados pelo pipeline, essa propriedade será definida como verdadeira. Essa propriedade só é especificada em conjuntos de dados de entrada. O conjunto de dados de entrada da primeira atividade sempre será definido como verdadeiro.
4. Salve o arquivo **InputDataset.json** .

#### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Agora, você cria o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. No **Gerenciador de Soluções**, clique com botão direito em **Tabelas**, aponte para **Adicionar**e clique em **Novo Item**.
2. Selecione **Blob do Azure** na lista, altere o nome do arquivo para **OutputDataset.json** e clique em **Adicionar**.
3. Substitua o **JSON** no editor pelo seguinte JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    O snippet JSON define um conjunto de dados chamado **AzureBlobOutput** que representa os dados de saída produzidos pela atividade de hive no pipeline. Você especifica que os dados de saída produzidos pela atividade de hive são colocados no contêiner de blobs `adfgetstarted` e na pasta `partitioneddata`. 
    
    A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente. O conjunto de dados de saída conduzem a agenda do pipeline. O pipeline é executado mensalmente entre suas horas de início e término. 

    Consulte a seção **Criar o conjunto de dados de entrada** para obter descrições dessas propriedades. Você não define a propriedade externa em um conjunto de dados de saída porque o conjunto de dados é produzido pelo pipeline.
4. Salve o arquivo **OutputDataset.json** .

### <a name="create-pipeline"></a>Criar um pipeline
Até o momento, você criou o serviço vinculado do armazenamento do Azure e conjuntos de dados de entrada e saída. Agora, você pode criar um pipeline com uma atividade **HDInsightHive**. A **entrada** para a atividade de hive é definida como **AzureBlobInput** e a**saída**, como **AzureBlobOutput**. Uma fatia de um conjunto de dados de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), e a fatia de saída também é produzida mensalmente. 

1. No **Gerenciador de Soluções**, clique com botão direito em **Pipelines**, aponte para **Adicionar** e clique em **Novo Item.**
2. Selecione **Pipeline de Transformação do Hive** na lista e clique em **Adicionar**.
3. Substitua o **JSON** pelo snippet a seguir:

    > [!IMPORTANT]
    > Substitua `<storageaccountname>` com o nome da sua conta de armazenamento.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Substitua `<storageaccountname>` com o nome da sua conta de armazenamento.

    O snippet JSON define um pipeline que consiste em uma única atividade (atividade de hive). Esta atividade executa um script de hive para processar dados de entrada em um cluster do HDInsight sob demanda para gerar dados de saída. Na seção de atividades do pipeline JSON, você verá apenas uma atividade na matriz com o tipo definido como **HDInsightHive**. 

    Nas propriedades de tipo especificados para a atividade de Hive do HDInsight, você especifica o caminho, os parâmetros e qual serviço vinculado de armazenamento do Azure tem o arquivo de script do hive. 

    O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService) e na pasta `script` no contêiner `adfgetstarted`.

    A seção `defines` é usada para especificar as configurações de tempo de execução passadas para o script do hive como valores de configuração de Hive (por exemplo, `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`).

    As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline. Você configurou o conjunto de dados a ser produzido mensalmente, portanto, apenas uma fatia será produzida pelo pipeline (porque o mês é o mesmo nas datas de início e término).

    Na atividade do JSON, você especifica que o script do Hive é executado na máquina especificada pelo **nomeServiçoVinculado** – **HDInsightOnDemandLinkedService**.
4. Salve o arquivo **HiveActivity1.json** .

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Adicionar partitionweblogs.hql e input.log como uma dependência
1. Clique com botão direito em **Dependências** na janela do **Gerenciador de Soluções**, aponte para **Adicionar**e clique em **Item Existente**.  
2. Navegue até **C:\ADFGettingStarted** e selecione os arquivos **partitionweblogs.hql** e **input.log**, e clique em **Adicionar**. Você criou esses dois arquivos como parte dos pré-requisitos da [Visão Geral do Tutorial](data-factory-build-your-first-pipeline.md).

Quando você publicar a solução na próxima etapa, o arquivo **partitionweblogs.hql** será carregado para a pasta de **script** no contêiner de blobs `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publicar/implantar entidades de data factory
Nesta etapa, você pode publicar as entidades da Data Factory (serviços vinculados, conjuntos de dados e pipeline) no seu projeto para o serviço Azure Data Factory. No processo de publicação, você deve especificar o nome da sua data factory. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**.
2. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
3. Você deve ver a caixa de diálogo a seguir:

   ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Na página **Configurar data factory**, execute as seguintes etapas:

    ![Publicação - configurações da nova data factory](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Selecione a opção **Criar Nova Data Factory** .
   2. Insira um **nome** exclusivo para o armazenamento de dados. Por exemplo:  **DataFactoryUsingVS09152016**. O nome deve ser globalmente exclusivo.
   3. Selecione a assinatura certa para o campo **Assinatura** . 
        > [!IMPORTANT]
        > Se você não vir nenhuma assinatura, verifique se está conectado usando uma conta que seja administrador ou coadministrador da assinatura.
   4. Selecione o **grupo de recursos** para o data factory a ser criado.
   5. Selecione a **região** do data factory.
   6. Clique em **Avançar** para alternar para a página **Publicar Itens**. (Pressione **TAB** para sair do campo Nome se o botão **Avançar** estiver desabilitado).

      > [!IMPORTANT]
      > Se você receber o erro **O nome da data factory “DataFactoryUsingVS” não está disponível** durante a publicação, altere o nome (por exemplo, yournameDataFactoryUsingVS). Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.   
1. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.

    ![Publicar página de item](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.

    ![Página Resumo](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Na página **Status da Implantação** , você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado.

Pontos importantes a serem considerados:

- Se você receber o erro **Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**, siga um dos seguintes procedimentos e tente a publicação novamente:
    - No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado.

        ```powershell
        Get-AzResourceProvider
        ```
    - Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.
- O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
- Para criar instâncias do Data Factory, você precisa ser administrador ou coadministrador da assinatura do Azure

### <a name="monitor-pipeline"></a>Monitorar o pipeline
Nesta etapa, você deve monitorar o pipeline usando o modo de exibição de diagrama da data factory. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Monitorar o pipeline usando a Exibição de Diagrama
1. Faça logon no [portal do Azure](https://portal.azure.com/), siga as etapas abaixo:
   1. Clique em **Mais serviços** e **Data factories**.
       
        ![Procurar data factories](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Selecione o nome do data factory (por exemplo: **DataFactoryUsingVS09152016**) na lista de data factories.
   
       ![Selecionar o data factory](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Na home page do seu data factory, clique em **Diagrama**.

    ![Bloco do diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. Na Exibição de Diagrama, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.

    ![Exibição de diagrama](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Para exibir todas as atividades no pipeline, clique com o botão direito do mouse no pipeline no diagrama e clique em Abrir Pipeline.

    ![Menu do pipeline aberto](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Confirme que você vê a atividade HDInsightHive no pipeline.

    ![Abrir a exibição do pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Para navegar de volta ao modo de exibição anterior, clique em **Data factory** no menu de atalho na parte superior.
6. Na **Exibição do Diagrama**, clique duas vezes no conjunto de dados **AzureBlobInput**. Confirme se a fatia está no estado **Pronto** . Pode levar alguns minutos para que a fatia apareça no estado Pronto. Se isso não acontecer depois de algum tempo, veja se o arquivo de entrada (input.log) está posicionado no contêiner à direita (`adfgetstarted`) e na pasta (`inputdata`). E confira se a propriedade **externa** no conjunto de dados de entrada está definida como **verdadeira**. 

   ![Fatia de entrada no estado pronto](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Clique em **X** para fechar a folha **AzureBlobInput**.
8. Na **Exibição do Diagrama**, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá a fatia que está sendo processada.

   ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Quando o processamento for concluído, você verá a fatia no estado **Pronto** .

   > [!IMPORTANT]
   > A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo (20 minutos, aproximadamente). Portanto, espere que o pipeline demore **cerca de 30 minutos** para processar a fatia.  
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Quando a fatia estiver no estado **Pronto**, verifique a pasta `partitioneddata` no contêiner `adfgetstarted` em seu armazenamento de blobs para os dados de saída.  

    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Clique na fatia para ver detalhes sobre ela em uma folha **Fatia de dados** .

    ![Detalhes da fatia de dados](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Clique em uma execução da atividade na **Lista de execuções da atividade** para ver detalhes sobre uma execução da atividade (atividade de Hive em nosso cenário) em uma janela **Detalhes de execução da atividade**. 
  
    ![Detalhes da execução da atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Nos arquivos de log, você pode ver a consulta do Hive executada e as informações de status. Esses logs são úteis para solucionar problemas.  

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar o Portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorar o pipeline usando o aplicativo Monitorar e Gerenciar
Você também pode usar o aplicativo Monitorar e Gerenciar para monitorar os pipelines. Para obter informações detalhadas sobre como usar esse aplicativo, veja [Monitorar e gerenciar confira do Azure Data Factory usando o aplicativo Monitorar e Gerenciar](data-factory-monitor-manage-app.md).

1. Clique no bloco Monitorar e Gerenciar.

    ![Bloco Monitorar e Gerenciar](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Você deve ver o aplicativo Monitorar e Gerenciar. Altere a **Hora de início** e a **Hora de término** para coincidir com o início (04-01-2016 12:00 AM) e o término (04-02-2016 12:00 AM) do seu pipeline, e clique **Aplicar**.

    ![Aplicativo Monitorar e Gerenciar](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Para ver detalhes sobre uma janela de atividade, selecione-a na **Lista de janela de atividade**.
    ![Detalhes da janela Atividade](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou fazer o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta `inputdata` do contêiner `adfgetstarted`.

### <a name="additional-notes"></a>Observações adicionais
- Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar um script do Hive para transformar dados de entrada. Confira [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver todas as fontes e coletores com suporte da Atividade de Cópia. Confira os [serviços vinculados de computação](data-factory-compute-linked-services.md) para ver uma lista dos serviços de computação com suporte do Data Factory.
- Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Confira [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver todas as fontes e coletores com suporte da Atividade de Cópia. Confira os [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter a lista de serviços de computação com suporte pela Data Factory e as [Atividades de transformação](data-factory-data-transformation-activities.md) que podem ser executadas neles.
- Confira [Como mover dados de/para Blobs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades JSON usadas no armazenamento do Azure vinculado à definição de serviço.
- Você pode usar seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Veja [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter detalhes.
-  O Data Factory cria um cluster HDInsight **baseado no Linux** para você com o JSON anterior. Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
- O cluster do HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (linkedServiceName). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster do HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (timeToLive). O cluster será excluído automaticamente quando o processamento for concluído.
    
    Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.
- Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. 
- Este tutorial não mostra como copiar dados usando Azure Data Factory. Para obter um tutorial sobre como copiar dados usando o Azure Data Factory, confira [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>Usar o Gerenciador de Servidores para exibir os data factories
1. No **Visual Studio**, clique em **Exibir** no menu e em **Gerenciador de Servidores**.
2. Na janela Gerenciador de Servidores, expanda **Azure** e expanda **Data Factory**. Se **Entrar no Visual Studio** for exibido, insira a **conta** associada à sua assinatura do Azure e clique em **Continuar**. Insira a **senha** e clique em **Entrar**. O Visual Studio tenta obter informações sobre todos os data factories do Azure em sua assinatura. Você verá o status da operação na janela **Lista de Tarefas de Data Factory** .

    ![Gerenciador de Servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Clique com o botão direito em um data factory e selecione **Exportar Fábrica de Dados para Novo Projeto** para criar um projeto do Visual Studio com base em um data factory existente.

    ![Exportar data factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

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
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para processar dados ao executar o script Hive em um cluster hadoop do HDInsight. Você usou o Data Factory Editor no portal do Azure para executar as seguintes etapas:  

1. Foi criada uma **data factory**do Azure.
2. Foram criados dois **serviços vinculados**:
   1. **Armazenamento do Azure** para vincular seu armazenamento de blobs do Azure que contém os arquivos de entrada/saída para a data factory.
   2. **Azure HDInsight** sob demanda para vincular um cluster Hadoop do HDInsight sob demanda à data factory. O Azure Data Factory cria um cluster Hadoop do HDInsight just-in-time para processar dados de entrada e gerar dados de saída.
3. Foram criados dois **conjuntos de dados**que descrevem dados de entrada e de saída para a atividade Hive do HDInsight no pipeline.
4. Foi criado um **pipeline** com uma atividade **Hive do HDInsight**.  

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight sob demanda. Para ver como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um blob do Azure para o SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 


## <a name="see-also"></a>Veja também

| Tópico | DESCRIÇÃO |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como usá-los para construir fluxos de trabalho orientados a dados para o seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory. |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) |Este artigo fornece uma lista de atividades de transformação de dados (como a transformação do Hive do HDInsight usado neste tutorial) com suporte do Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. |
