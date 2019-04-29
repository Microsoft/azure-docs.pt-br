---
title: Mover dados - Gateway de Gerenciamento de Dados | Microsoft Docs
description: Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4eb881992b7e40e0a9d67bd2cee94f1f09958e9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825772"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [copiar dados entre local e nuvem usando o Data Factory](../tutorial-hybrid-copy-powershell.md).

Este artigo fornece uma visão geral da integração de dados entre os armazenamentos de dados locais e os armazenamentos de dados na nuvem usando o Data Factory. Ele se baseia no artigo [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) e em outros artigos de conceitos principais de data factory: [conjuntos de dados](data-factory-create-datasets.md) e [pipelines](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway de gerenciamento de dados
Você deve instalar o Gateway de Gerenciamento de Dados em seu computador local para habilitar a movimentação de dados de/para um armazenamento de dados local. O gateway pode ser instalado no mesmo computador que o armazenamento de dados ou em outro computador, desde que o gateway possa se conectar com o armazenamento de dados.

> [!IMPORTANT]
> Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados. 

O passo a passo a seguir mostra como você pode criar uma Data Factory com um pipeline que move dados de um banco de dados local do **SQL Server** para um armazenamento de blobs do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Passo a passo: copiar os dados locais para a nuvem
Neste passo a passo, você realizará as seguintes etapas: 

1. Criar uma fábrica de dados.
2. Criar um Gateway de Gerenciamento de Dados. 
3. Criar serviços vinculados para armazenamentos de dados de origem e de coletor.
4. Nesta etapa, você cria conjuntos de dados para representar a entrada e a saída de dados.
5. Criar um pipeline com uma atividade de cópia para mover os dados.

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar este passo a passo, é necessário ter os seguintes pré-requisitos:

* **Assinatura do Azure**.  Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
* **Conta de Armazenamento do Azure**. Você utiliza o armazenamento de Blobs como um armazenamento de dados **destino/coletor** neste tutorial. Se você não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para conhecer as etapas para criar um.
* **SQL Server**. Neste tutorial, você utiliza um Banco de Dados do SQL Server local como um armazenamento de dados de **origem**. 

## <a name="create-data-factory"></a>Criar um data factory
Nesta etapa, você usa o Portal do Azure para criar uma instância do Azure Data Factory chamada **ADFTutorialOnPremDF**.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso**, clique em **Inteligência + análise** e clique em **Data Factory**.

   ![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na página **Novo data factory**, insira **ADFTutorialOnPremDF** como o Nome.

    ![Adicionar ao quadro inicial](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory “ADFTutorialOnPremDF” não está disponível**, altere o nome da data factory (por exemplo, seunomeADFTutorialOnPremDF) e tente criá-la novamente. Use esse nome em vez de ADFTutorialOnPremDF ao executar as etapas restantes neste tutorial.
   >
   > O nome do data factory pode ser registrado futuramente como um nome **DNS** e tornar-se visível publicamente.
   >
   >
4. Escolha a **assinatura do Azure** onde você deseja que o data factory seja criado.
5. Selecione um **grupo de recursos** existente ou crie um grupo de recursos. Para o tutorial, crie um grupo de recursos chamado: **ADFTutorialResourceGroup**.
6. Clique em **Criar** na página **Novo data factory**.

   > [!IMPORTANT]
   > Para criar instâncias de Data Factory, você deve ser um membro da função [Colaborador de Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) no nível de assinatura/grupo de recursos.
   >
   >
7. Depois que a criação estiver concluída, você verá a página **Data Factory** conforme mostrado na imagem abaixo:

   ![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Criar gateway
1. Na página **Data Factory**, clique no bloco **Criar e implantar** para iniciar o **Editor** do data factory.

    ![Bloco Criar e implantar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. No Editor de Data Factory, clique em **... Mais** na barra de ferramentas e clique em **Novo gateway de dados**. Como alternativa, você pode clicar com o botão direito em **Gateways de Dados** no modo de exibição de árvore e clicar em **Novo gateway de dados**.

   ![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na página **Criar**, digite **adftutorialgateway** como **nome** e clique em **OK**.     

    ![Página Criar Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Neste passo a passo, você cria o gateway lógico com apenas um nó (computador do Windows local). Você pode escalar horizontalmente um Gateway de Gerenciamento de Dados por meio da associação de vários computadores locais com o gateway. Você pode escalar verticalmente com o aumento do número de trabalhos de movimentação de dados que podem ser executados simultaneamente em um nó. Esse recurso também está disponível para um gateway lógico com um único nó. Consulte o artigo [Escalar o Gateway de Gerenciamento de Dados no Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes.  
4. Na página **Configurar**, clique em **Instalar diretamente neste computador**. Essa ação baixará o pacote de instalação para o gateway, instalará, configurará e registrará o gateway no computador.  

   > [!NOTE]
   > Use o Internet Explorer ou um navegador da Web compatível com o Microsoft ClickOnce.
   >
   > Se você estiver usando o Chrome, vá para a [loja na Web do Chrome](https://chrome.google.com/webstore/), pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.
   >
   > Faça o mesmo para o Firefox (instalar o suplemento). Clique no botão **Abrir menu** na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **Complementos**, pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.    
   >
   >

    ![Página Gateway – Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Arquivos de Programas\Microsoft Data Management Gateway\2.0\Shared**.

    Você também pode baixar e instalar o gateway manualmente usando os links nessa página e registrá-lo usando a chave mostrada na caixa de texto **NOVA CHAVE** .

    Confira o artigo [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway de Gerenciamento de Dados) para obter todos os detalhes sobre o gateway.

   > [!NOTE]
   > Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de gerenciamento de dados. Você pode acrescentar usuários adicionais ao grupo local de usuários do **Gateway de Gerenciamento de dados do Windows** . Os membros desse grupo podem usar a ferramenta Gerenciador de Configuração de Gateway de Gerenciamento de Dados para configurar o gateway.
   >
   >
5. Aguarde alguns minutos ou até ver a seguinte mensagem de notificação:

    ![Instalação do gateway bem-sucedida](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Inicie o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados** no computador. Na janela **Search**, digite **Gateway de Gerenciamento de Dados** para acessar esse utilitário. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Arquivos de Programas\Microsoft Data Management Gateway\2.0\Shared**

    ![Gerenciador de configuração de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Confirme que você vê a mensagem `adftutorialgateway is connected to the cloud service`. A barra de status inferior exibe a mensagem **Conectado ao serviço de nuvem** junto com uma **marca de seleção verde**.

    Na guia **Página inicial**, você também pode fazer as seguintes operações:

   * **Registrar** um gateway com uma chave do Portal do Azure usando o botão Registrar.
   * **Parar** o Serviço de Host do Gateway de Gerenciamento de Dados em execução no computador do gateway.
   * **Agendar atualizações** para serem instaladas em uma hora específica do dia.
   * Exibir quando o gateway foi **atualizado pela última vez**.
   * Especifique a hora em que uma atualização para o gateway pode ser instalada.
8. Alterne para a guia **Configurações** . O certificado especificado na seção **Certificado** é usado para criptografar/descriptografar as credenciais do armazenamento de dados local que você especifica no portal. (opcional) Como alternativa, clique em **Alterar** para usar seu próprio certificado. Por padrão, o gateway usa o certificado que é gerado automaticamente pelo serviço de Data Factory.

    ![Configuração do certificado do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Você também pode executar as seguintes ações na guia **Configurações**:

   * Exibir ou exportar o certificado que está sendo usado pelo gateway.
   * Alterar o ponto de extremidade HTTPS usado pelo gateway.    
   * Defina um proxy de HTTP para ser usado pelo gateway.     
9. (opcional) Mude para a guia **Diagnóstico** e marque a opção **Habilitar log detalhado** se você quiser habilitar o log detalhado que pode ser usado para solucionar problemas com o gateway. As informações de log podem ser encontradas no **Visualizador de Eventos** em **Logs de Aplicativos e Serviços** -> **nó Gateway de Gerenciamento de Dados**.

    ![Guia Diagnósticos](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Você também pode executar as seguintes ações na guia **Diagnóstico** :

   * Use a seção **Testar Conexão** para uma fonte de dados local usando o gateway.
   * Clique em **Exibir Logs** para ver o log de Gateway de Gerenciamento de Dados em uma janela do Visualizador de Eventos.
   * Clique em **Enviar Logs** para carregar um arquivo zip com logs dos últimos sete dias para a Microsoft a fim de facilitar a solução de problemas.
10. Na guia **Diagnóstico**, na seção **Testar Conexão**, selecione **SqlServer** para o tipo do armazenamento de dados, insira o nome do servidor de banco de dados, o nome do banco de dados, especifique o tipo de autenticação, digite o nome de usuário e a senha, e clique em **Testar** para testar se o gateway pode se conectar ao banco de dados.
11. Mude para o navegador da Web e, no **Portal do Azure**, clique em **OK** na página **Configurar** e na página **Novo gateway de dados**.
12. Você deve consultar **adftutorialgateway** em **Gateways de Dados** no modo de exibição de árvore à esquerda.  Se você clicar nisso, verá o JSON associado.

## <a name="create-linked-services"></a>Criar serviços vinculados
Nesta etapa, crie dois serviços vinculados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados local SQL Server e o serviço vinculado **AzureStorageLinkedService** vincula um repositório de blob do Azure ao Data Factory. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o repositório de blob do Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Adicionar um serviço vinculado a um banco de dados SQL Server local
1. No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de ferramentas e selecione **SQL Server**.

   ![Serviço vinculado do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. No **Editor JSON** à direita, siga estas etapas:

   1. Em **gatewayName**, especifique **adftutorialgateway**.    
   2. Em **connectionString**, siga estas etapas:    

      1. Para **nomedoservidor**, insira o nome do servidor que hospeda o banco de dados SQL Server.
      2. Para **nomedobancodedados**, insira o nome do banco de dados.
      3. Clique no botão **Criptografar** na barra de ferramentas. Você vê o aplicativo do Gerenciador de Credenciais.

         ![Aplicativo do Gerenciador de credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Na caixa de diálogo **Definindo Credenciais**, especifique o tipo de autenticação, nome de usuário e senha, e clique em **OK**. Se a conexão for bem-sucedida, as credenciais criptografadas serão armazenadas no JSON e a caixa de diálogo é fechada.
      5. Feche a guia vazia do navegador que iniciou a caixa de diálogo se ela não tiver sido fechada automaticamente e volte à guia com o portal do Azure.

         Na máquina de gateway, essas credenciais são **criptografadas** usando um certificado que o serviço Data Factory detém. Como alternativa, se você quiser usar o certificado associado ao Gateway de Gerenciamento de Dados, confira Definir credenciais com segurança.    
   3. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado do SQL Server. Você deverá ver o serviço vinculado na exibição de árvore.

      ![Serviço vinculado do SQL Server na exibição de árvore](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Adicionar um serviço vinculado para uma conta de armazenamento do Azure
1. No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de comandos e clique em **Armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento do Azure em **Nome da conta**.
3. Insira a chave da sua conta de armazenamento do Azure em **Chave de conta**.
4. Clique em **Implantar** para implantar o **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blobs do Azure). Antes de criar conjuntos de dados, primeiro é necessário fazer o seguinte (etapas detalhadas seguem a lista):

* Criar uma tabela chamada **emp** no banco de dados SQL Server adicionado como um serviço vinculado à data factory e inserir alguns exemplos de entradas na tabela.
* Crie um contêiner de blob chamado **adftutorial** na conta de armazenamento de blob do Azure que você adicionou como um serviço vinculado à data factory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparar o SQL Server local para o tutorial
1. No banco de dados especificado para o serviço vinculado do SQL Server local (**SqlServerLinkedService**), use o seguinte script SQL para criar a tabela **emp** no banco de dados.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Insira algum exemplo na tabela:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada

1. No **Editor de Data Factory**, clique em **... Mais**, clique em **Novo conjunto de dados** na barra de comandos e clique em **tabela SQL Server**.
2. Substitua o JSON no painel direito pelo texto a seguir:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Observe os seguintes pontos:

   * **type** é definido como **SqlServerTable**.
   * **tablename** está definido como **emp**.
   * **linkedServiceName** está definido como **SqlServerLinkedService** (você criou esse serviço vinculado na Etapa anteriormente neste passo a passo).
   * Para um conjunto de dados de entrada que não é gerada por outro pipeline na Azure Data Factory, você deve configurar **external** como **true**. Ela indica que os dados de entrada são produzidos externos ao serviço Data Factory do Azure. Opcionalmente, você pode especificar quaisquer políticas de dados externas usando o elemento **externalData** na seção **Policy**.    

   Confira [Mover dados para/de SQL Server](data-factory-sqlserver-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **Implantar** na barra de comando para implantar o conjunto de dados.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída

1. No **Editor Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e clique em **Armazenamento de Blobs do Azure**.
2. Substitua o JSON no painel direito pelo texto a seguir:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Observe os seguintes pontos:

   * **type** é definido como **AzureBlob**.
   * O **linkedServiceName** é definido como **AzureStorageLinkedService** (você criou esse serviço vinculado na Etapa 2).
   * **folderPath** é definido como **adftutorial/outfromonpremdf**, em que outfromonpremdf é a pasta no contêiner adftutorial. Crie o contêiner **adftutorial** se ele ainda não existir.
   * A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**).  O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

   Se você não especificar uma **fileName** para um **tabela de saída**, os arquivos gerados no **folderPath** são nomeados no seguinte formato: `Data.<Guid>.txt` (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade partitionedBy. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Confira [Mover dados para/de Armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md) para obter detalhes sobre as propriedades JSON.
3. Clique em **Implantar** na barra de comando para implantar o conjunto de dados. Confirme que você vê os conjuntos de dados na exibição de árvore.  

## <a name="create-pipeline"></a>Criar um pipeline
Nesta etapa, você criará um **pipeline** com uma **Atividade de Cópia** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1. No Editor de Data Factory, clique em **... Mais** e clique em **Novo pipeline**.
2. Substitua o JSON no painel direito pelo texto a seguir:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
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
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte.
   >
   >

   Observe os seguintes pontos:

   * Na seção de atividades, há somente uma atividade cujo **type** é definido como **Copy**.
   * A **entrada** da atividade é definida como **EmpOnPremSQLTable** e a **saída** da atividade é definida como **OutputBlobTable**.
   * No **typeProperties** seção, **SqlSource** é especificado como o **tipo de fonte** e **BlobSink** é especificado como o **tipo de coletor**.
   * A consulta SQL `select * from emp` é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

   Ambos os valores de data/hora de início e de término devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo:  2014-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial.

   Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor para a propriedade **end**.

   Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada conjunto de dados da Azure Data Factory.

   No exemplo, como cada fatia de dados é produzida por hora, existem 24 fatias de dados.        
3. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que o pipeline aparece na exibição em árvore no nó **Pipelines**.  
4. Agora, clique em **X** duas vezes para fechar as páginas até que você volte à página **Data Factory** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, conjuntos de dados e uma pipeline e a pipeline agendada com êxito.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Exibir a data factory em um Modo de Exibição de Diagrama
1. No **portal do Azure**, clique no bloco **Diagrama** na página inicial do data factory **ADFTutorialOnPremDF**. :

    ![Link do diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Você deverá ver o diagrama semelhante à imagem abaixo:

    ![Exibição de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e conjuntos de dados automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados).  Você pode clicar duas vezes em um objeto (pipeline ou conjunto de dados de entrada/saída) para ver as propriedades dele.

## <a name="monitor-pipeline"></a>Monitorar o pipeline
Nesta etapa, você utiliza o portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre monitoramento, consulte [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

1. No diagrama, clique duas vezes em **EmpOnPremSQLTable**.  

    ![Fatias de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Observe que todas as fatias de dados estão no estado **Pronto** porque a duração do pipeline (hora de início até a hora de término) está no passado. Isso também ocorre porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior. Para exibir todas as fatias, clique em **Ver mais** na parte inferior da lista de fatias.
3. Agora, na página **Conjuntos de Dados**, clique em **OutputBlobTable**.

    ![Fatias de OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Clique em qualquer fatia de dados na lista e você deverá ver a página **Fatia de Dados**. Você vê as execuções de atividade para essa fatia. Você normalmente vê apenas uma atividade sendo executada.  

    ![Folha Fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.
5. Clique na **execução de atividade** na lista na parte inferior para ver **detalhes de execução da atividade**.

   ![Página Detalhes da Execução da Atividade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Você veria informações como taxa de transferência, duração e o gateway usado para transferir os dados.
6. Clique no **X** para fechar todas as páginas até você
7. voltar para a home page do **ADFTutorialOnPremDF**.
8. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF** e execute uma consulta drill-through nas tabelas de entrada (**Consumed**) ou nos conjuntos de dados de saída (**Produced**).
9. Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para verificar se um arquivo/blob é criado para cada hora.

   ![Gerenciador de Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Próximas etapas
* Confira o artigo [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway de Gerenciamento de Dados) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados.
* Confira [Copiar dados do Blob do Azure para o SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como usar a Atividade de Cópia para mover dados de um repositório de dados de origem para um repositório de dados de coletor.
