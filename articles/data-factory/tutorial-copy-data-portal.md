---
title: Usar o portal do Azure para criar um pipeline do data factory
description: Este tutorial fornece instruções passo a passo para usar o portal do Azure a fim de criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 08ab3c9181f4fdcfc9b55bf80c53f88ff26f90ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584374"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados do Armazenamento de Blobs do Azure para um banco de dados no Banco de Dados SQL do Azure usando o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. O pipeline neste data factory copia dados do Armazenamento de Blobs do Azure para um banco de dados no Banco de Dados SQL do Azure. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) a fim de conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um banco de dados no Banco de Dados SQL do Azure, confira [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) para ver as etapas para a criação de um.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu armazenamento de blobs e o banco de dados SQL para o tutorial executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um contêiner chamado **adftutorial** no seu Armazenamento de blobs. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o seguinte script SQL para criar a tabela **dbo.emp** em seu banco de dados:

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

1. Permita que os serviços do Azure acessem o SQL Server. Verifique se a opção **Permitir acesso aos serviços do Azure** está **ATIVADA** para o SQL Server, para que o Data Factory possa gravar dados no SQL Server. Para verificar e ativar essa configuração, vá até um servidor lógico do SQL > Visão geral > Configurar o firewall do servidor > defina a opção **Permitir acesso aos serviços do Azure** para **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **Criar um recurso** > **Integração** > **Data Factory**.
3. Na página **Criar Data Factory**, na guia **Informações Básicas**, selecione a **Assinatura** do Azure na qual deseja criar o data factory.
4. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione um grupo de recursos existente na lista suspensa.

    b. Escolha **Criar** e insira o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
5. Em **Região**, escolha uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.
6. Em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Mensagem de erro para o nome duplicado do novo data factory.":::

7. Em **Versão**, selecione **V2**.
8. Escolha a guia **Configuração do Git** na parte superior e marque a caixa de seleção **Configurar o Git mais tarde**.
9. Selecione **Examinar + criar** e escolha **Criar** depois que a validação for aprovada.
10. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **Ir para o recurso** para navegar até a página do Data Factory.
11. Selecione **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.


## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você criará um pipeline com a atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de blobs para o Banco de Dados SQL. No [Tutorial de início rápido](quickstart-create-data-factory-portal.md), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado.
1. Criar conjuntos de dados de entrada e saída.
1. Crie um pipeline.

Neste tutorial, inicie com a criação do pipeline. Em seguida, crie conjuntos de dados e serviços vinculados quando forem necessários para configurar o pipeline.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Criar um pipeline](./media/doc-common-process/get-started-page.png)

1. No painel geral, em **Propriedades**, especifique **CopyPipeline** para **Nome**. Em seguida, recolha o painel clicando no ícone Propriedades no canto superior direito.

