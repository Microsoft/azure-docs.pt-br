---
title: Copiar dados do SQL Server para o Armazenamento de Blobs usando o portal do Azure
description: Saiba como copiar dados de um armazenamento de dados local para a nuvem usando o runtime de integração auto-hospedada no Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4bfbd83f3f3910e1231bcce4043d9b59ccc512db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606643"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Copiar dados de um banco de dados do SQL Server para o Armazenamento de Blobs do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você usa a interface do usuário do Azure Data Factory para criar um pipeline de data factory que copia dados de um banco de dados do SQL Server para o Armazenamento de Blobs do Azure. Você cria e usa um runtime de integração auto-hospedada, o qual move dados entre locais e armazenamentos de dados da nuvem.

> [!NOTE]
> Este artigo não fornece uma introdução detalhada ao Data Factory. Para saber mais, confira [Introdução ao Data Factory](introduction.md).

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

Para exibir as permissões que você tem na assinatura, acesse o portal do Azure. Selecione seu nome de usuário no canto superior direito e depois selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server como um armazenamento de dados de *origem*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server (origem) para um Armazenamento de Blobs (coletor). Depois você cria uma tabela chamada **emp** no seu banco de dados do SQL Server e insere algumas entradas de exemplo na tabela.

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Conecte-se à sua instância do SQL Server usando suas credenciais.

1. Crie um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**.
1. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**.

1. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

   ```
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
Neste tutorial, você usa uma conta de armazenamento do Azure para fins gerais (especificamente o Armazenamento de blobs) como armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de uso geral do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server (origem) para um Armazenamento de Blobs (coletor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Use o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, realize as etapas a seguir:

1. Entre no [Portal do Azure](https://portal.azure.com) com seu nome de usuário e senha do Azure.

1. No painel esquerdo, selecione **Todos os serviços**. Filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisa na conta de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento, se necessário. Em seguida, selecione sua conta de armazenamento.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial.

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu Armazenamento de blobs.

1. Na janela **Conta de armazenamento**, vá para **Visão geral** e depois selecione **Contêineres**.

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Contêineres**, selecione **+ Contêiner** para criar um contêiner.

1. Na janela **Novo contêiner**, em **Nome**, digite **adftutorial**. Em seguida, selecione **Criar**.

1. Na lista de contêineres, selecione **adftutorial**, que você acabou de criar.

1. Mantenha aberta a janela **contêiner** para **adftutorial**. Você a usa para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, selecione **Criar um recurso** > **Integração** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do data factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Mensagem de erro para o nome duplicado do novo data factory.":::

1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.

   - Selecione **Criar novo** e insira o nome de um grupo de recursos.
        
     Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Local**, selecione o local para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo Data Factory podem estar em outras regiões.
1. Selecione **Criar**.

1. Depois que a criação estiver concluída, a página **Data Factory** será exibida conforme mostrado na imagem:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::
1. Selecione o bloco **Criar e Monitorar** para iniciar a interface do usuário do Data Factory em uma guia separada.


## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página **Introdução**, selecione **Criar pipeline**. Um pipeline é criado automaticamente para você. Você vê o pipeline no modo de exibição de árvore e o editor é aberto.

   ![Página Introdução](./media/doc-common-process/get-started-page.png)

1. No painel Geral, em **Propriedades**, especifique **SQLServerToBlobPipeline** para **Nome**. Em seguida, recolha o painel clicando no ícone Propriedades no canto superior direito.

1. Na caixa de ferramentas **Atividades**, expanda **Mover e transformar**. Arraste e solte a atividade de **Cópia** na superfície de design do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

1. Na janela **Propriedades**, vá para a guia **Origem** e selecione **+ Novo**.

1. Na caixa de diálogo **Novo Conjunto de Dados**, procure **SQL Server**. Selecione **SQL Server** e selecione **Continuar**.
    ![Novo conjunto de dados do SqlServer](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. Na caixa de diálogo **Definir Propriedades**, em **Nome**, insira **SqlServerDataset**. Em **Serviço vinculado**, selecione **+ Novo**. Você pode criar uma conexão com o repositório de dados de origem (banco de dados do SQL Server) nesta etapa.

1. Na caixa de diálogo **Novo Serviço Vinculado**, adicione **Nome** como **SqlServerLinkedService**. Em **Conectar por meio do runtime de integração**, selecione **+Novo**.  Nesta seção, você cria um Integration Runtime auto-hospedado e o associa a um computador local com o banco de dados do SQL Server. O runtime de integração auto-hospedada é o componente que copia dados do banco de dados do SQL Server em seu computador para o armazenamento de Blobs.

1. Na caixa de diálogo **Instalação do Integration Runtime**, selecione **Auto-hospedado** e clique em **Continuar**.

1. Em nome, insira **TutorialIntegrationRuntime**. Em seguida, selecione **Criar**.

1. Em Configurações, selecione **Clique aqui para iniciar a instalação expressa para este computador**. Essa ação instala o Integration Runtime em seu computador e o registra com o Data Factory. Como alternativa, você pode usar a opção de baixar o arquivo de instalação manual, executá-lo e usar a chave para registrar o runtime de integração.
    ![Configuração do Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. Na janela **Instalação Expressa do Integration Runtime (auto-hospedado)** , selecione **Fechar** após a conclusão do processo.

    ![Instalação expressa do runtime de integração (auto-hospedado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Na caixa de diálogo **Novo serviço vinculado (SQL Server)** , verifique se **TutorialIntegrationRuntime** está selecionado em **Conectar por meio do Integration Runtime**. Em seguida, execute as etapas a seguir:

    a. Em **Nome**, insira **SqlServerLinkedService**.

    b. Em **Nome do servidor**, insira o nome da instância do SQL Server.

    c. Em **Nome do banco de dados**, insira o nome do banco de dados com a tabela **emp**.

    d. Em **tipo de autenticação**, selecione o tipo de autenticação adequado que o Data Factory deve usar para se conectar ao seu banco de dados do SQL Server.

    e. Em **Nome de usuário** e **Senha**, digite o nome de usuário e a senha. Use *meudomínio\\meuusuário* como o nome de usuário, se necessário.

    f. Selecione **Testar conexão**. Esta etapa serve para confirmar que o Data Factory pode se conectar ao banco de dados do SQL Server usando o runtime de integração auto-hospedada criado por você.

    g. Para salvar o serviço vinculado, selecione **Criar**.
 
    ![Novo serviço vinculado (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Depois que o serviço vinculado for criado, você voltará para a página **Definir propriedades** para o SqlServerDataset. Siga estas etapas:

    a. Em **Serviço vinculado**, confirme que você vê **SqlServerLinkedService** como Serviço vinculado.

    b. Em **Nome da tabela**, selecione **[dbo].[emp]** .
    
    c. Selecione **OK**.

1. Vá para a guia com **SQLServerToBlobPipeline**, ou selecione **SQLServerToBlobPipeline** no modo de exibição de árvore.

1. Vá para a guia **Coletor** na parte inferior da janela **Propriedades** e selecione **+ Novo**.

1. Na caixa de diálogo **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure**. Depois selecione **Continuar**.

1. Na caixa de diálogo **Selecionar formato**, escolha o tipo de formato dos seus dados. Depois selecione **Continuar**.

    ![Seleção de formato de dados](./media/doc-common-process/select-data-format.png)

1. Na caixa de diálogo **Definir Propriedades**, insira **AzureBlobDataset** como o Nome. Selecione **+ Novo** ao lado da caixa de texto **Serviço vinculado**.

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , insira **AzureStorageLinkedService** como o nome e selecione sua conta de armazenamento na lista de nomes da **Conta de armazenamento**. Teste a conexão e, em seguida, selecione **Criar** para implantar o serviço vinculado.

1. Depois que o serviço vinculado for criado, você voltará para a página **Definir propriedades**. Selecione **OK**.

1. Abra o conjunto de dados do coletor. Na guia **Conexão**, siga estas etapas:

    a. Confirme se **AzureStorageLinkedService** está selecionado como **Serviço vinculado**.

    b. No **Caminho do arquivo**, insira **adftutorial/fromonprem** para a parte de **Contêiner/Diretório**. Se a pasta de saída não existir no contêiner adftutorial, o serviço Data Factory criará essa pasta automaticamente.

    c. Para a parte de **Arquivo**, selecione **Adicionar conteúdo dinâmico**.
    ![expressão dinâmica para resolução de nomes de arquivo](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adicione `@CONCAT(pipeline().RunId, '.txt')` e, em seguida, **Concluir**. Essa ação renomeará o arquivo para PipelineRunID.txt.

1. Alterne para a guia com o pipeline aberto ou selecione o pipeline no modo de exibição de árvore. Confirme se **AzureBlobDataset** está selecionado no **Conjunto de Dados do Coletor**.

1. Para validar as configurações de pipeline, selecione **Validar** na barra de ferramentas do pipeline. Para fechar a **Saída de validação de pipe**, selecione o ícone **>>** .
    ![validar o pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Para publicar as entidades criadas no Data Factory, selecione **Publicar tudo**.

1. Aguarde até que você veja o pop-up **Publicação concluída**. Para verificar o status da publicação, selecione o link **Mostrar notificações** na parte superior da janela. Para fechar a janela de notificação, selecione **Fechar**.


## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline
Selecione **Adicionar Gatilho** na barra de ferramentas do pipeline e selecione **Disparar Agora**.

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Vá para a guia **Monitorar**. Você vê o pipeline que disparou manualmente na etapa anterior.

1. Para exibir as execuções de atividade associadas à execução do pipeline, selecione o link **SQLServerToBlobPipeline** em *NOME DO PIPELINE*. 
    ![Monitorar execuções de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Na página **Execuções de atividade**, selecione os Detalhes (imagem de óculos) para ver detalhes sobre a operação de cópia. Para voltar à exibição Execuções de Pipeline, selecione **Todas as execuções de pipeline** na parte superior.

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída chamada *fromonprem* no contêiner de blobs `adftutorial`. Confirme que você vê o arquivo *[pipeline().RunId].txt* na pasta de saída.


## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de um local para outro no Armazenamento de blobs. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um runtime de integração auto-hospedada.
> * Criar serviços vinculados de armazenamento e do SQL Server.
> * Criar conjuntos de dados do SQL Server e de armazenamento de blobs.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorar a execução de pipeline.

Para obter uma lista dos armazenamentos de dados com suporte do Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para aprender a copiar dados em massa de uma origem para um destino, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)