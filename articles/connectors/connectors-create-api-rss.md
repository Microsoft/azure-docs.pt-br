---
title: Conectar-se com RSS feeds dos Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que monitoram e gerenciam RSS feeds usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204854"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerencie RSS feeds usando os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector de RSS, é possível criar fluxos de trabalho e tarefas automatizadas para qualquer RSS feed, por exemplo:

* Monitorar quando itens de RSS feeds são publicados.
* Listar todos os itens de um RSS feed.

RSS (Rich Site Summary), também chamado de Really Simple Syndication, é um formato popular para sindicalização da Web e é usado para publicar conteúdos atualizados com frequência, como postagens de blog e manchetes de notícias. Muitos editores de conteúdo fornecem um RSS feed para que os usuários possam assinar um determinado conteúdo. 

É possível usar um gatilho RSS que obtém respostas de um RSS feed e disponibiliza a saída para outras ações. Você pode usar uma ação de RSS em seus aplicativos lógicos para executar uma tarefa com o RSS feed. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* A URL de um RSS feed

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar um RSS feed. Para iniciar com um gatilho RSS, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de RSS, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-an-rss-feed"></a>Conectar-se com um RSS feed

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "rss" como o filtro. Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que deseja adicionar uma ação, escolha **Nova etapa**. Na caixa de pesquisa, insira "rss" como o seu filtro. Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/rss/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)