1. Na caixa de ferramentas de **Atividades**, expanda a categoria **Mover e transformar** e arraste e solte a atividade **Copiar dados** da caixa de ferramentas para a superfície do designer do pipeline. Especifique **CopyFromBlobToSql** para o **Nome**.

    ![Atividade de cópia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar fonte

>[!TIP]
>Neste tutorial, você usa *Chave de conta* como o tipo de autenticação para o armazenamento de dados de origem, mas você pode escolher outros métodos de autenticação compatíveis: *URI de SAS*, *Entidade de Serviço* e *Identidade Gerenciada* se necessário. Veja as seções correspondentes [neste artigo](./connector-azure-blob-storage.md#linked-service-properties) para obter detalhes.
>Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](./store-credentials-in-key-vault.md) para obter ilustrações detalhadas.

1. Vá para a guia **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **Novo conjunto de dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Continuar**. Os dados de origem estão em um Armazenamento de blobs e, portanto, você deve selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Selecionar formato**, escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**.

1. Na caixa de diálogo **Definir propriedades**, insira **SourceBlobDataset** como o nome. Marque a caixa de seleção de **Primeira linha como cabeçalho**. Na caixa de texto **Serviço vinculado**, selecione **+ Novo**.

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , insira **AzureStorageLinkedService** como o nome e selecione sua conta de armazenamento na lista **Nome da conta de armazenamento**. Teste a conexão e selecione **Criar** para implantar o serviço vinculado.

1. Depois que o serviço vinculado for criado, ele será redirecionado de volta para a página **Definir propriedades**. Ao lado de **Caminho do arquivo**, selecione **Procurar**.

1. Navegue até a pasta **adftutorial/input**, selecione o arquivo **emp.txt** e, em seguida, selecione **OK**.

1. Selecione **OK**. Ele navega automaticamente para a página do pipeline. Na guia **Origem**, confirme se **SourceBlobDataset** está selecionado. Para visualizar os dados da página, selecione **Visualizar dados**.

    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurar coletor
>[!TIP]
>Neste tutorial, você usa *Autenticação de SQL* como o tipo de autenticação para o armazenamento de dados coletor, mas você pode escolher outros métodos de autenticação compatíveis: *Entidade de Serviço* e *Identidade Gerenciada* se necessário. Veja as seções correspondentes [neste artigo](./connector-azure-sql-database.md#linked-service-properties) para obter detalhes.
>Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](./store-credentials-in-key-vault.md) para obter ilustrações detalhadas.

1. Alterne para a guia **Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.

1. Na caixa de diálogo **Novo conjunto de dados**, insira "SQL" na caixa de pesquisa para filtrar os conectores, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Continuar**. Neste tutorial, você copia dados para um banco de dados SQL.

1. Na caixa de diálogo **Definir propriedades**, insira **OutputSqlDataset** como o nome. Na lista suspensa **Serviço vinculado**, selecione **+ Novo**. Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o Data Factory usa para se conectar ao Banco de Dados SQL no runtime. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados.

1. Na caixa de diálogo **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as etapas a seguir:

    a. Em **Nome**, insira **AzureSqlDatabaseLinkedService**.

    b. Em **Nome do servidor**, selecione sua instância do SQL Server.

    c. Em **Nome do banco de dados**, selecione seu banco de dados.

    d. Em **Nome de usuário**, insira o nome do usuário.

    e. Em **Senha**, insira a senha do usuário.

    f. Selecione **Testar conectividade** para testar a conexão.

    g. Selecione **Criar** para implantar o serviço vinculado.

    ![Salvar o novo serviço vinculado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Ele navega automaticamente para a caixa de diálogo **Definir propriedades**. Em **Tabela**, selecione **[dbo].[emp]** . Depois, selecione **OK**.

1. Alterne para a guia com o pipeline e, em **Conjunto de Dados do Coletor**, confirme se **OutputSqlDataset** está selecionado.

    ![Guia Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Opcionalmente, é possível mapear o esquema da origem para o esquema de destino correspondente seguindo [Mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Validar o pipeline
Selecione **Validar** na barra de ferramentas para validar as configurações de pipeline.

Você pode ver o código JSON associado ao pipeline clicando em **Código**, no canto superior direito.

## <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline
Você pode depurar um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory ou em seu próprio repositório Git do Azure Repos.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela.

1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.

1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique na guia **Mostrar Notificações** no canto superior direito (botão de sino).

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior.

1. Selecione **Gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de pipeline**, selecione **OK**.  

1. Vá para a guia **Monitorar** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **PIPELINE NAME** para ver detalhes da atividade e executar o pipeline novamente.

    [![Monitorar execuções de pipeline](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Para ver as execuções de atividade associadas à execução do pipeline, selecione o link **CopyPipeline** na coluna **PIPELINE NAME**. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **ACTIVITY NAME**. Selecione **Todas as execuções de pipeline** na parte superior para voltar à exibição Execuções de Pipeline. Para atualizar a exibição, selecione **Atualizar**.

    [![Monitorar execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Verifique se mais duas linhas são adicionadas à tabela **emp** no banco de dados.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Nesse cronograma, você criará um gatilho de agenda para o pipeline. O gatilho dispara o pipeline conforme a agenda especificada, como por hora ou por dia. Aqui você definirá o gatilho para executar a cada minuto até o datetime de término especificado.

1. Vá até a guia **Autor** à esquerda acima da guia Monitorar.

1. Vá até o pipeline, clique em **Gatilho** na barra de ferramentas e selecione **Novo/Editar**.

1. Na caixa de diálogo **Adicionar gatilhos**, selecione **+ Novo** na área **Escolher gatilho**.

1. Na janela **Novo Gatilho**, siga estas etapas:

    a. Em **Nome**, digite **RunEveryMinute**.

    b. Atualize a **Data de início** do gatilho. Se a data for anterior ao datetime atual, o gatilho começará a entrar em vigor quando a alteração for publicada. 

    c. Em **Fuso horário**, selecione a lista suspensa.

    d. Defina a **Recorrência** como **A cada Minuto**.

    e. Marque a caixa de seleção **Especificar uma data de término** e atualize o componente **Término em** de modo que ele tenha alguns minutos após o datetime atual. O gatilho só é ativado depois de você publicar as alterações. Se você a definir com apenas alguns minutos de diferença e não publicar até a hora especificada, nenhum gatilho será executado.

    f. Para a opção **Ativado**, selecione **Sim**.

    g. Selecione **OK**.

    > [!IMPORTANT]
    > Há um custo associado a cada execução de pipeline e, portanto, defina a data de término adequadamente.

1. Na página **Editar gatilho**, examine o aviso e selecione **Salvar**. O pipeline neste exemplo não usa parâmetros.

1. Clique em **Publicar tudo** para publicar a alteração.

1. Vá para a guia **Monitor** à esquerda para ver as execuções de pipeline disparadas.

    [![Execuções de pipeline disparadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Para alternar do modo de exibição **Execuções de Pipeline** para **Execuções de Gatilho**, selecione **Execuções de Gatilho** no lado esquerdo da janela.

1. As execuções de gatilho serão exibidas em uma lista.

1. Verifique se duas linhas por minuto (para cada execução de pipeline) são inseridas na tabela **emp** até a hora de término especificada.

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.


Avance para o tutorial a seguir para saber mais sobre como copiar dados do local para a nuvem:

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-portal.md)
