---
title: Usar Azure Stream Analytics no pool dedicado do SQL
description: Dicas para usar o Azure Stream Analytics com o pool dedicado do SQL no Azure Synapse para desenvolver soluções em tempo real.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "96458217"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Usar o Azure Stream Analytics com o pool SQL dedicado no Azure Synapse Analytics

A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Neste artigo, você aprenderá a usar seu pool SQL dedicado como um coletor de saída para a ingestão de dados de alta taxa de transferência com trabalhos de Azure Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Azure Stream Analytics trabalho – para criar um trabalho de Azure Stream Analytics, siga as etapas no tutorial introdução ao [uso de Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para:  

    1. Criar uma entrada de Hub de eventos
    2. Configurar e iniciar o aplicativo gerador de evento
    3. Provisionar um trabalho de análise de fluxo
    4. Especifique a entrada e a consulta do trabalho
* Pool de SQL dedicado – para criar um novo pool de SQL dedicado, siga as etapas no guia de [início rápido: criar um pool de SQL dedicado](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Especifique a saída de streaming para apontar para seu pool SQL dedicado

### <a name="step-1"></a>Etapa 1

No portal do Azure, vá para seu trabalho de Stream Analytics e clique em **saídas** no menu **topologia de trabalho** .

### <a name="step-2"></a>Etapa 2

Clique no botão **Adicionar** e escolha **análise de Synapse do Azure** no menu suspenso.

![Escolher o Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Etapa 3

Insira os valores a seguir:

* *Alias de saída*: Insira um nome amigável para esta saída de trabalho.
* *Assinatura*:
  * Se o seu pool SQL dedicado estiver na mesma assinatura que o trabalho de Stream Analytics, clique em ***selecionar Azure Synapse Analytics de suas assinaturas** _.
  _ Se o seu pool SQL dedicado estiver em uma assinatura diferente, clique em fornecer configurações do Azure Synapse Analytics manualmente.
* *Banco de dados*: selecione o banco de dados de destino na lista suspensa.
* *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: forneça a senha da conta de usuário especificada.
* *Tabela*: especifique o nome da tabela de destino no banco de dados.
* Clique no botão **salvar**

![Formulário do Azure Synapse Analytics concluído](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Etapa 4

Antes de executar um teste, você precisará criar a tabela em seu pool SQL dedicado.  Execute o seguinte script de criação de tabela usando SQL Server Management Studio (SSMS) ou sua opção de ferramenta de consulta.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Etapa 5

No portal do Azure para o trabalho de Stream Analytics, clique no nome do trabalho.  Clique no botão **_Test_* _ no painel de _*_detalhes de saída_*_ .

![Botão testar em detalhes do outpout ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) quando a conexão com o banco de dados for realizada com sucesso, você verá uma notificação no Portal.

### <a name="step-6"></a>Etapa 6

Clique no menu _*_consulta_*_ em _*_topologia do trabalho_*_ e altere a consulta para inserir dados na saída do fluxo que você criou.  Clique no botão _*_testar consulta selecionada_*_ para testar sua consulta.  Clique no botão _*_Salvar consulta_*_ quando o teste de consulta for bem-sucedido.

![Salvar consulta](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Etapa 7

Inicie o trabalho de Azure Stream Analytics.  Clique no botão _*_Iniciar_*_ no menu _*_visão geral_*_ .

![Iniciar trabalho do Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Clique no botão _ *_Iniciar_** no painel Iniciar trabalho.

![Clique em Iniciar](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da integração, consulte [integrar outros serviços](sql-data-warehouse-overview-integrate.md).
Para obter mais dicas de desenvolvimento, consulte [decisões de design e técnicas de codificação para o pool SQL dedicado](sql-data-warehouse-overview-develop.md).
