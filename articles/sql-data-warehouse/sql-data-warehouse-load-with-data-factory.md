---
title: "Carregar dados no SQL Data Warehouse do Azure – Data Factory | Microsoft Docs"
description: Este tutorial carrega dados no SQL Data Warehouse do Azure usando o Azure Data Factory e usa um banco de dados SQL Server como a fonte de dados.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Carregar dados no SQL Data Warehouse com o Data Factory

Você pode usar o Azure Data Factory para carregar dados no SQL Data Warehouse do Azure de qualquer um dos [repositórios de dados de origem com suporte](../data-factory/copy-activity-overview.md). Por exemplo, é possível carregar dados de um Banco de Dados SQL do Azure ou um Banco de Dados Oracle em um SQL Data Warehouse usando o Data Factory. O tutorial neste artigo mostra como carregar dados de um banco de dados local do SQL Server em um SQL Data Warehouse.

**Tempo estimado**: este tutorial levará cerca de 10 a 15 minutos para ser concluído depois que os pré-requisitos forem atendidos.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de um **Banco de Dados do SQL Server** com tabelas que contêm os dados a serem copiados para o SQL Data Warehouse.  

- Você precisa de um **SQL Data Warehouse** online. Se você ainda não tiver um data warehouse, saiba como [Criar um SQL Data Warehouse do Azure](sql-data-warehouse-get-started-provision.md).

