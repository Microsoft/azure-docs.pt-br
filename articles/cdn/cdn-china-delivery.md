---
title: Fornecimento de conteúdo da China com a Rede de Distribuição de Conteúdo do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre como usar a Rede de Distribuição de Conteúdo do Microsoft Azure (CDN) para entregar conteúdo aos usuários da China.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 599ec041837460c30b4655531b822eab5f0eafa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778907"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Fornecimento de conteúdo da China com a Rede de Distribuição de Conteúdo do Microsoft Azure

A Rede de Distribuição de Conteúdo (CDN) global do Microsoft Azure pode enviar conteúdo aos usuários da China com os locais de ponto de presença (POP) próximo à China ou qualquer POP que fornece o melhor desempenho para solicitações provenientes da China. No entanto, se a China é um mercado significativo para os clientes e eles precisam de desempenho rápido, considere usar a CDN do Microsoft Azure China em vez disso.

A CDN do Microsoft difere da CDN do Microsoft Azure global que entrega conteúdo dos POPs dentro da China através da parceria com um número de fornecedores locais. Devido a normas e regulamentações chinesas, você deve registrar uma assinatura separada para usar a CDN do Microsoft Azure China e seus website precisam ter uma licença ICP. O portal e a experiência de API habilitam e gerenciam a distribuição de conteúdo idêntica entre a CDN do Microsoft Azure global e CDN do Microsoft Azure China.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Comparação da CDN do Microsoft Azure global e CDN do Microsoft Azure China

A CDN do Microsoft Azure e a CDN do Microsoft Azure Chine têm os seguintes recursos:

- CDN do Microsoft Azure global:

     - Portal: https://portal.azure.com  

     - Executa o fornecimento de conteúdo fora da China

     - Quatro camadas de preços: Microsoft standard, Verizon standard, Verizon premium e Akamai standard

     - [Documentação](./index.yml)

- Rede de Distribuição de Conteúdo do Microsoft Azure China:

     - Portal: https://portal.azure.cn

     - Executa o fornecimento de conteúdo dentro da China

     - Duas camadas de preço: standard e premium

     - [Documentação](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a CDN do Microsoft Azure China, veja:

- [Diretrizes da Rede de Distribuição de Conteúdo](https://www.azure.cn/en-us/home/features/cdn/)

- [Visão geral da Rede de Distribuição de Conteúdo do Microsoft Azure](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Use a Rede de Distribuição de Conteúdo do Microsoft Azure](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Disponibilidade do serviço do Microsoft Azure na China](/azure/china/concepts-service-availability)