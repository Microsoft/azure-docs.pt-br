---
title: Copiar dados do SQL Server para o Armazenamento de Blobs usando o PowerShell
description: Saiba como copiar dados de um armazenamento de dados local para a nuvem do Azure usando o runtime de integração auto-hospedada no Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: 33c3138013c43062465cce16189e6c19d22ae4d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724924"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Tutorial: Copiar dados de um banco de dados do SQL Server para o Armazenamento de Blobs do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você usa o Azure PowerShell para criar um pipeline de data factory que copia dados de um banco de dados do SQL Server para o Armazenamento de Blobs do Azure. Você cria e usa um runtime de integração auto-hospedada, o qual move dados entre locais e armazenamentos de dados da nuvem.

> [!NOTE]
> Este artigo não fornece uma introdução detalhada do serviço de Data Factory. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um runtime de integração auto-hospedada.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorar a execução de pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para entrar no Azure deve ter uma função de *Colaborador* ou *Proprietário* atribuída, ou deve ser de um *administrador* da assinatura do Azure.

Para exibir as permissões que você tem com a assinatura, vá até o portal do Azure, selecione seu nome de usuário no canto superior direito e depois **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para ver instruções de exemplo de como adicionar um usuário a uma função, confira o artigo [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server como um armazenamento de dados de *origem*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server (origem) para um Armazenamento de Blobs do Azure (coletor). Depois, crie uma tabela chamada **emp** no seu banco de dados do SQL Server e insira algumas entradas de exemplo na tabela.

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Conecte-se à sua instância do SQL Server usando suas credenciais.

1. Crie um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**.

1. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**.

1. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Neste tutorial, use uma conta de armazenamento do Azure de uso geral (especificamente o Armazenamento de Blobs do Azure) como um armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de uso geral do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server (origem) para esse Armazenamento de Blobs do Azure (coletor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste tutorial. Obtenha o nome e a chave da sua conta de armazenamento fazendo o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta e senha do Azure.

1. No painel esquerdo, selecione **Mais serviços**, filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a sua conta de armazenamento.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial.

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial
Nesta seção, você cria um contêiner de blob chamado **adftutorial** no armazenamento de Blobs do Azure.

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**.

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço Blob**, selecione **Contêiner**.

1. Na janela **Novo contêiner**, na caixa **Nome**, insira **adftutorial** e depois selecione **OK**.

    ![Inserir o nome do contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Na lista de contêineres, selecione **adftutorial**.  

1. Mantenha aberta a janela **contêiner** para **adftutorial**. Use-a para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale a versão mais recente do Azure PowerShell caos ainda não a tenha em seu computador. Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Fazer logon no PowerShell

1. Inicie o PowerShell em seu computador e mantenha-o aberto até a conclusão deste tutorial de início rápido. Se você fechá-lo e reabri-lo, precisará executar esses comandos novamente.

1. Execute o comando a seguir e depois insira o nome de usuário e a senha do Azure que você usa para entrar no portal do Azure:

    ```powershell
    Connect-AzAccount
    ```        

1. Se você tiver várias assinaturas do Azure, execute o comando a seguir para selecionar as assinaturas com as quais quer trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Defina uma variável para o nome do grupo de recursos que você usará nos comandos do PowerShell posteriormente. Copie o comando a seguir para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) (entre aspas duplas; por exemplo, `"adfrg"`) e depois execute o comando. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Para criar o grupo de recursos do Azure, execute o seguinte comando:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.

1. Defina uma variável para o nome do data factory que você pode usar nos comandos do PowerShell mais tarde. O nome deve começar com uma letra ou um número e pode conter apenas letras, números e o caractere traço (-).

    > [!IMPORTANT]
    >  Atualize o nome do data factory usando um nome globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Defina uma variável para o local do data factory:

    ```powershell
    $location = "East US"
    ```  

1. Para criar o data factory, execute o seguinte cmdlet `Set-AzDataFactoryV2`:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * O nome do data factory deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Para criar instâncias de data factory, a conta de usuário usada para entrar no Azure deve ter uma função de *colaborador* ou *proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure.
> * Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure e assim por diante) e serviços de computação (HDInsight e assim por diante) usados pelo data factory podem estar em outras regiões.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O Integration Runtime auto-hospedado é o componente que copia dados do banco de dados do SQL Server em seu computador para o armazenamento de Blobs do Azure.

1. Crie uma variável para o nome do Integration Runtime. Use um nome exclusivo e anote o nome. Você o usará posteriormente neste tutorial.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Criar um runtime de integração auto-hospedada.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Veja o exemplo de saída:

    ```console
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Para recuperar o status do Integration Runtime criado, execute o comando a seguir:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Veja o exemplo de saída:

    ```console
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Para recuperar as *chaves de autenticação* para registrar o Integration Runtime auto-hospedado com o serviço Data Factory na nuvem, execute o comando a seguir. Copie uma das chaves (excluindo as aspas) para registrar Integration Runtime auto-hospedado instalado em seu computador na próxima etapa.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Veja o exemplo de saída:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Instalar o Integration Runtime
1. Baixe o [Integration Runtime do Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) em uma máquina local do Windows e execute a instalação.

1. No assistente **Bem-vindo à instalação do Microsoft Integration Runtime**, selecione **Avançar**.  

1. Na janela **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e selecione **Avançar**.

1. No janela **Pasta de Destino**, selecione **Avançar**.

1. Na janela **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

1. No assistente **Instalação do Microsoft Integration Runtime concluída**, selecione **Concluir**.

1. Na janela **Registrar Integration Runtime (auto-hospedado)** , cole a chave que você salvou na seção anterior e selecione **Registrar**.

    ![Registrar Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. Na janela **Novo Nó do Integration Runtime (auto-hospedado)** , selecione **Concluir**.

    ![Janela Novo Nó do Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Quando o Integration Runtime auto-hospedado for registrado com êxito, a mensagem a seguir será exibida:

    ![Registrado com êxito](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. Na janela **Registrar Integration Runtime (auto-hospedado)** , selecione **Iniciar o Gerenciador de Configurações**.

1. Quando o nó estiver conectado ao serviço de nuvem, a mensagem a seguir será exibida:

    ![O nó está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Teste a conectividade com o seu banco de dados do SQL Server fazendo o seguinte:

    a. Na janela **Gerenciador de Configurações**, alterne para a guia **Diagnóstico**.

    b. Na caixa **Tipo da fonte de dados**, selecione **SqlServer**.

    c. Insira o nome do servidor.

    d. Insira o nome do banco de dados.

    e. Selecione o modo de autenticação.

    f. Insira o nome de usuário.

    g. Insira a senha associada ao nome de usuário.

    h. Para confirmar que esse runtime de integração pode se conectar ao SQL Server, selecione **Testar**.  
    ![Conectividade bem-sucedida](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Se a conexão tiver êxito, uma marca de seleção verde será exibida. Caso contrário, você receberá uma mensagem de erro associada à falha. Corrija quaisquer problemas e verifique se o Integration Runtime pode se conectar ao seu SQL Server.

    Anote todos os valores anteriores para uso posterior neste tutorial.

## <a name="create-linked-services"></a>Criar serviços vinculados
Para vincular seus armazenamentos de dados e serviços de computação ao data factory, crie serviços vinculados no data factory. Neste tutorial, você vincula sua conta de armazenamento do Azure e a instância do SQL Server para o armazenamento de dados. Os serviços vinculados têm as informações de conexão que o serviço do Data Factory usa no runtime para se conectar a eles.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Criar um serviço vinculado do Armazenamento do Azure (destino/coletor)
Nesta etapa, você vincula a conta de armazenamento do Azure ao data factory.

1. Crie um arquivo JSON denominado *AzureStorageLinkedService.json* na pasta *C:\ADFv2Tutorial*, com o código a seguir. Se a pasta *ADFv2Tutorial* ainda não existir, crie-a.  

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua \<accountName> e \<accountKey> pelo nome e a chave da sua conta de armazenamento do Azure. Você os anotou na seção [Pré-requisitos](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. No PowerShell, alterne para a pasta *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Para criar um serviço vinculado, AzureStorageLinkedService, execute o cmdlet `Set-AzDataFactoryV2LinkedService` a seguir:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Aqui está uma amostra de saída:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Se você receber um erro "arquivo não encontrado", confirme que o arquivo existe executando o comando `dir`. Se o nome do arquivo tiver a extensão *.txt* (por exemplo, AzureStorageLinkedService.json.txt), remova-a e execute novamente o comando do PowerShell.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Criar e criptografar um serviço vinculado do SQL Server (origem)
Nesta etapa, você vincula sua instância do SQL Server ao data factory.

1. Crie um arquivo JSON file chamado *SqlServerLinkedService.json* na pasta *C:\ADFv2Tutorial* usando o seguinte código:

    > [!IMPORTANT]
    > Selecione a seção que está baseada na autenticação usada para se conectar ao SQL Server.

    **Usando a autenticação SQL (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Usando a autenticação do Windows:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Selecione a seção que está baseada na autenticação usada para se conectar à sua instância do SQL Server.
    > - Substitua **\<integration runtime name>** pelo nome de seu runtime de integração.
    > - Antes de salvar o arquivo, substitua **\<servername>** , **\<databasename>** , **\<username>** e **\<password>** por valores da sua instância do SQL Server.
    > - Se precisar usar um caractere de barra invertida (\\) em sua conta de usuário e nome de servidor, use o caractere de escape à frente (\\). Por exemplo, use *mydomain\\\\myuser*.

1. Para criptografar os dados confidenciais (nome de usuário, senha e assim por diante), execute o cmdlet `New-AzDataFactoryV2LinkedServiceEncryptedCredential`.  
    Essa criptografia garante que as credenciais sejam criptografadas usando a API de Proteção de Dados (DPAPI). As credenciais criptografadas são armazenadas localmente no nó do runtime de integração auto-hospedada (computador local). O conteúdo de saída pode ser redirecionado para outro arquivo JSON (nesse caso, *encryptedLinkedService.json*) que contém credenciais criptografadas.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Execute o comando a seguir, o qual cria EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados de entrada e saída. Eles representam dados de entrada e saída da operação de cópia, que copia dados do banco de dados SQL Server para o Armazenamento de Blobs do Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Criar um conjunto de dados para o banco de dados SQL Server fonte
Nesta etapa, você define um conjunto de dados que representa os dados na instância do banco de dados do SQL Server. O conjunto de dados é do tipo SqlServerTable. Ele se refere ao serviço vinculado do SQL Server criado na etapa anterior. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa para se conectar à sua instância do SQL Server no runtime. Esse conjunto de dados especifica a tabela SQL no banco de dados que contém os dados. Neste tutorial, a tabela **emp** contém os dados de origem.

1. Crie um arquivo JSON denominado *SqlServerDataset.json* na pasta *C:\ADFv2Tutorial* com o seguinte código:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Para criar o conjunto de dados SqlServerDataset, execute o cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Criar um conjunto de dados para o Armazenamento de Blobs do Azure (coletor)
Nesta etapa, você define um conjunto de dados que representa os dados que serão copiados para o Armazenamento de Blobs do Azure. O conjunto de dados é do tipo AzureBlob. Ele se refere ao serviço vinculado do Armazenamento do Azure criado anteriormente neste tutorial.

O serviço vinculado tem as informações de conexão que o serviço do data factory usa no runtime para se conectar à sua conta de Armazenamento do Azure. Esse conjunto de dados especifica a pasta no armazenamento do Azure para a qual os dados são copiados do banco de dados do SQL Server. Neste tutorial, a pasta é *adftutorial/fromonprem*, em que `adftutorial` é o contêiner de blob e `fromonprem` é a pasta.

1. Crie um arquivo JSON denominado *AzureBlobDataset.json* na pasta *C:\ADFv2Tutorial* com o seguinte código:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Para criar o conjunto de dados AzureBlobDataset, execute o cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta tutorial, você cria um pipeline com uma atividade de cópia. A atividade de cópia usa o SqlServerDataset como o conjunto de dados de entrada e o AzureBlobDataset como o conjunto de dados de saída. O tipo de fonte está definido como *SqlSource*, e o tipo de coletor está definido como *BlobSink*.

1. Crie um arquivo JSON denominado *SqlServerToBlobPipeline.json* na pasta *C:\ADFv2Tutorial* com o seguinte código:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Para criar o pipeline SQLServerToBlobPipeline, execute o cmdlet `Set-AzDataFactoryV2Pipeline`.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Veja o exemplo de saída:

    ```console
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Inicie a execução de um pipeline para o pipeline SQLServerToBlobPipeline e capture a ID da execução de pipeline para monitoramento futuro.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Para verificar continuamente o status de execução do pipeline SQLServerToBlobPipeline, execute o script a seguir no PowerShell e imprima o resultado final:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Aqui está a saída da execução de exemplo:

    ```console
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Você pode obter a ID da execução de pipeline SQLServerToBlobPipeline e verificar o resultado detalhado da execução de atividade executando o seguinte comando:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Aqui está a saída da execução de exemplo:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída chamada *fromonprem* no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo *dbo.emp.txt* na pasta de saída.

1. No portal do Azure, na janela de contêiner **adftutorial**, selecione **Atualizar** para ver a pasta de saída.
1. Selecione `fromonprem` na lista de pastas.
1. Confirme que você vê um arquivo com o nome `dbo.emp.txt`.

    ![Arquivo de saída](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline nesse exemplo copia dados de uma localização para outra no Armazenamento de Blobs do Azure. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um runtime de integração auto-hospedada.
> * Criar serviços vinculados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e de Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorar a execução de pipeline.

Para obter uma lista dos armazenamentos de dados com suporte pelo Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy.md)