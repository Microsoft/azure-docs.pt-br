---
title: Copiar dados do armazenamento de Blobs do Azure para o SQL usando a ferramenta Copiar Dados
description: Crie um Azure Data Factory e use a ferramenta Copiar Dados para copiar dados do Armazenamento de Blobs do Azure para um Banco de Dados SQL.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1540e088565f69ca6d923202ad9b32b8d4ccf0ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584417"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copie dados do Armazenamento de Blobs do Azure para um Banco de Dados SQL usando a ferramenta Copiar Dados

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, você usará a ferramenta Copiar Dados para criar um pipeline que copia dados do Armazenamento de Blobs do Azure para um Banco de Dados SQL.

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:
> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o armazenamento de Blobs como um armazenamento de dados de _origem_. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).
* **Banco de Dados SQL do Azure**: Use um Banco de Dados SQL como o armazenamento de dados _coletor_. Se você não tiver um Banco de Dados SQL, confira as instruções em [Criar um Banco de Dados SQL](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Prepare o Armazenamento de Blobs e o Banco de Dados SQL para o tutorial executando estas etapas.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o **Bloco de notas**. Copie o texto a seguir e salve-o em um arquivo chamado **inputEmp.txt** no disco:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Crie um contêiner chamado **adfv2tutorial** e carregue o arquivo inputEmp.txt no contêiner. É possível usar o portal do Azure ou várias ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para executar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o seguinte script SQL para criar uma tabela chamada **dbo.emp** no Banco de Dados SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir que serviços e recursos do Azure acessem este servidor** está habilitada para o servidor que executa o Banco de Dados SQL. Essa configuração permite que o Data Factory grave dados em sua instância de banco de dados. Para confirmar e ativar essa configuração, acesse servidor SQL lógico > Firewalls e redes virtuais > defina a opção **Permitir que serviços e recursos do Azure acessem este servidor** como **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Integração** > **Data Factory**:

    ![Criação do novo data factory](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

    O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Mensagem de erro para o nome duplicado do novo data factory.":::

    Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
1. Selecione a **assinatura** do Azure na qual deseja criar o novo data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo** e insira o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).

1. Em **versão**, selecione **V2** para a versão.
1. Em **local**, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo seu data factory podem estar em outros locais e regiões.
1. Selecione **Criar**.

1. Depois de finalizada a criação, a home page do **Data Factory** é exibida.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page do Azure Data Factory com o bloco Criar e Monitorar.":::
1. Para iniciar a interface do usuário do Azure Data Factory em uma guia separada, selecione o bloco **Criar e Monitorar**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data.

    ![Bloco Ferramenta Copy Data](./media/doc-common-process/get-started-page.png)
1. Na página **Propriedades**, em **Nome da tarefa**, insira **CopyFromBlobToSqlPipeline**. Em seguida, selecione **Avançar**. A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa.
    ![Criar uma pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Continuar**.

    c. Na página **Novo Serviço Vinculado**, selecione a assinatura do Azure e a conta de armazenamento na lista **Nome da conta de armazenamento**. Teste a conexão e, em seguida, selecione **Concluir**.

    d. Selecione o serviço vinculado recém-criado como fonte, depois clique **Avançar**.

    ![Selecione o serviço de origem vinculado](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

    a. Clique em **Navegar** para navegar até a pasta **adfv2tutorial/input**, selecione o arquivo **inputEmp.txt** e então clique em **Escolher**.

    b. Clique em **Avançar** para mover-se para a próxima etapa.

1. Na página **Configurações de formato de arquivo**, habilite a caixa de seleção *Primeira linha como cabeçalho*. Observe que a ferramenta detecta automaticamente os delimitadores de coluna e de linha. Selecione **Avançar**. Visualize também os dados e veja o esquema dos dados de entrada nesta página.

    ![Configurações de formato de arquivo](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na página **Armazenamento de dados de destino**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão

    b. Selecione **Banco de Dados SQL do Azure** por meio da galeria e, em seguida, selecione **Continuar**.

    c. Na página **Novo Serviço Vinculado**, selecione o nome do servidor e o nome do BD na lista suspensa, especifique o nome de usuário e a senha e, em seguida, selecione **Criar**.

    ![Configure o Banco de Dados SQL do Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecione o serviço vinculado criado recentemente como coletor e clique em **Avançar**.

1. Na página **Mapeamento de tabela**, selecione a tabela **[dbo].[emp]** e clique em **Avançar**.

1. Na página **Mapeamento de coluna**, observe que a segunda e a terceira colunas do arquivo de entrada são mapeadas para as colunas **FirstName** e **LastName** da tabela **emp**. Ajuste o mapeamento para garantir que não haja nenhum erro e, em seguida, selecione **Avançar**.

    ![Página Mapeamento de coluna](./media/tutorial-copy-data-tool/column-mapping.png)

1. Na página **Configurações**, selecione **Avançar**.

1. Na página **Resumo**, analise as configurações e selecione **Avançar**.

1. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Monitorar o pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)
    
1. Na página Execuções de pipeline, selecione **Atualizar** para atualizar a lista. Clique no link em **NOME DO PIPELINE** para ver os detalhes da execução de atividade ou execute o pipeline novamente. 
    ![Execução de pipeline](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Na página Execuções de atividade, selecione o link **Detalhes** (ícone de óculos) na coluna **NOME DA ATIVIDADE** para obter mais detalhes sobre a operação de cópia. Para voltar à exibição Execuções de Pipeline, selecione o link **TODAS as execuções de pipeline** no menu de navegação estrutural. Para atualizar a exibição, selecione **Atualizar**.

    ![Monitorar execuções de atividade](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Verifique se os dados foram inseridos na tabela **dbo.emp** no Banco de Dados SQL.

1. Selecione a guia **Criar** à esquerda para alternar para o modo de edição. É possível atualizar os serviços vinculados, os conjuntos de dados e os pipelines criados com a ferramenta usando o editor. Para obter detalhes sobre essas entidades na IU do Data Factory, confira em [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

    ![Selecionar a guia Autor](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Próximas etapas
O pipeline desta amostra copia dados do Armazenamento de Blobs para um Banco de Dados SQL. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre como copiar dados do local para a nuvem:

>[!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-data-tool.md)
