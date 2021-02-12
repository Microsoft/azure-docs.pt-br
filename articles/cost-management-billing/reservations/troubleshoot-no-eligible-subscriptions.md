---
title: Solução de problemas de ausência de assinaturas qualificadas no portal do Azure
description: Este artigo ajuda você a solucionar o problema da mensagem de erro "Nenhuma assinatura qualificada" no portal do Azure quando é feita uma tentativa de compra de uma reserva.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.openlocfilehash: ad85bd278b5dff1532f218acc0b8e88515d96070
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561198"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Solução de problemas de ausência de assinaturas qualificadas

Este artigo ajuda você a solucionar o problema da mensagem de erro *Nenhuma assinatura qualificada* no portal do Azure quando é feita uma tentativa de compra de uma reserva.

## <a name="symptoms"></a>Sintomas

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Reservas**.
1. Selecione **Adicionar** e escolha um serviço.
1. Se você vir a seguinte mensagem de erro:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. Na área **Selecione o produto que deseja comprar**, expanda a lista **Assinatura para cobrança** para ver o motivo pelo qual uma assinatura específica não está qualificada para comprar uma instância reservada. A imagem a seguir mostra exemplos de motivos pelos quais uma reserva não pode ser comprada.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Exemplo mostrando os motivos pelos quais uma reserva não pode ser comprada" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Causa

Para comprar uma instância reservada do Azure, você precisa ter, pelo menos, uma assinatura que atenda aos seguintes requisitos:

- A assinatura precisa ser de um tipo de oferta compatível. Os tipos de ofertas compatíveis são: Pagamento conforme o uso, CSP (Provedor de Soluções de Nuvem), Microsoft Azure Enterprise ou Contrato de Cliente da Microsoft.
- Você precisa ser um proprietário ou comprador de reserva na assinatura.

Quando você não tiver uma assinatura que atenda aos requisitos, receberá o erro `No eligible subscriptions`.

### <a name="cause-1"></a>Causa 1

A assinatura precisa ser de um tipo de oferta compatível. Os tipos de ofertas compatíveis são: Pagamento conforme o uso, CSP, Microsoft Azure Enterprise ou Contrato de Cliente da Microsoft. O tipo de assinatura não é compatível. Quando você seleciona uma assinatura que tem um tipo de oferta que não dá suporte a reservas, o erro a seguir é exibido.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Exemplo mostrando a mensagem de erro &quot;Assinatura não qualificada para compra&quot;" :::

### <a name="cause-2"></a>Causa 2

Você precisa ser um proprietário ou comprador de reserva na assinatura. Quando você não tem permissões suficientes, você vê o erro a seguir.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Solução

- Se a sua oferta atual não der suporte a reservas, você precisará criar uma assinatura do Azure.
- Caso não tenha acesso a uma reserva existente, obtenha acesso a ela por meio do proprietário atual.

### <a name="solution-1"></a>Solução 1

Para comprar uma reserva, você precisará criar uma assinatura do Azure que dê suporte a reservas.

- Caso você tenha uma avaliação gratuita do Azure, [atualize sua assinatura](../manage/upgrade-azure-subscription.md).
- Você pode criar uma assinatura do Azure com [taxas de pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Inscreva-se em um [Contrato de Cliente da Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) e crie uma assinatura.
- Compre uma nova assinatura com um [CSP](https://www.microsoft.com/solution-providers/home) e crie uma assinatura.

### <a name="solution-2"></a>Solução 2

Para obter acesso de proprietário a uma reserva, você precisará obter acesso ao seguinte:

- O pedido de reserva com o qual a reserva foi comprada
- A reserva em si

O proprietário do pedido de reserva atual ou o proprietário da reserva podem delegar acesso a você usando as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
1. Selecione a reserva que deseja delegar acesso a outros usuários.
1. Selecione **IAM (Controle de acesso)** .
1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Ou, se você quiser conceder acesso limitado, selecione uma função diferente.
1. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
1. Selecione o usuário e, em seguida, selecione **Salvar**.

Para obter mais informações, consulte [Adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="next-steps"></a>Próximas etapas

- Examine [Adicionar ou alterar usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default) se precisar que um proprietário de reserva permita acesso a você.
