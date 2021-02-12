---
title: Tarefas e aplicativos de replicação de mensagens – barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da criação de aplicativos e tarefas de replicação de mensagens com o Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657330"
---
# <a name="message-replication-tasks-and-applications"></a>Tarefas e aplicativos de replicação de mensagens

Conforme explicado no artigo sobre a [replicação de mensagens e a Federação entre regiões](service-bus-federation-overview.md) , a replicação de sequências de mensagens entre pares de entidades do barramento de serviço e entre o barramento de serviço e outras fontes de mensagens e destinos geralmente é mais simples em Azure functions.

[Azure Functions](../azure-functions/functions-overview.md) é um ambiente de execução escalonável e confiável para configurar e executar aplicativos sem servidor, incluindo tarefas [de Federação e replicação de mensagens](service-bus-federation-overview.md) .

Nesta visão geral, você aprenderá sobre os recursos internos do Azure Functions para tais aplicativos, sobre os blocos de código que você pode adaptar e modificar para tarefas de transformação e sobre como configurar um aplicativo Azure Functions de modo que ele se integre de forma ideal com o barramento de serviço e outros serviços de mensagens do Azure. Para muitos detalhes, este artigo apontará para a documentação Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
