---
title: Escolha o tipo de preço certo para mapas de Microsoft Azure
description: Saiba mais sobre os tipos de preço do Azure Maps. Veja quais recursos são oferecidos em quais camadas e veja as principais considerações para escolher um tipo de preço.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905291"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha a camada de preços certa no Azure Mapas

O mapas do Azure oferece dois tipos de preço: S0 e S1. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para escolher o tipo de preço certo, faça a si mesmo as duas perguntas a seguir.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte?

As camadas de preços S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. A camada de preços S0 lida com até **50 consultas por segundo**. Enquanto a camada S1 lida com **mais de 50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?

Se as APIs geoespaciais principais atenderem aos seus requisitos de serviço, escolha o tipo de preço S0. Se você quiser recursos mais avançados para seu aplicativo, considere escolher o tipo de preço S1. Os recursos avançados incluem: imagens aéreas e híbridas, obter intervalo de rotas e geocodificação em lote. Para selecionar o tipo de preço mais adequado para seu aplicativo, examine a tabela **capacidades de tipo de preço** abaixo:

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Recurso                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização de mapa                              | ✓                   | ✓       |
| Imagens satélite                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Pesquisa em lotes                            |                     | ✓        |
| Rota                                   | ✓                    |✓        |
| Roteamento do lote                            |                    | ✓        |
| Roteamento de matriz                          |                     | ✓        |
| Intervalo de rotas (isócronas)                |                     | ✓        |
| Tráfego                                |✓                    |✓        |
| Fuso horário                               |✓                    |✓        |
| Localização geográfica (versão prévia)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Delimitação geográfica                                |                    |✓        |
| Dados do Azure Maps (versão prévia)                |                     | ✓        |
| Mobilidade (visualização)                       |                     | ✓        |
| Clima (visualização)                        |✓                    |✓        |
|  Criador (versão prévia)                         |                   |✓        |
|  Elevação (versão prévia)                        |                   |✓        |

Considere estes pontos adicionais:

* Que tipo de empresa você tem?
* Quão crítico é seu aplicativo?

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

Consulte a tabela com **Clientes da camada de preços** para obter uma noção melhor das camadas de preços S1 e S0. Para saber mais, consulte [Preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Tipo de preço  |     Clientes de destino                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    O tipo de preço S0 funciona para aplicativos em todos os estágios de produção: desde desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos. No entanto, essa camada foi projetada para desenvolvimento de pequena escala ou para clientes com poucos usuários simultâneos, ou ambos. 
| S1            |    O tipo de preço S1 é para clientes com aplicativos empresariais de grande escala, aplicativos de missão crítica ou grandes volumes de usuários simultâneos. Vale também para os clientes que necessitam de serviços geoespaciais avançados.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como exibir e alterar a camada de preços:

> [!div class="nextstepaction"]
> [Gerenciar uma camada de preços](how-to-manage-pricing-tier.md)
