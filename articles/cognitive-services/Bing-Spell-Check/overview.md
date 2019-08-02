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
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 996db9690d19cc40c3963922d4edb3b59469752b
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514805"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é API de Verificação Ortográfica do Bing?

A API de Verificação Ortográfica do Bing permite fazer verificações gramaticais e ortográficas contextuais no texto. Embora a maioria dos verificadores ortográficos dependa de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática estatística para fornecer correções precisas e contextuais. 

## <a name="features"></a>Recursos


|  |  |
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

Quando você estiver pronto para chamar a API, crie uma [Conta de API de serviços cognitivos](../../cognitive-services/cognitive-services-apis-create-account.md). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.
