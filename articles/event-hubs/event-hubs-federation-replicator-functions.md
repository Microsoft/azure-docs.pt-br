---
title: Tarefas e aplicativos de replicação de eventos – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da criação de tarefas e aplicativos de replicação de eventos com o Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97663565"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Tarefas e aplicativos de replicação de eventos com Azure Functions

> [!TIP]
> Para todas as tarefas de replicação com estado em que você precisa considerar as cargas de seus eventos e transformar, agregar, enriquecer ou reduzir, use [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) em vez de Azure functions.

Conforme explicado no artigo [replicação de eventos e Federação entre regiões](event-hubs-federation-overview.md) , a replicação sem estado de fluxos de eventos entre pares de hubs de eventos e entre os hubs de eventos e outras fontes de fluxo de eventos e se destina a Lean Azure functions.

[Azure Functions](../azure-functions/functions-overview.md) é um ambiente de execução escalonável e confiável para configurar e executar aplicativos sem servidor, incluindo tarefas de Federação e replicação de eventos.

Nesta visão geral, você aprenderá sobre os recursos internos do Azure Functions para tais aplicativos, sobre os blocos de código que você pode adaptar e modificar para tarefas de transformação e sobre como configurar um aplicativo Azure Functions de modo que ele se integre de forma ideal com os hubs de eventos e outros serviços de mensagens do Azure. Para muitos detalhes, este artigo apontará para a documentação Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









