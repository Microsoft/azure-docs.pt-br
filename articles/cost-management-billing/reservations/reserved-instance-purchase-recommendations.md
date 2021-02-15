---
title: Recomendações de reserva do Azure
description: Saiba mais sobre as recomendações de reserva do Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928538"
---
# <a name="reservation-recommendations"></a>Recomendações de reserva

As recomendações de compra de RI (instância reservada) do Azure são fornecidas por meio da [API de Recomendação de Reserva](/rest/api/consumption/reservationrecommendations) e do [Assistente do Azure](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) do Azure Consumption e também por meio da experiência de compra de reserva no portal do Azure.

As seguintes etapas definem como as recomendações são calculadas:

1. O mecanismo de recomendação avalia o uso por hora dos seus recursos no escopo fornecido durante os últimos 7, 30 e 60 dias.
2. Com base nos dados de uso, o mecanismo simula seus custos com e sem reservas.
3. Os custos são simulados para diferentes quantidades e a quantidade que maximiza a economia é recomendada.
4. Se os recursos forem desligados regularmente, a simulação não encontrará nenhuma economia e nenhuma recomendação de compra será fornecida.
5. Os cálculos da recomendação incluem todos os descontos especiais que você possa ter sobre as taxas de uso sob demanda.

## <a name="recommendations-in-the-azure-portal"></a>Recomendações no portal do Azure

As recomendações de compra de reserva também são mostradas no portal do Azure, na experiência de compra. As recomendações são mostradas com a **Quantidade Recomendada**. Ao comprar a quantidade recomendada pelo Azure você obterá o máximo de economia possível. Embora seja possível comprar qualquer quantidade desejada, se você comprar uma quantidade diferente, sua economia não será ideal.

Vejamos alguns exemplos do porquê.

Na imagem de exemplo a seguir para a recomendação selecionada, o Azure recomenda a compra da quantidade igual a 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exemplo mostrando uma recomendação de compra de reserva" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

São exibidas mais informações sobre a recomendação quando você seleciona **Ver detalhes**. A imagem a seguir mostra detalhes sobre a recomendação. A quantidade recomendada é calculada para permitir o maior uso possível e é baseada no seu uso histórico. Sua recomendação poderá não considerar 100% de utilização se você apresentar uso inconsistente. No exemplo, observe que a utilização flutuou ao longo do tempo. São mostrados o custo da reserva, a possível economia proporcionada e o percentual de utilização.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exemplo que mostra detalhes sobre uma recomendação de compra de reserva" :::

O gráfico e os valores estimados mudam quando você aumenta a quantidade recomendada. Ao aumentar a quantidade de reserva, sua economia será reduzida porque você acabará com um menor uso de reserva. Em outras palavras, você pagará por reservas que não serão totalmente usadas.

Se você diminuir a quantidade de reserva, sua economia também será reduzida. Embora você alcance maior utilização, provavelmente haverá períodos em que as reservas não serão suficientes para cobrir totalmente o seu uso. O uso que exceder sua quantidade de reservas será coberto por recursos mais caros pagos conforme o uso. A imagem de exemplo a seguir ilustra esse fato. Reduzimos manualmente a quantidade de reserva para 4. A utilização de reserva é aumentada, mas a economia geral é reduzida porque são gerados custos pagos conforme o uso.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exemplo mostrando detalhes sobre uma recomendação de compra de reserva alterada" :::

Para maximizar a economia com reservas, tente comprar a quantidade mais próxima possível da recomendação.

## <a name="recommendations-in-azure-advisor"></a>Recomendações no Assistente do Azure

Recomendações de compra de reserva estão disponíveis no Assistente do Azure. Tenha em mente os seguintes pontos:

- O Assistente tem apenas recomendações de escopo de assinatura única. Caso deseje ver as recomendações para todo o escopo do orçamento (conta de cobrança ou perfil de cobrança):
  -  No portal do Azure, navegue até **Reservas** > **Adicionar** e selecione o tipo para o qual deseja ver as recomendações.
- As recomendações disponíveis no Assistente consideram sua tendência de uso dos últimos 30 dias.
- A quantidade e a economia das recomendações referem-se a uma reserva de três anos, quando disponível. Se uma reserva de três anos não for vendida para o serviço, a recomendação será calculada usando o preço da reserva de um ano.
- Os cálculos da recomendação incluem todos os descontos especiais que você possa ter sobre as taxas de uso sob demanda.
- Se você comprar uma reserva com escopo compartilhado, as recomendações de compra de reserva do Assistente poderão levar até cinco dias para desaparecer.

## <a name="other-expected-api-behavior"></a>Outro comportamento de API esperado

- Ao usar um período de retrospectiva de sete dias, talvez você não receba recomendações quando as VMs ficarem desligadas por mais de um dia.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como é aplicado o desconto de reserva do Azure a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
