---
title: Escolha o tipo de preço para Azure Mapas | Microsoft Docs
description: Saiba mais sobre tipo de preço oferecido pelo Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1bf60014b20f4cec29d661db4deb029fea4ab1d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60796297"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha a camada de preços certa no Azure Mapas

Os Mapas do Azure oferecem dois tipos de preço. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para ajudar a escolher a melhor camada de preços, pergunte o seguinte a si mesmo.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?
A camada de preços S0 é ideal para você se as APIs geoespaciais principais atendem aos seus requisitos de serviço. Se quiser recursos mais avançados para seu aplicativo, considere a camada de preços S1. Exemplos de recursos incluem imagens por área e híbridas, obtenção de intervalo de rota e geocodificação em lote. A tabela de **recursos por camada de preços** a seguir dá uma ideia melhor das necessidades do seu aplicativo. Ela também ajuda a escolher um camada de preços mais adequada para seu aplicativo.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte? 
As camadas de preços S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. Antes de escolher uma camada de preços do Azure Mapas, faça algumas perguntas a si mesmo. Um exemplo é "a quantos usuários simultâneos planejo oferecer suporte?" A camada de preços S0 lida com até **50 consultas por segundo**. A camada de preços S1 lida com **mais de 50 consultas por segundo**.

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Recurso                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Pesquisa (fwd/rev geocodificação, pontos de interesse)  |        ✓           |     ✓    |
| Geocodificação de lote (versão prévia)              |                   |     ✓    |
| Polígonos de pesquisa          |                   |     ✓    |
| Roteamento                                 |        ✓           |     ✓    |
| Intervalo de rotas                    |                   |     ✓    |
| Roteamento de lote (versão prévia)                |                   |     ✓    |
| Roteamento de matriz (versão prévia)               |                   |     ✓    |
| Renderizar                                  |        ✓           |     ✓    |
| Imagens + imagens híbridas    |            |     ✓    |
| Tráfego                                 |        ✓           |     ✓    |
| Fusos horários                              |        ✓           |     ✓    |
| Geolocalização (versão prévia)                |        ✓           |     ✓    |
| Dados (versão prévia)               |                   |     ✓    |
| Espacial (versão prévia)               |                   |     ✓    |
| Delimitação geográfica (versão prévia)               |                   |     ✓    |



Vale a pena considerar esses pontos de dados adicionais:
* Que tipo de empreendimento você tem?
* Qual a importância do aplicativo que está sendo desenvolvido?

Consulte a tabela com **Clientes da camada de preços** para obter uma noção melhor das camadas de preços S1 e S0. Para saber mais, consulte [Preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

| Tipo de preço  |     Clientes de destino                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 é para clientes que são as empresas de pequeno ou médio porte. É a camada de preços certa se você não espera altos volumes de usuários simultâneos. Também é a opção correta se as principais APIs geoespaciais mostradas na tabela anterior atendem aos seus requisitos de serviço. Esta camada está disponível para o público em geral. Ela funciona com aplicativos em todos os estágios de produção, desde o desenvolvimento da prova de conceito e o teste inicial até a produção e a implantação do aplicativo.<p>|
| S1            |    <p>A camada de preços S1 serve clientes que precisam oferecer suporte a uma empresa de grande porte, aplicativos essenciais ou altos volumes de usuários simultâneos. Vale também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como exibir e alterar a camada de preços:

> [!div class="nextstepaction"] 
> [Gerenciar uma camada de preços](how-to-manage-pricing-tier.md)
