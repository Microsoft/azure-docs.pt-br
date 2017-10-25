---
title: "Conector do GitHub no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O GitHub é um serviço de hospedagem do repositório Git baseado na Web. Ele oferece toda a funcionalidade de controle de revisão distribuída e de SCM (gerenciamento do código-fonte) do Git, além de adicionar seus próprios recursos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-github-connector"></a>Introdução ao conector do GitHub
O GitHub é um serviço de hospedagem do repositório Git baseado na Web. Ele oferece toda a funcionalidade de controle de revisão distribuída e de SCM (gerenciamento do código-fonte) do Git, além de adicionar seus próprios recursos.

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub
Para criar Aplicativos lógicos com o GitHub, você deve primeiro criar uma **conexão**, em seguida, fornecer os detalhes para as seguintes propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do GitHub |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/github/).

## <a name="more-connectors"></a>Mais conectores
Volte para a [Lista de APIs](apis-list.md).