---
title: Dimensionar Azure Cosmos DB em uma agenda usando o temporizador Azure Functions
description: Saiba como dimensionar alterações na taxa de transferência em Azure Cosmos DB usando o PowerShell e o Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339242"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Dimensionar Azure Cosmos DB taxa de transferência usando Azure Functions gatilho de timer
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O desempenho de uma conta do Azure cosmos é baseado na quantidade de produtividade provisionada expressa em unidades de solicitação por segundo (RU/s). O provisionamento é de uma granularidade de segundo e é cobrado com base na mais alta RU/s por hora. Esse modelo de capacidade provisionada permite que o serviço forneça uma taxa de transferência previsível e consistente, além de garantia de baixa latência e alta disponibilidade. A maioria das cargas de trabalho de produção esses recursos. No entanto, em ambientes de desenvolvimento e teste em que Azure Cosmos DB é usado somente durante o horário de trabalho, você pode escalar verticalmente a taxa de transferência na manhã e reduzir a velocidade da noite após o horário de trabalho.

Você pode definir a taxa de transferência por meio de [modelos de Azure Resource Manager](./templates-samples-sql.md), [CLI do Azure](cli-samples.md)e [PowerShell](powershell-samples.md), para contas de API de núcleo (SQL) ou usando os SDKs de Azure Cosmos DB específicos à linguagem. O benefício de usar modelos do Resource Manager, CLI do Azure ou PowerShell é que eles dão suporte a todas as APIs de modelo de Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Projeto de exemplo do Agendador de produtividade

Para simplificar o processo de dimensionamento de Azure Cosmos DB em uma agenda, criamos um projeto de exemplo chamado [Agendador de produtividade Cosmos do Azure](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Este projeto é um aplicativo Azure Functions com dois gatilhos de temporizador: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script do PowerShell que define a taxa de transferência em cada recurso, conforme definido no `resources.json` arquivo em cada gatilho. O ScaleUpTrigger é configurado para ser executado às 8:00 UTC e o ScaleDownTrigger é configurado para ser executado às 18:00 UTC e essas horas podem ser facilmente atualizadas dentro do `function.json` arquivo para cada gatilho.

Você pode clonar esse projeto localmente, modificá-lo para especificar os Azure Cosmos DB recursos para escalar verticalmente e para baixo e a agenda para execução. Posteriormente, você pode implantá-lo em uma assinatura do Azure e protegê-lo usando a identidade de serviço gerenciada com as permissões do Azure [RBAC (controle de acesso baseado em função)](role-based-access-control.md) com a função "operador de Azure Cosmos DB" para definir a taxa de transferência em suas contas do Azure Cosmos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais e baixe o exemplo do [Agendador de taxa de transferência Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).