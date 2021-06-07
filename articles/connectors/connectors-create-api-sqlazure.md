---
title: Conectar-se a SQL Server, banco de dados SQL do Azure ou SQL do Azure Instância Gerenciada
description: Automatizar tarefas para bancos de dados SQL locais ou na nuvem usando Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 75c657236b6e06a7e0f6c717d746bcc8c034d423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013440"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para um banco de dados SQL usando aplicativos lógicos do Azure

Este artigo mostra como você pode acessar dados no banco de dados SQL de dentro de um aplicativo lógico com o conector do SQL Server. Dessa forma, você pode automatizar tarefas, processos ou fluxos de trabalho que gerenciam dados e recursos SQL com a criação de aplicativos lógicos. O conector do SQL Server funciona para [SQL Server](/sql/sql-server/sql-server-technical-documentation) , bem como o [banco de dados SQL do azure](../azure-sql/database/sql-database-paas-overview.md) e o [instância gerenciada do Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Você pode criar Aplicativos Lógicos que são executados quando disparados por eventos no Banco de Dados SQL ou em outros sistemas, como Dynamics CRM Online. Os aplicativos lógicos também podem obter, inserir e excluir dados, além de executar consultas SQL e procedimentos armazenados. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros Dynamics CRM Online, adiciona itens ao Banco de Dados SQL para novos registros e envia alertas de email sobre os itens adicionados.

Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, limitações e problemas conhecidos, confira a [página de referência do conector do SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [banco de dados SQL Server](/sql/relational-databases/databases/create-a-database), [banco de dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md)ou [instância gerenciada SQL do Azure](../azure-sql/managed-instance/instance-create-quickstart.md).

  As tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você usar o banco de dados SQL do Azure, poderá usar bancos de dados de exemplo que estão incluídos.

* O nome do SQL Server, o nome do banco de dados, o nome de usuário e a senha. Você precisará dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server.

  * Para SQL Server locais, você pode encontrar esses detalhes na cadeia de conexão:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para o banco de dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão.
  
    Por exemplo, para localizar essa cadeia de caracteres no portal do Azure, abra o banco de dados. No menu banco de dados, selecione **cadeias de conexão** ou **Propriedades**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Com base em se seus aplicativos lógicos serão executados no Azure de vários locatários ou em um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), aqui estão outros requisitos para se conectar ao SQL Server local:

  * Para aplicativos lógicos no Azure de multilocatário global que se conectam ao SQL Server local, você precisa ter o [Gateway de dados](../logic-apps/logic-apps-gateway-install.md) local instalado em um computador local e um recurso de [Gateway de dados que já foi criado no Azure](../logic-apps/logic-apps-gateway-connection.md).

  * Para aplicativos lógicos em um ISE que se conectam ao SQL Server local e usam a autenticação do Windows, o conector de SQL Server com controle de versão do ISE não dá suporte à autenticação do Windows. Portanto, você ainda precisa usar o gateway de dados e o conector de SQL Server não ISE. Para outros tipos de autenticação, você não precisa usar o gateway de dados e pode usar o conector com controle de versão do ISE.

* O aplicativo lógico em que você precisa de acesso ao banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Agora, continue com estas etapas:

* [Conectar-se ao banco de dados SQL do Azure baseado em nuvem ou Instância Gerenciada](#connect-azure-sql-db)
* [Conectar-se ao SQL Server local](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Conectar-se ao banco de dados SQL do Azure ou Instância Gerenciada

Para acessar um Instância Gerenciada do SQL do Azure sem usar o gateway de dados local ou o ambiente do serviço de integração, você precisa [Configurar o ponto de extremidade público no instância gerenciada do SQL do Azure](../azure-sql/managed-instance/public-endpoint-configure.md). O ponto de extremidade público usa a porta 3342, portanto, certifique-se de especificar esse número de porta ao criar a conexão de seu aplicativo lógico.


Na primeira vez que você adicionar um [gatilho SQL](#add-sql-trigger) ou uma [ação SQL](#add-sql-action)e ainda não tiver criado uma conexão com seu banco de dados, você será solicitado a concluir estas etapas:

1. Para **tipo de autenticação**, selecione a autenticação necessária e habilitada no banco de dados no banco de dados SQL do Azure ou azure SQL instância gerenciada:

   | Autenticação | Descrição |
   |----------------|-------------|
   | [**Integrado do Azure AD**](../azure-sql/database/authentication-aad-overview.md) | -Dá suporte ao conector de SQL Server não ISE e ISE. <p><p>-Requer uma identidade válida no Azure Active Directory (Azure AD) que tenha acesso ao seu banco de dados. <p>Para saber mais, consulte esses tópicos: <p>- [Visão geral da segurança do SQL do Azure-autenticação](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizar o acesso ao banco de dados no SQL Azure-autenticação e autorização](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL-autenticação integrada do Azure AD](../azure-sql/database/authentication-aad-overview.md) |
   | [**Autenticação do SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Dá suporte ao conector de SQL Server não ISE e ISE. <p><p>-Requer um nome de usuário válido e uma senha forte que são criados e armazenados em seu banco de dados. <p>Para saber mais, consulte esses tópicos: <p>- [Visão geral da segurança do SQL do Azure-autenticação](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizar o acesso ao banco de dados no SQL Azure-autenticação e autorização](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Este exemplo continua com o **Azure ad integrado**:

   ![Captura de tela que mostra a janela de conexão "SQL Server" com a lista "tipo de autenticação" aberta e "Azure AD integrado" selecionado.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Depois de selecionar o **Azure ad integrado**, selecione **entrar**. Com base em se você usa o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada, selecione suas credenciais de usuário para autenticação.

1. Selecione esses valores para seu banco de dados:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do servidor** | Sim | O endereço do SQL Server, por exemplo, `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome do banco de dados** | Sim | O nome do banco de dados SQL, por exemplo, `Fabrikam-Azure-SQL-DB` |
   | **Nome da tabela** | Sim | A tabela que você deseja usar, por exemplo, `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Para fornecer suas informações de banco de dados e de tabela, você tem estas opções:
   > 
   > * Localize essas informações na cadeia de conexão do seu banco de dados. Por exemplo, no portal do Azure, localize e abra seu banco de dados. No menu banco de dados, selecione **cadeias de conexão** ou **Propriedades**, onde você pode encontrar essa cadeia de caracteres:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Por padrão, as tabelas em bancos de dados do sistema são filtradas, portanto, elas podem não aparecer automaticamente quando você seleciona um banco de dados do sistema. Como alternativa, você pode inserir manualmente o nome da tabela depois de selecionar **Inserir valor personalizado** na lista banco de dados.
   >

   Este exemplo mostra como esses valores podem parecer:

   ![Criar conexão com o banco de dados SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Agora, continue com as etapas que você ainda não concluiu em [Adicionar um gatilho SQL](#add-sql-trigger) ou [Adicionar uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Conectar-se ao SQL Server local

Na primeira vez que você adicionar um [gatilho SQL](#add-sql-trigger) ou uma [ação SQL](#add-sql-action)e ainda não tiver criado uma conexão com seu banco de dados, você será solicitado a concluir estas etapas:

1. Para conexões com o SQL Server local que exigem o gateway de dados local, verifique se você [concluiu esses pré-requisitos](#multi-tenant-or-ise).

   Caso contrário, o recurso de gateway de dados não aparecerá na lista de **Gateway de conexão** quando você criar a conexão.

1. Para **tipo de autenticação**, selecione a autenticação necessária e habilitada no seu SQL Server:

   | Autenticação | Descrição |
   |----------------|-------------|
   | [**Autenticação do Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -Dá suporte apenas ao conector de SQL Server não ISE, que requer um recurso de gateway de dados criado anteriormente no Azure para sua conexão, independentemente de você usar o Azure multilocatário ou um ISE. <p><p>-Requer um nome de usuário e senha válidos do Windows para confirmar sua identidade por meio de sua conta do Windows. <p>Para obter mais informações, consulte [autenticação do Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**Autenticação do SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Dá suporte ao conector de SQL Server não ISE e ISE. <p><p>-Requer um nome de usuário válido e uma senha forte que são criados e armazenados em seu SQL Server. <p>Para obter mais informações, consulte [SQL Server autenticação](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Este exemplo continua com a **autenticação do Windows**:

   ![Selecione o tipo de autenticação a ser usado](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selecione ou forneça os seguintes valores para o banco de dados SQL:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do SQL Server** | Sim | O endereço do SQL Server, por exemplo, `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome do Banco de Dados SQL** | Sim | O nome para seu banco de dados SQL Server, por exemplo, `Fabrikam-Azure-SQL-DB` |
   | **Nome de usuário** | Sim | Seu nome de usuário para o SQL Server e o banco de dados |
   | **Senha** | Sim | Sua senha para o SQL Server e o banco de dados |
   | **Assinatura** |  Sim, para autenticação do Windows | A assinatura do Azure para o recurso de gateway de dados que você criou anteriormente no Azure |
   | **Gateway de conexão** | Sim, para autenticação do Windows | O nome do recurso de gateway de dados que você criou anteriormente no Azure <p><p>**Dica**: se o gateway não aparecer na lista, verifique se você [configurou corretamente seu gateway](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > Você pode encontrar essas informações na cadeia de conexão do seu banco de dados:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Este exemplo mostra como esses valores podem parecer:

   ![Criação de SQL Server conexão concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando estiver pronto, selecione **Criar**.

1. Agora, continue com as etapas que você ainda não concluiu em [Adicionar um gatilho SQL](#add-sql-trigger) ou [Adicionar uma ação SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicionar um gatilho SQL

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico. Este exemplo continua com o portal do Azure.

1. No designer, na caixa de pesquisa, digite `sql server` . Na lista de gatilhos, selecione o gatilho SQL desejado. Este exemplo usa o gatilho **quando um item é criado** .

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se você estiver se conectando ao banco de dados SQL pela primeira vez, você será solicitado a [criar sua conexão de banco de dados SQL agora](#create-connection). Depois de criar essa conexão, você pode continuar com a próxima etapa.

1. No gatilho, especifique o intervalo e a frequência para a frequência com que o gatilho verifica a tabela.

1. Para adicionar outras propriedades disponíveis para esse gatilho, abra a lista **Adicionar novo parâmetro** .

   Esse gatilho retorna apenas uma linha da tabela selecionada e nada mais. Para executar outras tarefas, continue adicionando uma ação do [conector do SQL](#add-sql-action) ou [outra ação](../connectors/apis-list.md) que execute a próxima tarefa que você deseja em seu fluxo de trabalho do aplicativo lógico.

   Por exemplo, para ver os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e, em seguida, enviar alertas por email. Para saber mais sobre outras ações disponíveis para esse conector, confira a [página de referência do conector](/connectors/sql/).

1. Selecione **Salvar** na barra de ferramentas do designer.

   Embora essa etapa habilite e publique automaticamente seu aplicativo lógico em tempo real no Azure, a única ação que seu aplicativo lógico usa atualmente é verificar seu banco de dados com base em seu intervalo e frequência especificados.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Disparar mudança e descompasso de recorrência

Gatilhos baseados em conexão onde você precisa criar uma conexão primeiro, como o gatilho SQL, diferem de gatilhos internos que são executados nativamente em aplicativos lógicos do Azure, como o [gatilho de recorrência](../connectors/connectors-native-recurrence.md). Em gatilhos baseados em conexão recorrentes, o agendamento de recorrência não é o único driver que controla a execução, e o fuso horário só determina a hora de início inicial. As execuções subsequentes dependem da agenda de recorrência, da última execução do gatilho *e* de outros fatores que podem causar tempos de execução para descompassor ou produzir um comportamento inesperado, por exemplo, não manter a agenda especificada quando o horário de verão é iniciado e encerrado. Para garantir que a hora da recorrência não mude quando o DST entrar em vigor, ajuste manualmente a recorrência para que seu aplicativo lógico continue a ser executado no tempo esperado. Caso contrário, a hora de início mudará uma hora para frente quando o horário de verão for iniciado e uma hora para trás quando o horário de verão terminar. Para obter mais informações, consulte [recorrência para gatilhos baseados em conexão](../connectors/apis-list.md#recurrence-connection-based).

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicionar uma ação SQL

Neste exemplo, o aplicativo lógico é iniciado com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md) e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico. Este exemplo continua a portal do Azure.

1. No gatilho ou na ação em que você deseja adicionar a ação SQL, selecione **Nova etapa**.

   ![Adicionar uma ação ao seu aplicativo lógico](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Ou, para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `sql server`. Na lista de ações, selecione a ação SQL que desejar. Este exemplo usa a ação **Obter linha**, que obtém um registro único.

   ![Selecione a ação SQL "obter linha"](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Se você estiver se conectando ao banco de dados SQL pela primeira vez, você será solicitado a [criar sua conexão de banco de dados SQL agora](#create-connection). Depois de criar essa conexão, você pode continuar com a próxima etapa.

1. Selecione o **nome da tabela**, que está `SalesLT.Customer` neste exemplo. Insira a **ID de linha** para o registro desejado.

   ![Selecione o nome da tabela e especifique a ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Esta ação retorna apenas uma linha da tabela selecionada, nada mais. Portanto, para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e armazena esse arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para esse conector, confira a [página de referência do conector](/connectors/sql/).

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Lidar com os dados em massa

Às vezes, você tem que trabalhar com conjuntos de resultados tão grandes que o conector não retorna todos os resultados ao mesmo tempo ou você deseja um controle melhor sobre o tamanho e a estrutura dos conjuntos de resultados. Você pode lidar com esses grandes conjuntos de resultados das seguintes maneiras:

* Para ajudar você a gerenciar os resultados como conjuntos menores, ative a *paginação*. Para obter mais informações, confira [Obter dados em massa, registros e itens usando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). Para obter mais informações, consulte [paginação SQL para transferência de dados em massa com aplicativos lógicos](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Crie um [*procedimento armazenado*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que organize os resultados da maneira desejada. O conector do SQL fornece muitos recursos de back-end que você pode acessar usando o aplicativo lógico do Azure para que você possa automatizar com mais facilidade as tarefas comerciais que funcionam com tabelas do banco de dados SQL.

  Ao obter ou inserir várias linhas, o aplicativo lógico pode iterar por essas linhas usando um [*loop until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando o aplicativo lógico precisa trabalhar com conjuntos de registros muito grandes, como milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira que deseja, você pode criar um procedimento armazenado que é executado na instância SQL e usa a instrução **SELECT – ORDER BY**. Essa solução fornece a você mais controle sobre o tamanho e a estrutura de seus resultados. Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector do SQL Server. Para obter mais informações, consulte [cláusula SELECT-order by](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > O conector do SQL tem um limite de tempo limite de procedimento armazenado [inferior a 2 minutos](/connectors/sql/#known-issues-and-limitations). Alguns procedimentos armazenados podem levar mais tempo do que esse limite para ser concluído, causando um `504 Timeout` erro. Você pode contornar esse problema usando um gatilho de conclusão SQL, uma consulta de passagem SQL nativa, uma tabela de estado e trabalhos do lado do servidor.
  > 
  > Para essa tarefa, você pode usar o [agente de trabalho elástico do Azure](../azure-sql/database/elastic-jobs-overview.md) para o [banco de dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md). Para [SQL Server local](/sql/sql-server/sql-server-technical-documentation) e o [SQL instância gerenciada do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), você pode usar o [SQL Server Agent](/sql/ssms/agent/sql-server-agent). Para saber mais, confira [tratar tempos limite de procedimentos armazenados de execução longa no conector do SQL para aplicativos lógicos do Azure](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Manipular dados em massa dinâmicos

Quando você chama um procedimento armazenado usando o conector de SQL Server, a saída retornada, às vezes, é dinâmica. Nesse cenário, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Exiba o formato de saída executando uma execução de teste. Copie e salve a saída de exemplo.

1. No designer, na ação em que você chama o procedimento armazenado, selecione **Nova etapa**.

1. Em **escolher uma ação**, localize e selecione a ação [**analisar JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .

1. Na ação **Analisar JSON**, selecione a opção **Usar carga de amostra para gerar esquema**.

1. Na caixa **Inserir ou colar um conteúdo JSON de exemplo** , Cole a saída de exemplo e selecione **concluído**.

   > [!NOTE]
   > Se você receber um erro informando que os Aplicativos Lógicos não podem gerar um esquema, verifique se a sintaxe da saída de exemplo está formatada corretamente. Se você ainda não conseguir gerar o esquema, na caixa **esquema** , insira manualmente o esquema.

1. Selecione **Salvar** na barra de ferramentas do designer.

1. Para fazer referência às propriedades de conteúdo JSON, clique dentro das caixas de edição nas quais você deseja fazer referência a essas propriedades para que a lista de conteúdo dinâmico seja exibida. Na lista, no cabeçalho [**analisar JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) , selecione os tokens de dados para as propriedades de conteúdo JSON que você deseja.

## <a name="troubleshoot-problems"></a>Solução de problemas

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Problemas de conexão

Problemas de conexão podem acontecer normalmente, para solucionar problemas e resolver esses tipos de problemas, consulte [Resolvendo erros de conectividade para SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Estes são alguns exemplos:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, as ações e os limites desse conector, consulte a [página de referência do conector](/connectors/sql/), que é gerada a partir da descrição do Swagger.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [outros conectores para Aplicativos Lógicos do Azure](../connectors/apis-list.md)
