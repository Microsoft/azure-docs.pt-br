---
title: Usar pontos de extremidade privados para criar um pipeline do Azure Data Factory
description: Este tutorial fornece instruções passo a passo para usar o portal do Azure a fim de criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados do Armazenamento de Blobs do Azure para um Banco de Dados SQL do Azure.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.author: jingwang
ms.openlocfilehash: 191ad61990e10fdb718eebf1a8f57d8edaadcf35
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515440"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copiar dados com segurança do Armazenamento de Blobs do Azure para um Banco de Dados SQL usando pontos de extremidade privados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. *O pipeline deste data factory copia os dados com segurança do Armazenamento de Blobs do Azure para um Banco de Dados SQL do Azure (permitindo o acesso somente às redes selecionadas) usando pontos de extremidade privados na [Rede Virtual Gerenciada do Azure Data Factory](managed-virtual-network-private-endpoint.md).* O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados compatíveis como origens e coletores, confira a tabela de [Formatos e armazenamentos de dados compatíveis](./copy-activity-overview.md).

> [!NOTE]
> Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](./introduction.md).

Neste tutorial, você executa as seguintes etapas:

* Criar um data factory.
* Criar um pipeline com uma atividade de cópia.


## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) a fim de conhecer as etapas para criar uma. *Verifique se a conta de armazenamento permite acesso somente de redes selecionadas.* 
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um Banco de Dados SQL do Azure, confira [Criar um banco de dados SQL](../azure-sql/database/single-database-create-quickstart.md) para saber as etapas para criar um. *Verifique se a conta do Banco de Dados SQL permite acesso somente de redes selecionadas.* 

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare o Armazenamento de Blobs e o Banco de Dados SQL para o tutorial executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Abra o Bloco de Notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um contêiner chamado **adftutorial** no seu Armazenamento de Blobs. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

Use o script SQL a seguir para criar a tabela **dbo.emp** em seu banco de dados SQL:

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

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o Microsoft Edge ou Google Chrome. Atualmente apenas os navegadores da Web Microsoft Edge e Google Chrome são compatíveis com a interface do usuário do Data Factory.

1. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**.

1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](./naming-rules.md).

1. Selecione a **assinatura** do Azure na qual deseja criar o data factory.

1. Em **Grupo de Recursos**, use uma das seguintes etapas:

    - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.
    - Selecione **Criar novo** e insira o nome de um grupo de recursos. 
     
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 

1. Em **Versão**, selecione **V2**.

1. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte aparecem na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.

1. Selecione **Criar**.

1. Depois que a criação for concluída, você verá o aviso no Centro de notificações. Selecione **Ir para o recurso** para ir até a página do **Data Factory**.

1. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Criar um Azure Integration Runtime na Rede Virtual Gerenciada do Data Factory
Nesta etapa, você criará um Azure Integration Runtime e habilitará a Rede Virtual Gerenciada do Data Factory.

