---
title: Manipular procedimentos armazenados de longa execução no conector do SQL
description: Como lidar com procedimentos armazenados que atingiram o tempo limite ao usar o conector do SQL em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102788"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Manipular tempos limite do procedimento armazenado no conector do SQL para aplicativos lógicos do Azure

Quando seu aplicativo lógico funciona com conjuntos de resultados tão grandes que o [conector do SQL](../connectors/connectors-create-api-sqlazure.md) não retorna todos os resultados ao mesmo tempo, ou se você quiser mais controle sobre o tamanho e a estrutura de seus conjuntos de resultados, poderá criar um [procedimento armazenado](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que organize os resultados da maneira desejada. O conector do SQL fornece muitos recursos de back-end que você pode acessar usando o [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) para que você possa automatizar com mais facilidade as tarefas comerciais que funcionam com tabelas do banco de dados SQL.

Por exemplo, ao obter ou inserir várias linhas, seu aplicativo lógico pode iterar por meio dessas linhas usando um [loop **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando seu aplicativo lógico tem que trabalhar com milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados. Para obter mais informações, consulte [tratar dados em massa usando o conector do SQL](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Limite de tempo limite na execução de procedimento armazenado

O conector do SQL tem um limite de tempo limite de procedimento armazenado [inferior a 2 minutos](/connectors/sql/#known-issues-and-limitations). Alguns procedimentos armazenados podem levar mais tempo do que esse limite para ser concluído, causando um `504 Timeout` erro. Às vezes, esses processos de execução longa são codificados como procedimentos armazenados explicitamente para essa finalidade. Devido ao tempo limite, chamar esses procedimentos de aplicativos lógicos do Azure pode criar problemas. Embora o conector do SQL não dê suporte nativo a um modo assíncrono, você pode contornar esse problema e simular esse modo usando um gatilho de conclusão do SQL, uma consulta de passagem SQL nativa, uma tabela de estado e trabalhos do lado do servidor. Para essa tarefa, você pode usar o [agente de trabalho elástico do Azure](../azure-sql/database/elastic-jobs-overview.md) para o [banco de dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md). Para [SQL Server local](/sql/sql-server/sql-server-technical-documentation) e o [SQL instância gerenciada do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), você pode usar o [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

Por exemplo, suponha que você tenha o seguinte procedimento armazenado de execução longa, que leva mais tempo do que o tempo limite para concluir a execução. Se você executar esse procedimento armazenado de um aplicativo lógico usando o conector do SQL, obterá um `HTTP 504 Gateway Timeout` erro como resultado.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Em vez de chamar diretamente o procedimento armazenado, você pode executar o procedimento de forma assíncrona em segundo plano usando um *agente de trabalho*. Você pode armazenar as entradas e saídas em uma tabela de estado com a qual você pode interagir por meio de seu aplicativo lógico. Se você não precisar das entradas e saídas, ou se já estiver gravando os resultados em uma tabela no procedimento armazenado, você poderá simplificar essa abordagem.

> [!IMPORTANT]
> Verifique se o procedimento armazenado e todos os trabalhos são *idempotentes*, o que significa que eles podem ser executados várias vezes sem afetar os resultados. Se o processamento assíncrono falhar ou expirar, o agente de trabalho poderá repetir a etapa e, portanto, o procedimento armazenado, várias vezes. Para evitar a duplicação de saída, antes de criar qualquer objeto, examine essas [práticas recomendadas e abordagens](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

A próxima seção descreve como você pode usar o agente de trabalho elástico do Azure para o banco de dados SQL do Azure. Para SQL Server e SQL Instância Gerenciada do Azure, você pode usar o SQL Server Agent. Alguns detalhes de gerenciamento serão diferentes, mas as etapas fundamentais permanecerão as mesmas que a configuração de um agente de trabalho para o banco de dados SQL do Azure.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Agente de trabalho para banco de dados SQL do Azure

Para criar um trabalho que possa executar o procedimento armazenado para o [banco de dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md), use o [agente de trabalho elástico do Azure](../azure-sql/database/elastic-jobs-overview.md). Crie seu agente de trabalho no portal do Azure. Essa abordagem adicionará vários procedimentos armazenados ao banco de dados que é usado pelo agente, também conhecido como o *banco de dados do Agent*. Você pode criar um trabalho que executa o procedimento armazenado no banco de dados de destino e captura a saída quando terminar.

Antes de criar o trabalho, você precisa configurar permissões, grupos e destinos conforme descrito pela [documentação completa para o agente de trabalho elástico do Azure](../azure-sql/database/elastic-jobs-overview.md). Você também precisa criar uma tabela de suporte no banco de dados de destino, conforme descrito nas seções a seguir.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Criar tabela de estado para registrar parâmetros e armazenar entradas

Os trabalhos do SQL Agent não aceitam parâmetros de entrada. Em vez disso, no banco de dados de destino, crie uma tabela de estado na qual você registra os parâmetros e armazene as entradas a serem usadas para chamar os procedimentos armazenados. Todas as etapas de trabalho do Agent são executadas no banco de dados de destino, mas os procedimentos armazenados do trabalho são executados no banco de dados do Agent. 

Para criar a tabela de estado, use este esquema:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Veja a aparência da tabela resultante no [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms):

![Captura de tela que mostra a tabela de estado criada que armazena entradas para o procedimento armazenado.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Para garantir um bom desempenho e certificar-se de que o trabalho do agente possa localizar o registro associado, a tabela usa a ID de execução do trabalho ( `jobid` ) como a chave primária. Se desejar, você também pode adicionar colunas individuais para os parâmetros de entrada. O esquema descrito anteriormente pode geralmente lidar com vários parâmetros, mas é limitado ao tamanho calculado por `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Criar um trabalho de nível superior para executar o procedimento armazenado

Para executar o procedimento armazenado de execução longa, crie este agente de trabalho de nível superior no banco de dados do agente:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Agora, adicione etapas ao trabalho que parametrizar, executar e concluir o procedimento armazenado. Por padrão, uma etapa de trabalho expira após 12 horas. Se o procedimento armazenado precisar de mais tempo, ou se você quiser que o procedimento expire anteriormente, você poderá alterar o `step_timeout_seconds` parâmetro para outro valor que é especificado em segundos. Por padrão, uma etapa tem 10 repetições internas com um tempo limite de retirada entre cada repetição, que você pode usar para sua vantagem.

Aqui estão as etapas para adicionar:

1. Aguarde até que os parâmetros apareçam na `LongRunningState` tabela.

   Essa primeira etapa aguarda que os parâmetros sejam adicionados à `LongRunningState` tabela, o que acontece logo depois que o trabalho é iniciado. Se a ID de execução do trabalho ( `jobid` ) não for adicionada à `LongRunningState` tabela, a etapa simplesmente falhará e o tempo limite de repetição ou retirada padrão fará a espera:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Consulte os parâmetros da tabela de estado e passe-os para o procedimento armazenado. Essa etapa também executa o procedimento em segundo plano. 

   Se o procedimento armazenado não precisar de parâmetros, basta chamar diretamente o procedimento armazenado. Caso contrário, para passar o `@timespan` parâmetro, use o `@callparams` , que também pode ser estendido para passar parâmetros adicionais.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Conclua o trabalho e registre os resultados.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Iniciar o trabalho e passar os parâmetros

Para iniciar o trabalho, use uma consulta nativa de passagem com a [ação **executar uma consulta SQL**](/connectors/sql/#execute-a-sql-query-(v2)) e envie imediatamente os parâmetros do trabalho para a tabela de estado. Para fornecer entrada para o `jobid` atributo na tabela de destino, os aplicativos lógicos adicionam um loop **for each** que itera através da saída da tabela da ação anterior. Para cada ID de execução de trabalho, execute uma ação de **inserção de linha** que usa a saída de dados dinâmicos, `ResultSets JobExecutionId` , para adicionar os parâmetros para o trabalho a ser desempacotado e passar para o procedimento armazenado de destino.

![Captura de tela que mostra as ações a serem usadas para iniciar o trabalho e passar parâmetros para o procedimento armazenado.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Quando o trabalho é concluído, o trabalho atualiza a `LongRunningState` tabela para que você possa disparar facilmente o resultado usando o [gatilho **quando um item é modificado**](/connectors/sql/#when-an-item-is-modified-(v2)). Se você não precisar da saída ou se já tiver um gatilho que monitora uma tabela de saída, poderá ignorar essa parte.

![Captura de tela que mostra o gatilho SQL para quando um item é modificado.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Agente de trabalho para SQL Server ou SQL do Azure Instância Gerenciada

Para o mesmo cenário, você pode usar o [SQL Server Agent](/sql/ssms/agent/sql-server-agent) para [SQL Server local](/sql/sql-server/sql-server-technical-documentation) e [instância gerenciada do Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Embora alguns detalhes de gerenciamento sejam diferentes, as etapas fundamentais permanecem as mesmas que a configuração de um agente de trabalho para o banco de dados SQL do Azure.

## <a name="next-steps"></a>Próximas etapas

[Conectar-se a SQL Server, banco de dados SQL do Azure ou SQL do Azure Instância Gerenciada](../connectors/connectors-create-api-sqlazure.md)

