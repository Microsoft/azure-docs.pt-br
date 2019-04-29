---
title: Conectar-se com o Project Online dos Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize fluxos de trabalho que monitoram, criam e gerenciam projetos, tarefas e recursos do Project Online usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105623"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gerencie projetos, tarefas e recursos do Project Online usando os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Project Online, você pode criar fluxos de trabalho e tarefas automatizadas para seus projetos, tarefas e recursos no Project Online por meio do Office 365. Por exemplo, os fluxos de trabalho podem executar estas e outras ações:

* Monitorar quando novos projetos, tarefas ou recursos são criados. Monitorar quando novos projetos são publicados.
* Criar novos projetos, tarefas ou recursos.
* Listar projetos ou tarefas existentes.
* Fazer check-out e check-in de projetos ou publicá-los.

O Project Online ajuda você a planejar, priorizar e gerenciar projetos e investimentos em portfólio de projetos, praticamente em qualquer lugar e em qualquer dispositivo, fornecendo funcionalidades avançadas de gerenciamento de projetos. Você pode usar gatilhos do Project Online para obter respostas do Project Online e disponibilizar a saída para outras ações. Você pode usar ações em seus aplicativos lógicos para executar diversas tarefas no Project Online. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Project Online, disponível com uma [conta do Office 365](https://www.office.com/), 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar seus dados do Project Online. Para iniciar com um gatilho do Project Online, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar ações do Project Online, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-project-online"></a>Conectar-se com o Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "Project Online" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que deseja adicionar uma ação, escolha **Nova etapa**. Na caixa de pesquisa, insira “Project Online” como filtro. Na lista de ações, selecione a ação desejada.

1. Se for solicitado que você entre no Project Online, faça isso agora.

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão com o Project Online e acessar seus dados.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/projectonline/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)