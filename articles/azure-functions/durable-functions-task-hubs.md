---
title: "Hubs de tarefas nas Funções Duráveis – Azure"
description: "Saiba o que é um hub de tarefas na extensão de Funções Duráveis do Azure Functions. Saiba como configurar hubs de tarefas."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas nas Funções Duráveis (Azure Functions)

Um *hub tarefas* para [Funções Duráveis](durable-functions-overview.md) é um contêiner lógico para orquestrações e atividades no contexto de uma única conta de armazenamento do Azure. Várias funções e até mesmo aplicativos de funções podem existir no mesmo hub de tarefas e o hub de tarefas normalmente serve como um contêiner de aplicativo.

Hubs de tarefas não precisam ser criados de forma explícita. Eles são inicializados automaticamente pelo tempo de execução, usando um nome declarado no arquivo *host.json*. Cada hub de tarefas tem seu próprio conjunto de filas, tabelas e blobs de armazenamento em uma única conta de armazenamento. Todos os aplicativos de funções que são executados em um determinado hub de tarefas compartilham os mesmos recursos de armazenamento. Funções de orquestrador e atividade só podem interagir entre si quando pertencem ao mesmo hub de tarefas.

## <a name="configuring-a-task-hub-in-hostjson"></a>Configurando um hub de tarefas no host.json

O nome de um hub de tarefas pode ser configurado no arquivo *host.json* de um aplicativo de funções.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**.

> [!NOTE]
> Se você tiver vários aplicativos de funções que compartilham uma conta de armazenamento, é recomendável configurar um nome de hub de tarefas diferente para cada aplicativo de funções. Isso garante que cada aplicativo de funções seja isolado corretamente um do outro.

## <a name="azure-storage-resources"></a>Recursos de Armazenamento do Azure

Um hub de tarefas é composto por vários recursos do Armazenamento do Azure:

* Uma ou mais filas de controle.
* Uma fila de item de trabalho.
* Uma tabela de histórico.
* Um contêiner de armazenamento que contém um ou mais blobs de concessão.

Todos esses recursos são criados automaticamente na conta padrão do Armazenamento do Azure quando funções de atividade ou orquestrador são executadas ou estão agendadas para execução. O artigo [Desempenho e escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)