1. No portal do Data Factory, acesse **Gerenciar** e selecione **Novo** para criar um Azure Integration Runtime.

   ![Captura de tela que mostra a criação de um Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Na página **Instalação do runtime de integração**, escolha o runtime de integração a ser criado com base nas funcionalidades necessárias. Neste tutorial, selecione **Azure, Auto-hospedado** e clique em **Continuar**. 
1. Selecione **Azure** e clique em **Continuar** para criar um Azure Integration Runtime.

   ![Captura de tela que mostra um novo Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. Em **Configuração de rede virtual (versão prévia)** , selecione **Habilitar**.

   ![Captura de tela que mostra a habilitação de um novo Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Selecione **Criar**.

## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você criará um pipeline com a atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de blobs para o Banco de Dados SQL. No [Tutorial de início rápido](./quickstart-create-data-factory-portal.md), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado.
1. Criar conjuntos de dados de entrada e saída.
1. Crie um pipeline.

Neste tutorial, você começa pela criação de um pipeline. Em seguida, crie conjuntos de dados e serviços vinculados quando forem necessários para configurar o pipeline.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Captura de tela que mostra a criação de um pipeline.](./media/doc-common-process/get-started-page.png)
1. No painel de propriedades do pipeline, insira **CopyPipeline** como o nome do pipeline.

1. Na caixa de ferramentas **Atividades**, expanda a categoria **Mover e Transformar** e arraste a atividade **Copiar dados** da caixa de ferramentas até a superfície do designer do pipeline. Insira **CopyFromBlobToSql** como o nome.

    ![Captura de tela que mostra a atividade de cópia.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Configurar uma origem

>[!TIP]
>Neste tutorial, você usa **Chave de conta** como o tipo de autenticação para o armazenamento de dados de origem. Você também pode escolher outros métodos de autenticação compatíveis, como **URI de SAS**, **Entidade de Serviço** e **Identidade Gerenciada** se necessário. Para obter mais informações, confira as seções correspondentes em [Copiar e transformar dados no Armazenamento de Blobs do Azure usando o Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Para armazenar segredos de armazenamentos de dados com segurança, também recomendamos que você use um Azure Key Vault. Para obter mais informações e ilustrações, confira [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Criar um conjunto de dados de origem e um serviço vinculado

1. Vá para a guia **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **Novo conjunto de dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Continuar**. Os dados de origem estão em um Armazenamento de blobs e, portanto, você deve selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Selecionar Formato**, selecione o tipo de formato dos seus dados e escolha **Continuar**.

1. Na caixa de diálogo **Definir Propriedades**, insira **SourceBlobDataset** como o **Nome**. Marque a caixa de seleção de **Primeira linha como cabeçalho**. Na caixa de texto **Serviço vinculado**, selecione **+ Novo**.

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , insira **AzureStorageLinkedService** como **Nome** e selecione sua conta de armazenamento na lista **Nome de conta de armazenamento**. 

1. Habilite a **Criação interativa**. Pode levar cerca de um minuto para habilitar essa opção.

    ![Captura de tela que mostra a Criação interativa.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selecione **Testar conexão**. Ela deverá falhar quando a conta de armazenamento permitir acesso somente de **Redes selecionadas** e exigir que o Data Factory crie um ponto de extremidade privado para ela, que deverá ser aprovado antes do uso. Na mensagem de erro, você verá um link para criar um ponto de extremidade privado que poderá seguir para criar um ponto de extremidade privado gerenciado. Uma alternativa é acessar diretamente a guia **Gerenciar** e seguir as instruções da [próxima seção](#create-a-managed-private-endpoint) para criar um ponto de extremidade privado gerenciado.

   > [!NOTE]
   > É possível que a guia **Gerenciar** não fique disponível para todas as instâncias do data factory. Se você não a vir, acesse os pontos de extremidade privados selecionando **Criação** > **Conexões** > **Ponto de Extremidade Privado**.
1. Mantenha a caixa de diálogo aberta e acesse sua conta de armazenamento.

1. Siga as instruções [desta seção](#approval-of-a-private-link-in-a-storage-account) para aprovar o link privado.

1. Volte à caixa de diálogo. Selecione novamente **Testar conectividade** e selecione **Criar** para implantar o serviço vinculado.

1. Depois que o serviço vinculado for criado, você será levado de volta para a página **Definir propriedades**. Ao lado de **Caminho do arquivo**, selecione **Procurar**.

1. Acesse a pasta **adftutorial/input**, selecione o arquivo **emp.txt** e clique em **OK**.

1. Selecione **OK**. Isso navegará automaticamente para a página do pipeline. Na guia **Origem**, confirme se **SourceBlobDataset** está selecionado. Para visualizar os dados da página, selecione **Visualizar dados**.

    ![Captura de tela que mostra o conjunto de dados de origem.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Se você não selecionou o hiperlink ao testar a conexão, siga o caminho. Agora você precisará criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado que você criou.

1. Acesse a guia **Gerenciar**.

   > [!NOTE]
   > É possível que a guia **Gerenciar** não fique disponível para todas as instâncias do Data Factory. Se você não a vir, acesse os pontos de extremidade privados selecionando **Criação** > **Conexões** > **Ponto de Extremidade Privado**.

1. Acesse a seção **Pontos de extremidade privados** gerenciados.

1. Selecione **+ Novo** em **Pontos de extremidade privados gerenciados**.

    ![Captura de tela que mostra o novo botão Pontos de extremidade privados gerenciados.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o bloco do **Armazenamento de Blobs do Azure** na lista e selecione **Continuar**.

1. Insira o nome da conta de armazenamento criada.

1. Selecione **Criar**.

1. Depois de alguns segundos, você verá que o link privado criado precisa de uma aprovação.

1. Selecione o ponto de extremidade privado que você criou. Você verá um hiperlink que vai levar você à aprovação do ponto de extremidade privado no nível da conta de armazenamento.

    ![Captura de tela que mostra o painel Ponto de extremidade privado gerenciado.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Aprovação de um link privado em uma conta de armazenamento
1. Na conta de armazenamento, acesse **Conexões do ponto de extremidade privado** na seção **Configurações**.

1. Marque a caixa de seleção do ponto de extremidade privado que você criou e selecione **Aprovar**.

    ![Captura de tela que mostra o botão Aprovar do ponto de extremidade privado.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adicione uma descrição e selecione **sim**.
1. Volte à seção **Pontos de extremidade privados gerenciados** da guia **Gerenciar** no Data Factory.
1. Após um ou dois minutos, você verá a aprovação do ponto de extremidade privado aparecer na interface do usuário do Data Factory.


### <a name="configure-a-sink"></a>Configurar um coletor
>[!TIP]
>Neste tutorial, você usará a **autenticação do SQL** como o tipo de autenticação para o armazenamento de dados do coletor. Você também pode escolher outros métodos de autenticação compatíveis, como **Entidade de Serviço** e **Identidade Gerenciada** se necessário. Para obter mais informações, confira as seções correspondentes em [Copiar e transformar dados no Banco de Dados SQL do Azure usando o Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Para armazenar segredos de armazenamentos de dados com segurança, também recomendamos que você use um Azure Key Vault. Para obter mais informações e ilustrações, confira [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Criar um conjunto de dados de coletor e um serviço vinculado
1. Alterne para a guia **Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.

1. Na caixa de diálogo **Novo Conjunto de Dados**, digite **SQL** na caixa de pesquisa para filtrar os conectores. Selecione **Banco de Dados SQL do Azure** e **Continuar**. Neste tutorial, você copia dados para um banco de dados SQL.

1. Na caixa de diálogo **Definir Propriedades**, insira **OutputSqlDataset** como o **Nome**. Na lista suspensa **Serviço vinculado**, selecione **+ Novo**. Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o Data Factory usa para se conectar ao banco de dados SQL no runtime. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados.

1. Na caixa de diálogo **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as seguintes etapas:

    1. Em **Nome**, insira **AzureSqlDatabaseLinkedService**.
    1. Em **Nome do servidor**, selecione sua instância do SQL Server.
    1. Habilite a **Criação interativa**.
    1. Em **Nome do banco de dados**, selecione seu banco de dados SQL.
    1. Em **Nome de usuário**, insira o nome do usuário.
    1. Em **Senha**, insira a senha do usuário.
    1. Selecione **Testar conexão**. Ela deverá falhar, porque o SQL Server permite o acesso somente de **Redes selecionadas** e exige que o Data Factory crie um ponto de extremidade privado para ela, que deverá ser aprovado antes do uso. Na mensagem de erro, você verá um link para criar um ponto de extremidade privado que poderá seguir para criar um ponto de extremidade privado gerenciado. Uma alternativa é acessar diretamente a guia **Gerenciar** e seguir as instruções da próxima seção para criar um ponto de extremidade privado gerenciado.
    1. Mantenha a caixa de diálogo aberta e acesse o SQL Server selecionado.
    1. Siga as instruções [desta seção](#approval-of-a-private-link-in-sql-server) para aprovar o link privado.
    1. Volte à caixa de diálogo. Selecione novamente **Testar conectividade** e selecione **Criar** para implantar o serviço vinculado.

1. Isso ocasionará a navegação automática até a caixa de diálogo **Definir propriedades**. Em **Tabela**, selecione **[dbo].[emp]** . Depois, selecione **OK**.

1. Acesse a guia com o pipeline e, em **Conjunto de dados do Coletor**, confirme se **OutputSqlDataset** está selecionado.

    ![Captura de tela que mostra a guia Pipeline.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Opcionalmente, você pode mapear o esquema da origem para o esquema de destino correspondente seguindo [Mapeamento de esquema na atividade de cópia](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Se você não selecionou o hiperlink ao testar a conexão, siga o caminho. Agora você precisará criar um ponto de extremidade privado gerenciado que será conectado ao serviço vinculado que você criou.

1. Acesse a guia **Gerenciar**.
1. Acesse a seção **Pontos de extremidade privados** gerenciados.
1. Selecione **+ Novo** em **Pontos de extremidade privados gerenciados**.

    ![Captura de tela que mostra o novo botão Pontos de extremidade privados gerenciados.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecione o bloco do **Banco de Dados SQL do Azure** na lista e escolha **Continuar**.
1. Insira o nome do SQL Server que você selecionou.
1. Selecione **Criar**.
1. Depois de alguns segundos, você verá que o link privado criado precisa de uma aprovação.
1. Selecione o ponto de extremidade privado que você criou. Você verá um hiperlink que vai levar você à aprovação do ponto de extremidade privado no nível do SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Aprovação de um link privado no SQL Server
1. No SQL Server, acesse **Conexões do ponto de extremidade privado** na seção **Configurações**.
1. Marque a caixa de seleção do ponto de extremidade privado que você criou e selecione **Aprovar**.
1. Adicione uma descrição e selecione **sim**.
1. Volte à seção **Pontos de extremidade privados gerenciados** da guia **Gerenciar** no Data Factory.
1. Deve levar um ou dois minutos para que a aprovação seja exibida para seu ponto de extremidade privado.

#### <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline

Você pode depurar um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory ou em seu próprio repositório Git do Azure Repos.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela.
1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) que você criou para o Data Factory.
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, selecione **Mostrar Notificações** no canto superior direito (botão de sino).


#### <a name="summary"></a>Resumo
O pipeline deste exemplo copia dados do Armazenamento de Blobs para o Banco de Dados SQL usando pontos de extremidade privados na Rede Virtual Gerenciada do Data Factory. Você aprendeu a:

* Criar um data factory.
* Criar um pipeline com uma atividade de cópia.