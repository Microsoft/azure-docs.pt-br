---
title: Economizar com o SAP HANA em Instâncias Grandes com uma reserva do Azure
description: Veja o que você precisa saber antes de comprar uma reserva do HANA em Instâncias Grandes, além de como fazer a compra.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ea1ee2311d8655ce17017c73309bc69e89f4b5f4
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599113"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Economizar com o SAP HANA em Instâncias Grandes com uma reserva do Azure

Você pode economizar custos com o SAP HLI (SAP HANA em Instâncias Grandes) comprando antecipadamente reservas do Azure por um ou por três anos. O desconto pela reserva é aplicado ao SKU do HLI provisionado correspondente à instância reservada comprada. Veja neste artigo o que você precisa saber antes de comprar uma reserva, além de como fazer a compra.

Ao comprar uma reserva, você se compromete com o uso do HLI por um ou por três anos. A compra da capacidade reservada do HLI cobre o armazenamento de NFS e computação que fazem parte do pacote do SKU. A reserva não inclui custos de licenciamento de software, como o sistema operacional e o SAP, nem custos de armazenamento adicionais. O desconto pela reserva se aplica automaticamente ao SAP HLI provisionado. Quando o prazo da reserva termina, tarifas pagas conforme o uso se aplicam ao recurso provisionado.

## <a name="purchase-considerations"></a>Considerações sobre a compra

Um SKU do HLI precisa ser provisionado antes da realização da compra da capacidade reservada. A reserva é paga antecipadamente ou com pagamentos mensais. As seguintes restrições se aplicam à capacidade reservada do HLI:

- Os descontos pela reserva se aplicam somente a assinaturas com o Contrato Enterprise e com o Contrato de Cliente da Microsoft. Não há suporte para outras assinaturas.
- A flexibilidade de tamanho da instância não tem suporte para a capacidade reservada do HLI. A reserva se aplica somente ao SKU e à região para os quais você a adquire.
- Não há suporte para cancelamentos nem para trocas por autoatendimento.
- O escopo da capacidade reservada é único, ou seja, ela se aplica a uma assinatura única e a um só grupo de recursos. A capacidade adquirida não pode ser atualizada para uso por outra assinatura.
- Você não pode ter uma capacidade reservada do HANA com o escopo da reserva compartilhado. Você não pode dividir, mesclar nem atualizar o escopo da reserva.
- Você pode comprar um HLI por vez usando chamadas à API da capacidade reservada. Faça chamadas à API adicionais para comprar quantidades adicionais.

Você pode comprar a capacidade reservada no portal do Azure ou usando a [API REST](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Comprar uma reserva do HANA em Instância Grande

Use as informações a seguir para comprar uma reserva do HLI com as [APIs REST de Pedido de Reserva](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Obter o pedido e o preço da reserva

Primeiro, obtenha as informações sobre o pedido e o preço da reserva do SKU do HANA em Instância Grande provisionado usando a API [Calcular Preço](/rest/api/reserved-vm-instances/reservationorder/calculate).

O exemplo a seguir usa o [armclient](https://github.com/projectkudu/ARMClient) para fazer chamadas à API REST com o PowerShell. O pedido da reserva, bem como a solicitação e o corpo da solicitação à API Calcular Preço devem ter esta aparência:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Para saber mais sobre os campos de dados e suas descrições, confira [Campos de reserva do HLI](#hli-reservation-fields).

A resposta do exemplo a seguir é semelhante ao que é retornado. Observe o valor retornado para `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Fazer a compra

Faça a compra usando a `quoteId` retornada e a `reservationOrderId` obtida na seção anterior, [Obter o pedido e o preço da reserva](#get-the-reservation-order-and-price).

Veja um exemplo de solicitação:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Veja um exemplo de resposta. Se o pedido for realizado com êxito, o `provisioningState` deverá ser `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Verificar o status de êxito da compra

Execute a solicitação GET do pedido de reserva para ver o status da ordem de compra. `provisioningState` deve ser `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

A resposta deve ser semelhante ao exemplo a seguir.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Campos da reserva do HLI

As informações a seguir explicam o significado dos diferentes campos da reserva.

  **SKU** Nome do SKU do HLI. Ele se parece com `SAP_HANA_On_Azure_<SKUname>`.

  **Localização** Regiões do HLI disponíveis. Confira em [SKUs para SAP HANA no Azure (Instâncias Grandes)](../../virtual-machines/workloads/sap/hana-available-skus.md) as regiões disponíveis. Para obter o formato da cadeia de caracteres da localização, use [chamada à API obter localizações](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Tipo de recurso reservado** `SapHana`

  **Assinatura** A assinatura usada para pagar pela reserva. Os custos da reserva são cobrados segundo a forma de pagamento da assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft. Os preços são deduzidos do saldo do Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário), se disponível, ou cobrados como excedente.

  **Escopo** O escopo da reserva deve ser único.

  **Termo** Um ano ou três anos. Ele se parece com `P1Y` ou com `P3Y`.

  **Quantidade** O número de instâncias sendo compradas para a reserva. A quantidade a ser comprada é um HLI por vez. Para reservas adicionais, repita a chamada à API com os campos correspondentes.

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Você poderá receber um erro como o do exemplo a seguir ao comprar uma reserva. A causa possível é que o HLI não foi provisionado para compra. Nesse caso, entre em contato com sua equipe da conta Microsoft para provisionar um HLI antes de tentar comprar uma reserva.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Como chamar APIs REST do Azure com Postman e cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Confira em [SKUs para SAP HANA no Azure (Instâncias Grandes)](../../virtual-machines/workloads/sap/hana-available-skus.md) as lista com os SKUs e as regiões disponíveis.