---
title: "Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba mais sobre o gráfico de tendência de custo estimado mensal do Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs
O recurso de gerenciamento de custos dos Laboratórios de Desenvolvimento/Teste ajuda a rastrear o custo de laboratório. Este artigo ilustra como usar o gráfico **Tendência de custo estimado mensal** para exibir o custo estimado até a data do mês do calendário atual e o custo projetado do final do mês para o mês do calendário atual. Neste artigo, você aprenderá a exibir o gráfico de tendência de custo estimado mensal no portal do Azure.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizando o gráfico Tendência de custo estimado mensal
Para exibir o gráfico Tendência de custo estimado mensal, siga estas etapas: 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Na folha do laboratório, selecione **Configurações de custo**.
5. Na folha **Configurações de custo** do laboratório, selecione **Tendência de custo do laboratório**.
6. A captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O valor **Custo estimado** é o custo estimado até a data do mês do calendário atual. O **Custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório nos cinco dias anteriores.

Observe que os valores do custo são arredondados para o próximo número inteiro. Por exemplo: 

* 5.01 arredonda até 6 
* 5.50 arredonda até 6
* 5.99 arredonda até 6

Como é indicado acima do gráfico, os custos vistos no gráfico são custos *estimados* usando as tarifas da oferta [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) .
Além disso, o seguinte *não* está incluído no cálculo de custo:

* As assinaturas do CSP e do Dreamspark não têm suporte atualmente, pois o Azure DevTest Labs usa as [APIs de cobrança do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o custo do laboratório e essas APIs não dão suporte a assinaturas do Dreamspark ou do CSP.
* Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Estamos usando as tarifas pré-pagas.
* Seus impostos
* Seus descontos
* Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Mais duas coisas para manter o custo sob controle em laboratórios de DevTest](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Por que os limites de custo?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Próximas etapas
Eis aqui algumas coisas para experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) — os DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm-with-artifacts.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

