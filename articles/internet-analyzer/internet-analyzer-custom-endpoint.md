---
title: Criar um ponto de extremidade personalizado | Microsoft Docs
description: Neste artigo, saiba como configurar um ponto de extremidade personalizado para ser medido com o recurso Analisador de Internet.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744078"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Meça pontos de extremidade personalizados para avaliar nos testes do Analisador de Internet 

Este artigo demonstra como configurar um ponto de extremidade personalizado para ser medido como parte dos testes do Analisador de Internet. Os pontos de extremidade personalizados ajudam a avaliar cargas de trabalho locais, cargas de trabalho em execução em outros provedores de nuvem e configurações personalizadas do Azure.  A comparação de dois pontos de extremidade personalizados em um único teste é possível se um deles é um recurso do Azure. Para obter mais informações sobre o Analisador de Internet, confira a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

Verifique se você configurou um recurso do Analisador de Internet e selecione a opção "Ponto de Extremidade Personalizado". O Analisador de Internet pressupõe que seu ponto de extremidade personalizado seja acessível à Internet. Para obter mais informações, confira [Criar um recurso do Analisador de Internet](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Criar um Ponto de Extremidade Personalizado

1. Baixe uma imagem de teste transparente de um pixel [aqui](https://fpc.msedge.net/apc/trans.gif). Essa imagem de um pixel é o ativo que o JavaScript do cliente buscará para medir o desempenho.
2. Em seu aplicativo Web personalizado, implante a imagem de teste em um caminho publicamente acessível. O caminho deve funcionar em HTTPS. 
3. Copie a URL completa do ponto de extremidade personalizado (por exemplo, `https://contoso.com/test/trans.gif`) no campo ponto de extremidade personalizado durante a criação do teste.

## <a name="next-steps"></a>Próximas etapas

Leia as [Perguntas frequentes do Analisador de Internet](internet-analyzer-faq.md)

