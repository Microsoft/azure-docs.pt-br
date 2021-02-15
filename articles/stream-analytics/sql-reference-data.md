---
title: Usar dados de referência de Banco de Dados SQL em um trabalho do Azure Stream Analytics
description: Este artigo descreve como usar um Banco de Dados SQL como entrada de dados de referência para um trabalho do Azure Stream Analytics no portal do Azure e no Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016398"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Usar dados de referência de um Banco de Dados SQL para um trabalho do Azure Stream Analytics

O Azure Stream Analytics dá suporte ao Banco de Dados SQL do Azure como uma fonte de entrada para dados de referência. É possível usar o Banco de Dados SQL como dados de referência para o trabalho do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Este artigo monstra como realizar ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Siga as etapas abaixo para adicionar o Banco de Dados SQL do Azure como uma fonte de entrada de referência usando o portal do Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar um trabalho do Stream Analytics.

2. Crie uma conta de armazenamento para ser usada pelo trabalho do Stream Analytics.

3. Crie o Banco de Dados SQL do Azure com um conjunto de dados a ser usado como dados de referência pelo trabalho do Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. No trabalho do Stream Analytics, selecione **Entradas** em **Topologia do trabalho**. Clique em **Adicionar referência de entrada** e escolha **Banco de Dados SQL**.

   ![Entradas é selecionada no painel de navegação esquerdo. Em entradas, + adicionar entrada de referência é selecionado, revelando uma lista suspensa que mostra os valores armazenamento de BLOBs e banco de dados SQL.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as Configurações de entrada do Stream Analytics. Escolha o nome do banco de dados, o nome do servidor, o nome de usuário e a senha. Se quiser que os dados de referência de entrada sejam atualizados periodicamente, escolha "On" para especificar a taxa de atualização em DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curta, você poderá usar uma [consulta delta](sql-reference-data.md#delta-query).

   ![Quando banco de dados SQL é selecionado, a página novo banco de dados SQL é exibida. Há um formulário de configuração no painel esquerdo e uma consulta de instantâneo no painel direito.](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta de instantâneo no editor de consultas SQL. Para obter mais informações, confira o artigo sobre como [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar a conta de armazenamento na configuração do trabalho

Navegue até **Configurações da conta de armazenamento** em **Configurar** e selecione **Adicionar conta de armazenamento**.

   ![Configurações da conta de armazenamento está selecionada no painel esquerdo. Há um botão Adicionar conta de armazenamento no painel direito.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar o trabalho

Depois de configurar outras entradas, saídas e consultas, você poderá iniciar o trabalho do Stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para Visual Studio

Siga as etapas abaixo para adicionar o Banco de Dados SQL do Azure como uma fonte de entrada de referência usando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Visual Studio

1. [Instale as ferramentas do Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Há suporte para as seguintes versões do Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Familiarize-se com o início rápido das [Ferramentas do Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md).

3. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

Use o SQL Server Management Studio para criar uma tabela para armazenar os dados de referência. Consulte [criar seu primeiro banco de dados SQL do Azure usando o SSMS](../azure-sql/database/design-first-database-tutorial.md) para obter detalhes.

A tabela de exemplo usada a seguir foi criada com base nesta instrução:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolha sua assinatura

1. No Visual Studio, no menu **Exibir**, selecione **Gerenciador de Servidores**.

2. Clique com o botão direito do mouse em **Azure**, selecione **Conectar-se à assinatura do Microsoft Azure** e entre com a conta do Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Arquivo > Novo Projeto**. 

2. Na lista de modelos à esquerda, selecione **Stream Analytics** e então selecione **Aplicativo do Stream Analytics do Azure**. 

3. Insira o **Nome**, o **Local** e o **Nome da solução** do projeto e selecione **OK**.

   ![O modelo de Stream Analytics é selecionado, Azure Stream Analytics aplicativo é selecionado e as caixas nome, local e nomes de solução são realçadas.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. Crie uma nova entrada.

   ![Em Adicionar novo item, a entrada é selecionada.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas vezes em **Input.json** no **Gerenciador de soluções**.

3. Preencha as **Configurações de entrada do Stream Analytics**. Escolha o nome do banco de dados, o nome do servidor, tipo de atualização e a taxa de atualização. Especifique a taxa de atualização no formato `DD:HH:MM`.

   ![Em Stream Analytics configuração de entrada, os valores são inseridos ou selecionados nas listas suspensas.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um arquivo CodeBehind do SQL chamado **[Alias de entrada].snapshot.sql** é gerado no projeto sob o nó de arquivo **Input.json**.

   ![O arquivo code-behind do SQL química. snapshot. SQL está realçado.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se escolher "Atualizar periodicamente com Delta", dois arquivos CodeBehind do SQL serão gerados: **[Alias de entrada].snapshot.sql** e **[Alias de entrada].delta.sql**.

   ![Os arquivos codebehind SQL químicos. Delta. SQL e química. snapshot. SQL são realçados.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o arquivo SQL no editor e grave a consulta SQL.

5. Se estiver usando o Visual Studio 2019 e tiver instalado as ferramentas do SQL Server Data, você poderá testar a consulta clicando em **Executar**. Uma janela do assistente será exibida para ajudá-lo a se conectar ao banco de dados SQL e o resultado da consulta será exibido na janela na parte inferior.

### <a name="specify-storage-account"></a>Especificar a conta de armazenamento

Abra o **JobConfig.json** para especificar a conta de armazenamento para armazenar instantâneos de referência do SQL.

   ![Stream Analytics configuração de definição de trabalho é mostrada com valores padrão. As configurações de armazenamento global são realçadas.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testar localmente e implantar no Azure

Antes de implantar o trabalho no Azure, você poderá testar a lógica de consulta localmente em relação aos dados dinâmicos de entrada. Você encontra mais informações sobre esse recurso em [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md). Quando você terminar o teste, clique em **Enviar para o Azure**. Saiba mais sobre como começar trabalho no início rápido [Criar um Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="delta-query"></a>Consulta delta

Ao usar a consulta delta, são recomendadas [tabelas temporais no Banco de Dados SQL do Azure](../azure-sql/temporal-tables.md).

1. Crie uma tabela temporal no Banco de Dados SQL do Azure.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Crie a consulta de instantâneo. 

   Use o parâmetro **\@ SnapshotTime** para instruir o tempo de execução do Stream Analytics a obter o conjunto de dados de referência da tabela temporal do banco de dado SQL válida na hora do sistema. Se não fornecer esse parâmetro, você corre o risco de obter um conjunto impreciso de dados de referência de base devido à defasagem de horário. Veja a seguir um exemplo de uma consulta completa de instantâneo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Crie a consulta delta. 
   
   Essa consulta recupera todas as linhas no banco de dados SQL que foram inseridas ou excluídas em uma hora de início, **\@ deltaStartTime** e uma hora de término **\@ deltaEndTime**. A consulta delta terá de retornar as mesmas colunas como a consulta de instantâneo, bem como a coluna **_operação_**. Esta coluna define se a linha é inserida ou excluída entre **\@deltaStartTime** e **\@deltaEndTime**. As linhas resultantes são sinalizadas como **1**, se os registros foram inseridos, ou **2** se excluídos. A consulta também deve adicionar uma **marca-d'água** do lado do SQL Server para garantir que todas as atualizações no período delta sejam capturadas adequadamente. Usar a consulta delta sem a **marca-d'água** pode resultar em um conjunto de dados de referência incorreto.  

   Quanto aos registros que foram atualizados, a tabela temporal faz a contabilidade, capturando uma operação de inserção e exclusão. O runtime do Stream Analytics aplicará os resultados da consulta delta para o instantâneo anterior a fim de manter os dados de referência atualizados. Veja a seguir um exemplo de consulta delta:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   O runtime do Stream Analytics pode executar periodicamente a consulta de instantâneo além da consulta delta para armazenar pontos de verificação.

## <a name="test-your-query"></a>Testar a consulta
   É importante verificar se a consulta está retornando o conjunto de dados esperado que o trabalho de Stream Analytics usará como dados de referência. Para testar sua consulta, vá para entrada na seção Topologia do Trabalho no portal. Em seguida, você pode selecionar dados de exemplo em sua entrada de Referência do Banco de Dados SQL. Depois que o exemplo for disponibilizado, você poderá baixar o arquivo e verificar se os dados estão sendo retornados conforme o esperado. Se você quiser otimizar suas iterações de desenvolvimento e teste, é recomendável usar [ferramentas do Stream Analytics para Visual Studio](./stream-analytics-tools-for-visual-studio-install.md). Você também pode usar qualquer outra ferramenta de sua preferência para primeiro garantir que a consulta esteja retornando os resultados certos do Banco de Dados SQL do Azure e, em seguida, usá-la em seu trabalho do Stream Analytics. 

### <a name="test-your-query-with-visual-studio-code"></a>Teste sua consulta com Visual Studio Code

   Instale as [ferramentas de Azure Stream Analytics](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) e [SQL Server (mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) no Visual Studio Code e configure seu projeto asa. Para obter mais informações, consulte [início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](./quick-create-visual-studio-code.md) e o [tutorial de extensão MSSQL (SQL Server)](/sql/tools/visual-studio-code/sql-server-develop-use-vscode).

1. Configure sua entrada de dados de referência do SQL.
   
   ![Um editor de Visual Studio Code (guia) mostra ReferenceSQLDatabase.jsem.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Selecione o ícone de SQL Server e clique em **Adicionar conexão**.
   
   ![+ Adicionar conexão aparece no painel esquerdo e é realçado.](./media/sql-reference-data/add-sql-connection.png)

3. Preencha as informações de conexão.
   
   ![As duas caixas de informações de banco de dados e servidor são realçadas.](./media/sql-reference-data/fill-connection-information.png)

4. Clique com o botão direito do mouse em referência SQL e selecione **Executar consulta**.
   
   ![Executar consulta é realçado no menu de contexto.](./media/sql-reference-data/execute-query.png)

5. Escolha sua conexão.
   
   ![A caixa de diálogo informa "criar um perfil de conexão na lista abaixo", e a lista tem uma entrada, que é realçado.](./media/sql-reference-data/choose-connection.png)

6. Examine e verifique o resultado da consulta.
   
   ![Os resultados da pesquisa de consulta estão em uma guia do editor de VS Code.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Perguntas frequentes

**Haverá custos adicionais se eu usar a entrada de dados de referência do SQL no Azure Stream Analytics?**

Não há [custos por unidade de streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) adicionais no trabalho do Stream Analytics. No entanto, é preciso que o trabalho do Stream Analytics tenha uma conta de armazenamento do Azure associada. O trabalho do Stream Analytics consulta o Banco de Dados SQL (durante o início do trabalho e o intervalo de atualização) para recuperar o conjunto de dados de referência e armazena esse instantâneo na conta de armazenamento. O armazenamento desses instantâneos acarretará cobranças adicionais, conforme detalhadas na [página de preços](https://azure.microsoft.com/pricing/details/storage/) da conta de armazenamento do Azure.

**Como saber se o instantâneo de dados de referência está sendo consultado pelo Banco de Dados SQL e está sendo usado no trabalho do Azure Stream Analytics?**

Há duas métricas filtradas pelo nome lógico (em métricas portal do Azure), que você pode usar para monitorar a integridade da entrada de dados de referência do banco do dados SQL.

   * InputEvents: essa métrica mede o número de registros carregados no do conjunto de dados de referência do banco de dado SQL.
   * InputEventBytes: esta métrica mede o tamanho do instantâneo de dados de referência carregado na memória do trabalho do Stream Analytics. 

A combinação dessas duas métricas pode ser usada para inferir se o trabalho está consultando o banco de dados SQL para buscar o conjunto de dado de referência e, em seguida, carregá-lo na memória.

**É necessário um tipo especial de Banco de Dados SQL do Azure?**

O Azure Stream Analytics funciona com qualquer tipo de Banco de Dados SQL do Azure. No entanto, é importante entender que a taxa de atualização definida para sua entrada de dados de referência pode afetar sua carga de consulta. Para usar a opção de consulta delta, é recomendável usar tabelas temporais no Banco de Dados SQL do Azure.

**Por que o Azure Stream Analytics armazena instantâneos na conta de armazenamento do Azure?**

O Stream Analytics garante exatamente o processamento de eventos exatamente uma vez e pelo menos uma entrega de eventos. Nos casos em que problemas transitórios impactarem seu trabalho, será necessária um pequena reprodução para restaurar o estado. Para habilitar a reprodução, é necessário que esses instantâneos estejam armazenados em uma conta de Armazenamento do Azure. Confira mais informações sobre a reprodução de ponto de verificação em [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)