- Você precisa de uma **Conta de Armazenamento do Azure**. Se você ainda não tem uma conta de armazenamento, aprenda como [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Para melhor desempenho, localize a conta de armazenamento e o data warehouse na mesma região do Azure.

## <a name="configure-a-data-factory"></a>Configurar uma fábrica de dados
1. Faça logon no [Portal do Azure][].
2. Localize seu data warehouse e clique para abri-lo.
3. Na folha principal, clique em **Carregar Dados** > **Azure Data Factory**.

    ![Iniciar o assistente para Carregar Dados](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Se você não tem uma fábrica de dados na sua assinatura do Azure, você verá uma caixa de diálogo **Nova fábrica de dados** em uma guia separada do navegador. Preencha as informações solicitadas e clique em **Criar**. Depois que a fábrica de dados for criada, a caixa de diálogo **Nova fábrica de dados** será fechada e você verá a caixa de diálogo **Selecionar fábrica de dados**.

    Se você já tem uma ou mais fábricas de dados na assinatura do Azure, você verá a caixa de diálogo **Selecionar fábrica de dados**. Nessa caixa de diálogo é possível selecionar uma fábrica de dados existente ou clicar em **Criar nova fábrica de dados** para criar uma nova.

    ![Configurar o Data Factory](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. Na caixa de diálogo **Selecionar Data Factory**, a opção **Carregar dados** é selecionada por padrão. Clique em **Avançar** para começar a criar uma tarefa de carregamento de dados.

## <a name="configure-the-data-factory-properties"></a>Configurar as propriedades do data factory
Agora que você criou um data factory, a próxima etapa é configurar o agendamento de carregamento de dados.

1. Em **Nome da tarefa**, digite **DWLoadData-fromSQLServer**.
2. Use a opção padrão **Executar uma vez agora** e clique em **Avançar**.

    ![Configurar o agendamento de carregamento](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Configurar o gateway e o repositório de dados de origem
Agora você informa o Data Factory sobre o banco de dados SQL Server local do qual você deseja carregar os dados.

1. Escolha **SQL Server** no catálogo de armazenamento de dados de origem com suporte e clique em **Avançar**.

    ![Escolher a fonte do SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. Uma caixa de diálogo **Especificar o banco de dados SQL Server local** será exibida. O primeiro campo **Nome da conexão** é preenchido automaticamente. O segundo campo solicita o nome do **Gateway**. Se estiver usando uma fábrica de dados existente que já tenha um gateway, você poderá reutilizar o gateway, selecionando-o na lista suspensa. Clique no link **Criar Gateway** para criar um Gateway de Gerenciamento de Dados.  

    > [!NOTE]
    > Se o armazenamento de dados de origem for local ou estiver em uma máquina virtual do Azure IaaS, será necessário usar um gateway de gerenciamento de dados. Um gateway tem uma relação de 1-1 com uma fábrica de dados. Ele não pode ser usado de outra data factory, mas pode ser usado por várias tarefas de carregamento de dados na mesma fábrica de dados. Um gateway pode ser usado para se conectar a vários repositórios de dados durante a execução de tarefas de carregamento de dados.
    >
    > Para obter informações detalhadas sobre o gateway, consulte o artigo [Gateway de Gerenciamento de Dados](../data-factory/v1/data-factory-data-management-gateway.md).

3. Uma caixa de diálogo **Criar Gateway** será exibida. Em Nome, insira **GatewayForDWLoading** e clique em **Criar**.

4. Uma caixa de diálogo **Configurar Gateway** será exibida. Clique em **Iniciar a instalação expressa neste computador** para baixar, instalar e registrar automaticamente o Gateway de Gerenciamento de Dados no computador atual. O progresso é mostrado em uma janela pop-up. Se o computador não puder se conectar ao armazenamento de dados, você poderá [baixar e instalar o gateway](https://www.microsoft.com/download/details.aspx?id=39717) manualmente em um computador que possa se conectar ao armazenamento de dados e, em seguida, usar a chave para registrar.
    > [!NOTE]
    > A instalação expressa funciona nativamente com o Microsoft Edge e o Internet Explorer. Se você estiver usando o Google Chrome, primeiro instale a extensão ClickOnce na loja na Web do Chrome.

    ![Iniciar a instalação Expressa](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Aguarde a conclusão da instalação do gateway. Depois que o gateway for registrado com êxito e estiver online, a janela pop-up será fechada e o novo gateway será exibido no campo do gateway. Em seguida, preencha o restante dos campos obrigatórios como se segue e clique em **Avançar**.
    - **Nome do servidor**: nome do SQL Server local.
    - **Nome do banco de dados**: banco de dados SQL Server.
    - **Criptografia de credencial**: use o padrão "Pelo navegador da Web".
    - **Tipo de autenticação**: escolha o tipo de autenticação que está sendo usado.
    - **Nome de usuário** e **senha**: insira o nome de usuário e a senha de um usuário que tem permissão para copiar os dados.

    ![Iniciar a instalação Expressa](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. A próxima etapa é escolher as tabelas das quais os dados serão copiados. Filtre as tabelas usando palavras-chave. Além disso, você pode visualizar o esquema de dados e tabela no painel inferior. Depois de concluir a seleção, clique em **Avançar**.

    ![Selecionar tabelas](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configurar o destino, o SQL Data Warehouse

Agora você informa ao Data Factory sobre as informações de destino.

1. As informações de conexão do SQL Data Warehouse são preenchidas automaticamente. Insira a senha para o nome de usuário. e clique em **Avançar**.

    ![Configurar o destino](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. É exibido um mapeamento de tabela inteligente, que mapeia tabelas de origem para tabelas de destino com base em nomes de tabelas. Se a tabela não existir no destino, o ADF criará uma por padrão com o mesmo nome (isso se aplica ao SQL Server ou ao Banco de Dados SQL do Azure como fonte). Também é possível mapear para uma tabela existente. Examine e clique em **Avançar**.

    ![Mapear tabelas](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Examine o mapeamento de esquema e procure mensagens de erro ou de aviso. O mapeamento inteligente é baseado no nome da coluna. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro juntamente com a tabela correspondente. Se você escolher permitir que o Data Factory crie automaticamente as tabelas, a conversão apropriada de tipo de dados poderá ocorrer se for necessário corrigir a incompatibilidade entre os repositórios de origem e destino.

    ![Mapear esquema](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Clique em **Avançar**.

## <a name="configure-the-performance-settings"></a>Definir as configurações de desempenho
Nas configurações de Desempenho, configure uma conta de armazenamento do Azure usada para preparar os dados antes de carregá-los no de forma definitiva no SQL Data Warehouse usando o [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Depois que a cópia for concluída, os dados provisórios no armazenamento serão limpos automaticamente.

Selecione uma conta de armazenamento do Azure existente e clique em **Avançar**.

![Configurar um blob de preparo](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Examinar as informações de resumo e implantar o pipeline

Examine a configuração e clique no botão **Concluir** para implantar o pipeline.

![Implantar o data factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Monitorar o progresso do carregamento de dados

Você pode ver o progresso e os resultados da implantação na página **Implantação**.

1. Quando a implantação estiver concluída, clique no link **Clique aqui para monitorar o pipeline de cópia** para monitorar o progresso do carregamento de dados.

    ![Monitorar o pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. O pipeline de carregamento de dados recém-criado **DWLoadData-fromSQLServer** é automaticamente selecionado no **Gerenciador de Recursos** à esquerda.

    ![Exibir o pipeline](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Clique no pipeline, no painel do meio, para ver o status detalhado de cada tabela mapeada para uma Atividade.

    ![Exibir a atividade da tabela](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Continue clicando em uma atividade e você verá os detalhes do carregamento de dados no painel direito, incluindo tamanho dos dados, linhas, taxa de transferência, etc.

    ![Exibir os detalhes da atividade da tabela](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Para iniciar esta exibição de monitoramento mais tarde, vá para o SQL Data Warehouse, clique em **Carregar Dados > Azure Data Factory**, selecione seu factory e escolha **Monitorar as tarefas de carregamento existentes**.

## <a name="next-steps"></a>Próximas etapas

Para migrar seu banco de dados para o SQL Data Warehouse, consulte [Visão geral da migração](sql-data-warehouse-overview-migrate.md).

Para saber mais sobre o Azure Data Factory e seus recursos de movimentação de dados, consulte os seguintes artigos:

- [Introdução ao Azure Data Factory](../data-factory/introduction.md)
- [Mover dados usando a Atividade de Cópia](../data-factory/copy-activity-overview.md)
- [Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Para explorar seus dados no SQL Data Warehouse, consulte os seguintes artigos:

- [Conectar-se ao SQL Data Warehouse com o Visual Studio e o SSDT](sql-data-warehouse-query-visual-studio.md)
- [Dados visuais com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Portal do Azure]: https://portal.azure.com
