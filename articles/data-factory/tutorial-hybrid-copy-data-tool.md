---
title: Copiar dados locais usando a ferramenta de Copiar Dados do Azure
description: Crie um Azure Data Factory e use a ferramenta Copiar Dados para copiar dados de um banco de dados do SQL Server para o Armazenamento de Blobs do Azure.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/26/2021
ms.openlocfilehash: 70905d23b61ee9fee319bae41aeb421d808fa4d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566737"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copie dados de um banco de dados do SQL Server para um Armazenamento de Blobs do Azure usando a ferramenta Copiar Dados
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, use a ferramenta Copiar Dados para criar um pipeline que copia dados de um banco de dados do SQL Server para um Armazenamento de Blobs do Azure.

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para fazer logon no Azure deve ter uma função *Colaborador* ou *Proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure.

Para exibir as permissões que você tem na assinatura, acesse o portal do Azure. Selecione seu nome de usuário no canto superior direito, depois selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server como um armazenamento de dados de *origem*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server (origem) para um Armazenamento de Blobs (coletor). Depois você cria uma tabela chamada **emp** no seu banco de dados do SQL Server e insere algumas entradas de exemplo na tabela.

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
Neste tutorial, você usa uma conta de armazenamento do Azure para fins gerais (especificamente o Armazenamento de blobs) como armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de fins gerais, confira [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para obter instruções sobre como criar uma. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server (origem) para esse Armazenamento de Blobs (coletor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Use o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, realize as etapas a seguir:

1. Entre no [Portal do Azure](https://portal.azure.com) com seu nome de usuário e senha do Azure.

1. No painel esquerdo, selecione **Todos os serviços**. Filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisa na conta de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento, se necessário. Em seguida, selecione sua conta de armazenamento.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.


1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial.

## <a name="create-a-data-factory"></a>Criar um data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Integração** > **Data Factory**.

   ![Criação do novo data factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do data factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Mensagem de erro para o nome duplicado do novo data factory.":::
1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.

   - Selecione **Criar novo** e insira o nome de um grupo de recursos. 
        
     Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Local**, selecione o local para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo Data Factory podem estar em outros locais/regiões.
1. Selecione **Criar**.

1. Depois de finalizada a criação, a página **Data Factory** será exibida conforme mostrado na imagem.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::
1. Selecione **Criar e Monitorar** para iniciar a interface do usuário Data Factory em uma guia separada.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usar a ferramenta Copy Data para criar um pipeline

1. Na página **Introdução**, selecione **Copy Data** para iniciar a ferramenta Copy Data.

   ![Página Introdução](./media/doc-common-process/get-started-page.png)

1. Na página **Propriedades** da ferramenta Copy Data, em **Nome da tarefa**, insira **CopyFromOnPremSqlToAzureBlobPipeline**. Em seguida, selecione **Avançar**. A ferramenta Copy Data cria um pipeline com o nome especificado para este campo.
  ![Nome da tarefa](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na página **Armazenamento de dados de origem**, clique em **Criar nova conexão**.

1. Em **Novo serviço vinculado**, procure **SQL Server** e, em seguida, selecione **Continuar**.

1. Na caixa de diálogo **Novo serviço vinculado (SQL Server)** , em **Nome**, insira **SqlServerLinkedService**. Selecione **+Novo** em **Conectar por meio do runtime de integração**. Você deve criar um runtime de integração auto-hospedada, baixá-lo para seu computador e registrá-lo com o Data Factory. O runtime de integração auto-hospedada copia dados entre seu ambiente local e a nuvem.

1. Na caixa de diálogo **Instalação do Integration Runtime**, selecione **Auto-hospedado**. Depois selecione **Continuar**.

   ![Criar runtime de integração](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. Na caixa de diálogo **Instalação do Integration Runtime**, em **Nome**, insira **TutorialIntegrationRuntime**. Em seguida, selecione **Criar**.

1. Na caixa de diálogo **Instalação do Integration Runtime**, selecione **Clique aqui para iniciar a instalação expressa para este computador**. Essa ação instala o Integration Runtime em seu computador e o registra com o Data Factory. Como alternativa, você pode usar a opção de baixar o arquivo de instalação manual, executá-lo e usar a chave para registrar o runtime de integração.

1. Execute o aplicativo baixado. Você verá o status da instalação rápida na janela.

    ![Status da instalação rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Na caixa de diálogo **Novo Serviço Vinculado (SQL Server)** , verifique se **TutorialIntegrationRuntime** está selecionado para o campo Tempo de Execução de Integração. Em seguida, execute as etapas a seguir:

    a. Em **Nome**, insira **SqlServerLinkedService**.

    b. Em **Nome do servidor**, insira o nome da instância do SQL Server.

    c. Em **Nome do banco de dados**, insira o nome do seu banco de dados local.

    d. Em **Tipo de autenticação**, selecione a autenticação adequada.

    e. Em **Nome de usuário**, insira o nome de usuário com acesso ao SQL Server.

    f. Insira a **senha** para o usuário.

    g. Teste a conexão e selecione **Concluir**.

      ![runtime de integração selecionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Na página **Armazenamento de dados de origem**, selecione **Avançar**.

1. Na página **Selecionar tabelas das quais copiar os dados ou usar uma consulta personalizada**, selecione a tabela **[dbo].[emp]** na lista e selecione **Avançar**. Você pode selecionar qualquer outra tabela com base em seu banco de dados.

1. Na página **Armazenamento de dados de destino**, selecione **Criar nova conexão**


1. Em **Novo serviço vinculado**, procure e selecione **Blobs do Azure** e depois **Continuar**.

   ![Seleção de armazenamento de blobs](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , execute as seguintes etapas:

   a. Em **Nome**, insira **AzureStorageLinkedService**.

   b. Em **Conectar por meio de tempo de execução de integração**, selecione **TutorialIntegrationRuntime**

   c. Em **Nome da conta de armazenamento**, selecione sua conta de armazenamento na lista suspensa.

   d. Selecione **Concluir**.

1. Na caixa de diálogo **Armazenamento de dados de destino**, verifique se o **Armazenamento de Blobs do Azure** está selecionado. Em seguida, selecione **Avançar**.

1. Na caixa de diálogo **Escolha o arquivo ou a pasta de saída**, em **Caminho da pasta**, insira **adftutorial/fromonprem**. Você criou o contêiner **adftutorial** como parte dos pré-requisitos. Se a pasta de saída não existir (neste caso **fromonprem**), o Data Factory a cria automaticamente. Também é possível usar o botão **Procurar** para navegar no armazenamento de blobs e seus contêineres/pastas. Se você não especificar nenhum valor em **Nome de arquivo**, por padrão o nome da fonte será usado (neste caso **dbo.emp**).

   ![Escolha o arquivo ou a pasta de saída](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Na caixa de diálogo **Configurações de formato de arquivo**, selecione **Avançar**.

1. Na caixa de diálogo **Configurações**, selecione **Avançar**.

1. Na caixa de diálogo **Resumo**, revise os valores para todas as configurações e selecione **Avançar**.

1. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa). 

1. Quando a execução do pipeline for concluída, será possível exibir o status do pipeline que você criou. 

1. Na página Execuções de pipeline, selecione **Atualizar** para atualizar a lista. Clique no link em **NOME DO PIPELINE** para ver os detalhes da execução de atividade ou execute o pipeline novamente. 

1. Na página Execuções de atividade, selecione o link **Detalhes** (ícone de óculos) na coluna **NOME DA ATIVIDADE** para obter mais detalhes sobre a operação de cópia. Para voltar à exibição Execuções de Pipeline, selecione o link **TODAS as execuções de pipeline** no menu de navegação estrutural. Para atualizar a exibição, selecione **Atualizar**.

1. Confira se existe o arquivo de saída na pasta **fromonprem** do contêiner **adftutorial**.

1. Selecione a guia **Editar** à esquerda para alternar para o modo de edição. É possível atualizar serviços vinculados, conjuntos de dados e pipelines criados pela ferramenta usando o editor. Selecione **Código** para exibir o código JSON associado à entidade aberta no editor. Para obter detalhes sobre como editar essas entidades na interface do usuário do Data Factory, confira [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).


## <a name="next-steps"></a>Próximas etapas
O pipeline deste exemplo copia dados de um banco de dados do SQL Server para um Armazenamento de Blobs. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

Para obter uma lista dos armazenamentos de dados com suporte do Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre como copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
