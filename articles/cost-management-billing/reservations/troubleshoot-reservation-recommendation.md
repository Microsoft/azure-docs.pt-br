---
title: Solucionar problemas de recomendações de reserva do Azure
description: Este artigo ajuda você a entender e solucionar problemas das recomendações de reserva do Azure mostradas no portal do Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492221"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Solucionar problemas de recomendações de reserva do Azure

Este artigo ajuda você a entender e solucionar problemas das recomendações de reserva do Azure mostradas no portal do Azure. Talvez você não veja nenhuma recomendação ou veja recomendações que não correspondam às suas expectativas. Por exemplo, talvez você já tenha uma instância reservada para uma configuração de VM específica. Talvez você espere ver uma recomendação para uma configuração de VM semelhante.

## <a name="symptoms"></a>Sintomas

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até **Reservas**.
2. Selecione **+ Adicionar** e escolha um produto.
3. Na guia **Recomendações** , talvez você não veja nenhuma recomendação ou veja recomendações que não correspondam às suas expectativas.

## <a name="cause"></a>Causa

A lista de produtos recomendados é gerada pela quantidade de uso que você tem para seu escopo (compartilhado ou único) em comparação com o custo de uma reserva para o produto. Se o mecanismo de recomendação detectar economia, ele recomendará um produto para compra. No entanto, quando o mecanismo não identificar nenhuma economia, ele não recomendará um produto.

Quando você está executando um recurso com uma determinada configuração, não há garantia de que você economizará dinheiro comprando uma reserva para essa configuração. Você pode ter uso esporádico por exemplo. Nesse caso, o custo total da reserva pode não fazer você economizar dinheiro durante o tempo de vida do período de reserva.

Também é importante entender como a seleção de escopo afeta as recomendações. Quando o escopo é definido como **Compartilhado** , as recomendações na lista mostram as instâncias reservadas em que o Azure encontra economia para todo o registro associado à assinatura para cobrança. Quando o escopo é definido como **Único** , as recomendações na lista se aplicam somente a recursos executados na assinatura. É possível que alguns tamanhos de VM sejam recomendados para um escopo, mas não para outro. Por exemplo, você pode ter o uso agregado de **Standard_B1ls** em seu registro que seja alto o suficiente para justificar o custo da compra de uma reserva no escopo de registro. No entanto, uma assinatura única no registro pode não ter uso suficiente para justificar o custo da compra de uma reserva no escopo. A alteração do escopo entre o **Compartilhado** e **Único** pode produzir recomendações diferentes.

O Azure pode recomendar a compra de uma reserva para determinados termos, e não para outros, com base nas economias de custo identificadas. Especificamente, os prazos de três anos têm descontos maiores do que os prazos de um ano. É mais provável que o Azure encontre economias por um prazo de três anos do que para um prazo de um ano.

Se você quiser entender por que o Azure recomenda um tamanho e uma quantidade específicos de recursos, selecione **&lt;Quantidade&gt; Ver detalhes** para obter uma visualização detalhada mostrando possíveis economias ao longo do tempo.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Exemplo mostrando a recomendação de reserva Link Ver detalhes" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Solução

Você pode comprar qualquer quantidade de reserva para um prazo que desejar. Comprar uma reserva com uma quantidade e um prazo diferente da recomendação provavelmente resultará em uma economia e um uso mais baixos do que os níveis ideais.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre recomendações de reserva, confira [Recomendações de compra de reserva](determine-reservation-purchase.md).
