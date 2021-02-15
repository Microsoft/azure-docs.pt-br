---
title: O que é API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API de Verificação Ortográfica do Bing, que usa aprendizado de máquina e tradução automática estatística para fazer a verificação ortográfica contextual.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: be864e04143a381a08dedb5693026f05c42c5421
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349292"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é API de Verificação Ortográfica do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A API de Verificação Ortográfica do Bing permite fazer verificações gramaticais e ortográficas contextuais no texto. Embora a maioria dos verificadores ortográficos dependa de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática estatística para fornecer correções precisas e contextuais. 

## <a name="features"></a>Recursos

| Recurso | Descrição |
|---------|---------|
|Modos múltiplos de verificação ortográfica     | Os modos múltiplos de verificação ortográfica permitem obter correções com foco na gramática e/ou na ortografia. |
|Reconhecimento de gírias e linguagem informal     | Reconheça expressões comuns e termos informais usados no texto.         |
|Diferenciação entre palavras semelhantes     | Encontre o uso correto entre palavras com sons semelhantes, mas com significados diferentes (por exemplo, "see" e "sea")        |
|Suporte de marca, título e uso popular     | Reconheça novas marcas, títulos e outras expressões comuns conforme vão surgindo |

## <a name="workflow"></a>Fluxo de trabalho

A API de Verificação Ortográfica do Bing é fácil de ser chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. O serviço pode ser acessado usando a API REST ou os SDKs de Verificação Ortográfica do Bing. 

1. Crie uma [Conta da API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Caso você não tenha uma assinatura do Azure, crie uma conta gratuita. 
2. Envie uma solicitação à API de Pesquisa na Web do Bing.
3. Analisar a resposta JSON

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) da API de Pesquisa de Verificação Ortográfica de Bing para ver como verificar rapidamente uma variedade de textos.

Quando você estiver pronto para chamar a API, crie uma [Conta de API de serviços cognitivos](../../cognitive-services/cognitive-services-apis-create-account.md). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.

Você também pode visitar a [página do hub da API de Pesquisa do Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.