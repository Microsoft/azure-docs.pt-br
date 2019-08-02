---
title: Usar dados de referência de banco de dados SQL para um trabalho do Azure Stream Analytics
description: Este artigo descreve como usar um Banco de Dados SQL como entrada de dados de referência para um trabalho do Azure Stream Analytics no portal do Azure e no Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: ae1954b99e268e8bc44c4ba29bbc79d7734fda6e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461745"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Usar dados de referência de banco de dados SQL para um trabalho do Azure Stream Analytics

O Azure Stream Analytics dá suporte ao Banco de Dados SQL do Azure como uma fonte de entrada para dados de referência. É possível usar o Banco de Dados SQL como dados de referência para o trabalho do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Este artigo monstra como realizar ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Siga as etapas abaixo para adicionar o Banco de Dados SQL do Azure como uma fonte de entrada de referência usando o portal do Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar um trabalho do Stream Analytics.

2. Crie uma conta de armazenamento para ser usada pelo trabalho do Stream Analytics.

3. Crie o Banco de Dados SQL do Azure com um conjunto de dados a ser usado como dados de referência pelo trabalho do Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. No trabalho do Stream Analytics, selecione **Entradas** em **Topologia do trabalho**. Clique em **Adicionar referência de entrada** e escolha **Banco de Dados SQL**.

   ![Entrada de trabalho do Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as Configurações de entrada do Stream Analytics. Escolha o nome do banco de dados, o nome do servidor, o nome de usuário e a senha. Se quiser que os dados de referência de entrada sejam atualizados periodicamente, escolha "On" para especificar a taxa de atualização em DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curta, você poderá usar uma [consulta delta](sql-reference-data.md#delta-query).

   ![Configuração da referência do Banco de Dados SQL](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta de instantâneo no editor de consultas SQL. Para obter mais informações, confira o artigo sobre como [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar a conta de armazenamento na configuração do trabalho

Navegue até **Configurações da conta de armazenamento** em **Configurar** e selecione **Adicionar conta de armazenamento**.

   ![Configurações de conta de armazenamento do Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

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

Use o SQL Server Management Studio para criar uma tabela para armazenar os dados de referência. Saiba mais em [Projetar seu primeiro Banco de Dados SQL do Azure usando o SSMS](../sql-database/sql-database-design-first-database.md).

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

   ![Projeto do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. Crie uma nova entrada.

   ![Entrada do New Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas vezes em **Input.json** no **Gerenciador de soluções**.

3. Preencha as **Configurações de entrada do Stream Analytics**. Escolha o nome do banco de dados, o nome do servidor, tipo de atualização e a taxa de atualização. Especifique a taxa de atualização no formato `DD:HH:MM`.

   ![Configuração de entrada do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um arquivo CodeBehind do SQL chamado **[Alias de entrada].snapshot.sql** é gerado no projeto sob o nó de arquivo **Input.json**.

   ![Entrada do CodeBehind no Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se escolher "Atualizar periodicamente com Delta", dois arquivos CodeBehind do SQL serão gerados: **[Alias de entrada].snapshot.sql** e **[Alias de entrada].delta.sql**.

   ![CodeBehind no Gerenciador de Soluções](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o arquivo SQL no editor e grave a consulta SQL.

5. Se você estiver usando o Visual Studio de 2019, e você tiver instalado o SQL Server Data tools, você pode testar a consulta clicando **Execute**. Uma janela de assistente será exibida para ajudá-lo a se conectar ao Banco de Dados SQL, e o resultado da consulta será exibido na parte inferior da janela.

### <a name="specify-storage-account"></a>Especificar a conta de armazenamento

Abra o **JobConfig.json** para especificar a conta de armazenamento para armazenar instantâneos de referência do SQL.

   ![Configuração de trabalho do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testar localmente e implantar no Azure

Antes de implantar o trabalho no Azure, você poderá testar a lógica de consulta localmente em relação aos dados dinâmicos de entrada. Você encontra mais informações sobre esse recurso em [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md). Quando você terminar o teste, clique em **Enviar para o Azure**. Saiba mais sobre como começar trabalho no início rápido [Criar um Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="delta-query"></a>Consulta delta

Ao usar a consulta delta, são recomendadas [tabelas temporais no Banco de Dados SQL do Azure](../sql-database/sql-database-temporal-tables.md).

1. Crie uma tabela temporal no SQL Azure.
   
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

   Use o  **\@snapshotTime** parâmetro para instruir o tempo de execução do Stream Analytics para obter o conjunto de dados de referência de tabela temporal de banco de dados SQL válida na hora do sistema. Se não fornecer esse parâmetro, você corre o risco de obter um conjunto impreciso de dados de referência de base devido à defasagem de horário. Veja a seguir um exemplo de uma consulta completa de instantâneo:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Crie a consulta delta. 
   
   Essa consulta recupera todas as linhas no banco de dados SQL que foram inseridas ou excluídas dentro de uma hora de início  **\@deltaStartTime**e uma hora de término  **\@deltaEndTime**. A consulta delta terá de retornar as mesmas colunas como a consulta de instantâneo, bem como a coluna **_operação_** . Essa coluna define se a linha é inserida ou excluída entre  **\@deltaStartTime** e  **\@deltaEndTime**. As linhas resultantes são sinalizadas como **1**, se os registros foram inseridos, ou **2** se excluídos. 

   Quanto aos registros que foram atualizados, a tabela temporal faz a contabilidade, capturando uma operação de inserção e exclusão. O tempo de execução do Stream Analytics aplicará os resultados da consulta delta para o instantâneo anterior a fim de manter os dados de referência atualizados. Veja a seguir um exemplo de consulta delta:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   O tempo de execução do Stream Analytics pode executar periodicamente a consulta de instantâneo além da consulta delta para armazenar pontos de verificação.

## <a name="test-your-query"></a>Testar a consulta
   É importante verificar que sua consulta está retornando o conjunto de dados esperado que o trabalho do Stream Analytics usará como dados de referência. Para testar sua consulta, vá para a entrada na seção de topologia do trabalho no portal. Em seguida, você pode selecionar dados de exemplo na referência de banco de dados SQL de entrada. Depois que o exemplo se torna disponível, você pode baixar o arquivo e verifique ver se os dados retornados serão como esperado. Se você quiser um otimizar suas iterações de desenvolvimento e teste, é recomendável usar o [ferramentas do Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Você pode também qualquer outra ferramenta de sua preferência, primeiro verifique se que a consulta está retornando os resultados certos no banco de dados SQL e, em seguida, usá-lo no trabalho do Stream Analytics. 

## <a name="faqs"></a>Perguntas frequentes

**Haverá custos adicionais se eu usar a entrada de dados de referência do SQL no Azure Stream Analytics?**

Não há [custos por unidade de streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) adicionais no trabalho do Stream Analytics. No entanto, é preciso que o trabalho do Stream Analytics tenha uma conta de armazenamento do Azure associada. O trabalho do Stream Analytics consulta o Banco de Dados SQL (durante o início do trabalho e o intervalo de atualização) para recuperar o conjunto de dados de referência e armazena esse instantâneo na conta de armazenamento. O armazenamento desses instantâneos acarretará cobranças adicionais, conforme detalhadas na [página de preços](https://azure.microsoft.com/pricing/details/storage/) da conta de armazenamento do Azure.

**Como saber se o instantâneo de dados de referência está sendo consultado pelo Banco de Dados SQL e está sendo usado no trabalho do Azure Stream Analytics?**

Existem duas métricas filtradas pelo nome lógico (no Portal do Azure de métricas) que você pode usar para monitorar a integridade dos dados de referência de banco de dados SQL de entrada.

   * InputEvents: esta métrica mede o número de registros carregados do conjunto de dados de referência de Banco de Dados SQL.
   * InputEventBytes: esta métrica mede o tamanho do instantâneo de dados de referência carregado na memória do trabalho do Stream Analytics. 

A combinação de ambas as métricas pode ser usada para inferir se o trabalho é a consulta do Banco de Dados SQL para buscar o conjunto de dados de referência e, então, carregá-lo na memória.

**É necessário um tipo especial de Banco de Dados SQL do Azure?**

O Azure Stream Analytics funciona com qualquer tipo de Banco de Dados SQL do Azure. No entanto, é importante entender que a taxa de atualização definida para sua entrada de dados de referência pode afetar sua carga de consulta. Para usar a opção de consulta delta, é recomendável usar tabelas temporais no Banco de Dados SQL do Azure.

**Por que o Azure Stream Analytics armazena instantâneos na conta de armazenamento do Azure?**

O Stream Analytics garante exatamente o processamento de eventos exatamente uma vez e pelo menos uma entrega de eventos. Nos casos em que problemas transitórios impactarem seu trabalho, será necessária um pequena reprodução para restaurar o estado. Para habilitar a reprodução, é necessário que esses instantâneos estejam armazenados em uma conta de Armazenamento do Azure. Confira mais informações sobre a reprodução de ponto de verificação em [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)
