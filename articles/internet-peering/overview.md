---
title: Configurar o emparelhamento com a Microsoft
titleSuffix: Azure
description: Visão geral do emparelhamento
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586859"
---
# <a name="internet-peering-overview"></a>Visão geral do emparelhamento pela Internet

O emparelhamento é a interconexão entre a rede global da Microsoft (AS8075) e a sua rede para fins de troca de tráfego entre a Internet e os serviços online da Microsoft e os serviços do Microsoft Azure. As operadoras ou os provedores de serviços podem solicitar a conexão com a Microsoft em qualquer um de nossas localizações de borda. Cada solicitação é examinada pela Microsoft para garantir que ela atenda à nossa política de emparelhamento. Você pode configurar um emparelhamento com a rede da Microsoft de duas maneiras:

* **Emparelhamento direto:**

    O emparelhamento é estabelecido em conexões físicas diretas entre a rede da Microsoft em um Microsoft Edge e a sua rede. As sessões de BGP são configuradas entre essas conexões segundo a nossa política de roteiros e usando um contrato previamente negociado. Isso também é chamado de PNI.

* **Emparelhamento de troca:**

    Isso se refere a conexões de emparelhamento público padrão em TT (trocas de tráfego). As conexões físicas entre a rede da Microsoft e a sua rede são por meio da comutação de malha operada pela troca de tráfego. As sessões BGP são configuradas usando o espaço de endereços IP fornecido pela troca de tráfego.

## <a name="benefits-of-peering-with-microsoft"></a>Benefícios do emparelhamento com a Microsoft
* Reduza os seus custos de trânsito de dados, entregando tráfego à Microsoft usando o emparelhamento com a Microsoft.
* Aprimore o desempenho para seus clientes reduzindo os saltos de rede e a latência para a rede do Microsoft Edge.
* Proteja o tráfego do cliente contra falhas na rede do seu provedor de rede ou de trânsito, emparelhando com a Microsoft em locais redundantes.
* Aprenda métricas de desempenho sobre suas conexões de emparelhamento e utilize os insights para solucionar problemas de sua rede.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Benefícios de usar o Azure para configurar o emparelhamento

Você pode solicitar o emparelhamento com a Microsoft usando Azure PowerShell ou portal. Um emparelhamento configurado dessa maneira é gerenciado como um recurso do Azure e oferece os seguintes benefícios:
* Etapas simplificadas e automatizadas para configurar e gerenciar o emparelhamento com a Microsoft.
* Maneira rápida e fácil de exibir e gerenciar todos os seus emparelhamentos em um só lugar.
* Acompanhar os dados de status e de largura de banda de todas as suas conexões.
* Você pode usar a mesma assinatura para acessar os seus Serviços de Nuvem do Azure.

Se você já estabeleceu emparelhamentos com a Microsoft, eles são chamados **emparelhamentos herdados**. Você pode optar por gerenciar tais emparelhamentos como recursos do Azure para aproveitar os benefícios acima. Para enviar uma nova solicitação de emparelhamento ou converter o emparelhamento herdado em um recurso do Azure, siga os links na seção **Próximas etapas** abaixo.

## <a name="peering-policy"></a>Política de emparelhamento
A Microsoft tem uma política de emparelhamento seletiva, mas em linhas gerais, aberta. Os pares são selecionados com base no desempenho, na capacidade e no local em que há um benefício mútuo e estão sujeitos a determinados requisitos técnicos, comerciais e legais. Para obter detalhes, confira a [política de emparelhamento](policy.md).

## <a name="faq"></a>Perguntas frequentes
Para perguntas frequentes sobre emparelhamento, confira [Emparelhamento de Internet – perguntas frequentes](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as etapas para configurar o emparelhamento direto com a Microsoft, siga o [Passo a passo do emparelhamento direto](walkthrough-direct-all.md)
* Para saber mais sobre as etapas para configurar o emparelhamento de troca com a Microsoft, siga o [Passo a passo do emparelhamento direto](walkthrough-exchange-all.md)
* Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.