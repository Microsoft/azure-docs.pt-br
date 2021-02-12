---
title: APIs para a automação de reserva do Azure | Microsoft Docs
description: Saiba mais sobre as APIs do Azure que você pode usar para obter programaticamente informações de reserva.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 773334787ec7b2706c16e517281d6a60215ad482
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353010"
---
# <a name="apis-for-azure-reservation-automation"></a>APIs para a automação de reserva do Azure

Use as APIs do Azure para obter programaticamente informações para sua organização sobre reservas de software ou serviço do Azure.

## <a name="find-reservation-plans-to-buy"></a>Localizar os planos de reserva para comprar

Use a API de recomendação de Reserva para obter recomendações sobre quais reservas você planeja comprar com base no uso da sua organização. Para obter mais informações, confira [Obter recomendações de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Você também pode analisar o uso de recursos usando os Detalhes de Uso de API de Consumo. Para obter mais informações, confira [Detalhes de uso – lista para período de cobrança por conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy). Os recursos do Azure que você usa consistentemente geralmente são os melhores candidatos para uma reserva.

## <a name="buy-a-reservation"></a>Comprar uma reserva

É possível comprar planos de software e reservas do Azure programaticamente usando APIs REST. Para obter mais informações, confira [Pedido de Reserva – API de Compra](/rest/api/reserved-vm-instances/reservationorder/purchase).

Veja um exemplo de solicitação para comprar usando a API REST:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Corpo da solicitação:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Também é possível comprar uma reserva no portal do Azure. Para obter mais informações, consulte os seguintes artigos:

Planos de serviço:
- [Máquina virtual](../../virtual-machines/prepay-reserved-vm-instances.md?toc=%2fazure%2fbilling%2fTOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Banco de Dados SQL](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

Planos de software:
- [Software SUSE Linux](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Obter reservas

Se você for um cliente do Azure com um Contrato Enterprise (cliente EA), poderá obter as reservas que sua organização comprou usando [Obter API de encargos de transação de Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Para outras assinaturas, obtenha a lista de reservas que você comprou e tem permissões para exibir usando a API [Pedido de Reserva – Lista](/rest/api/reserved-vm-instances/reservationorder/list). Por padrão, o proprietário da conta ou a pessoa que comprou a reserva tem permissões para exibir a reserva.

## <a name="see-reservation-usage"></a>Confira o uso de reserva

Se você for um cliente EA, poderá exibir programaticamente como as reservas em sua organização estão sendo usadas. Para obter mais informações, confira [Obter Uso da Instância Reservada para clientes Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Para outras assinaturas, use a API [Resumos de reservas – lista por reserva e ordem de reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Se você descobrir que as reservas de sua organização estiverem sendo subutilizadas:

- Verifique se as máquinas virtuais que sua organização cria correspondem ao tamanho da VM que está na reserva.
- Verifique se a flexibilidade de tamanho da instância está ligada. Para obter mais informações, confira [Gerenciar reservas – alterar configuração de otimização para Instâncias de VM Reservadas](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Altere o escopo da reserva para compartilhado de modo que ele se aplique mais amplamente. Para obter mais informações, confira [Gerenciar reservas – altere o escopo para uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).
- Trocar a quantidade não usada. Para obter mais informações, confira [Gerenciar reservas](manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Dar acesso às reservas

Obtenha a lista de todas as reservas às quais um usuário tem acesso usando a [API Reserva – Operação – Lista](/rest/api/reserved-vm-instances/reservationorder/list). Para conceder acesso a uma reserva de modo programático, leia um dos seguintes artigos:

- [Adicionar ou remover atribuições de função do Azure usando a API REST](../../role-based-access-control/role-assignments-rest.md)
- [Adicionar ou remover atribuições de função do Azure usando o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dividir ou mesclar reserva

Depois de comprar mais de uma instância de recurso em uma reserva, é recomendável atribuir instâncias nessa reserva a assinaturas diferentes. Você pode alterar o escopo da reserva de modo que ela se aplique a todas as assinaturas no mesmo contexto de cobrança. Porém, para fins de gerenciamento de custos ou orçamento, você talvez queira manter o escopo como "assinatura única" e atribuir as instâncias de reserva a uma assinatura específica.

Para dividir uma reserva, use a API [Reserva – Dividir](/rest/api/reserved-vm-instances/reservation/split). Você também pode dividir uma reserva usando o PowerShell. Para obter mais informações, confira [Gerenciar reservas – dividir reserva em duas reservas](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Para mesclar duas reservas em uma reserva, use a API [Reserva – Mesclagem](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Alterar o escopo de uma reserva

O escopo de uma reserva pode ser a assinatura única, grupo de recursos único ou todas as assinaturas de seu contexto de cobrança. Se você definir o escopo como assinatura única ou grupo de recursos único, a reserva será correspondida aos recursos em execução na assinatura selecionada. Se você excluir ou mover a assinatura ou o grupo de recursos, a reserva não será utilizada.  Se você definir o escopo compartilhado, o Azure corresponderá a reserva de recursos que são executados em todas as assinaturas no contexto de cobrança. O contexto de cobrança depende da assinatura usada para comprar a reserva. É possível selecionar o escopo na compra ou alterá-lo a qualquer momento após a compra. Para obter mais informações, confira [Gerenciar Reservas – Alterar o escopo](manage-reserved-vm-instance.md#change-the-reservation-scope).

Para alterar o escopo de modo programático, use a API [Reserva – Atualização](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Saiba mais

- [Quais são as reservas para o Azure](save-compute-costs-reservations.md)
- [Entender como o desconto de reserva de VM é aplicado](../manage/understand-vm-reservation-charges.md)
- [Entender como o desconto do plano de software do SUSE Linux Enterprise é aplicado](understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)