---
title: Tipos de URLs com suporte para Import-QnA Maker
description: Entenda como os tipos de URLs são usados para importar e criar pares de QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776690"
---
# <a name="urls-supported-for-importing-documents"></a>URLs com suporte para importação de documentos

Entenda como os tipos de URLs são usados para importar e criar pares de QnA.

## <a name="faq-urls"></a>URLs de perguntas frequentes

O QnA Maker pode dar suporte a páginas da Web de perguntas frequentes em três diferentes formas:

* Páginas de perguntas frequentes simples
* Páginas de perguntas frequentes com links
* Página de perguntas frequentes com uma home page de tópicos

### <a name="plain-faq-pages"></a>Páginas de perguntas frequentes simples

Este é o tipo mais comum de página de perguntas frequentes em que as respostas a seguem imediatamente às perguntas na mesma página.

Abaixo está um exemplo de uma página de perguntas frequentes simples:

![Exemplo de página de perguntas frequentes simples para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de perguntas frequentes com links

Nesse tipo de página de perguntas frequentes, as perguntas são agregadas e vinculadas a respostas em seções diferentes da mesma página ou em páginas diferentes.

Abaixo está um exemplo de uma página de perguntas frequentes com links nas seções que estão na mesma página:

 ![Exemplo de página de perguntas frequentes com link de seção para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Links de página de tópicos pai para páginas de respostas filho

Esse tipo de perguntas frequentes tem uma página de tópicos em que cada tópico está vinculado a um conjunto correspondente de perguntas e respostas em uma página diferente. QnA Maker rastreia todas as páginas vinculadas para extrair as perguntas correspondentes & respostas.

Abaixo está um exemplo de uma página de tópicos com links para seções de perguntas frequentes em páginas diferentes.

 ![Exemplo de página de perguntas frequentes com link profundo para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>URLs de suporte

O QnA Maker pode processar páginas da web de suporte semiestruturadas, como artigos da web que descrevem como executar uma tarefa específica, como diagnosticar e resolver um problema específico e quais são as práticas recomendadas para um determinado processo. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

> [!NOTE]
> Extração para artigos de suporte é um recurso novo e está nos estágios iniciais. Funciona melhor para páginas simples, que também são estruturadas e não contêm cabeçalhos/rodapés complexos.

![O QnA Maker dá suporte à extração de páginas web semi-estruturadas em que a estrutura limpa é apresentada com cabeçalhos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)