---
title: Comprar uma reserva do Azure
description: Conheça pontos importantes para ajudar você a comprar uma reserva do Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b03db9632f5f47a7600c5b9037d16b1cff6ccf69
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344242"
---
# <a name="buy-a-reservation"></a>Comprar uma reserva

As reservas do Azure ajudam você a economizar dinheiro confirmando os planos de um ou de três anos para muitos recursos do Azure. Antes de inserir um compromisso para comprar uma reserva, é preciso que você examine as seções a seguir para se preparar para sua compra.

## <a name="who-can-buy-a-reservation"></a>Quem pode comprar uma reserva

Para comprar uma reserva, você precisa ter a função proprietário ou comprador de reserva em uma assinatura do Azure que é do tipo Enterprise (MS-AZR-0017P ou MS-AZR-0148P), Pagamento Conforme o Uso (MS-AZR-0003P ou MS-AZR-0023P) ou Contrato de Cliente da Microsoft. Os provedores de solução na nuvem podem usar o portal do Azure ou o  [Partner Center](/partner-center/azure-reservations)  para comprar Reservas do Azure.

Clientes com EA (Contrato Enterprise) podem limitar as compras aos administradores de EA desabilitando a opção **Adicionar Instâncias Reservadas** no Portal do EA. Os administradores do EA precisam ter acesso de proprietário ou de comprador de reserva em pelo menos uma assinatura do EA para comprar uma reserva. A opção é útil para empresas que desejam que uma equipe centralizada compre reservas.

Um desconto de reserva aplica-se somente aos recursos associados a assinaturas adquiridas por meio do Enterprise, do CSP (Provedor de Soluções de Nuvem), Contrato de Cliente da Microsoft e planos individuais com tarifas pagas conforme o uso.

## <a name="scope-reservations"></a>Escopo das reservas

Você pode definir o escopo de uma reserva como uma assinatura ou como grupos de recursos. Definir o escopo de uma reserva seleciona o local a que a economia da reserva se aplica. Quando você define o escopo da reserva como um grupo de recursos, os descontos da reserva aplicam-se somente ao grupo de recursos, não à assinatura inteira.

### <a name="reservation-scoping-options"></a>Opções de escopo de reserva

Você tem três opções para definir o escopo de uma reserva, dependendo de suas necessidades:

- **Escopo de grupo de recursos único** – aplica o desconto da reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
- **Escopo de assinatura única** – aplica o desconto da reserva apenas aos recursos correspondentes na assinatura selecionada.
- **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança.
    - Para clientes do Contrato Enterprise, o contexto de cobrança é o registro. O escopo compartilhado da reserva incluiria vários locatários do Active Directory em um registro.
    - Para clientes do Contrato de Cliente da Microsoft, o escopo do orçamento é o perfil de cobrança.
    - Para assinaturas individuais com tarifas pagas conforme o uso, o escopo do orçamento são todas as assinaturas qualificadas criadas pelo administrador da conta.

Ao aplicar descontos de reserva ao seu uso, o Azure processa a reserva na seguinte ordem:

1. Reservas que estão no escopo de um grupo de recursos
2. Reservas de escopo único
3. Reservas de escopo compartilhado

Você sempre pode atualizar o escopo depois de comprar uma reserva. Para fazer isso, vá para a reserva, clique em **Configuração** e redefina o escopo da reserva. A redefinição do escopo de uma reserva não é uma transação comercial. O termo da reserva não é alterado. Para saber mais sobre como atualizar o escopo, confira [Atualizar o escopo depois de comprar uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo mostrando uma alteração de escopo de reserva](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Assinatura com desconto e tipos de oferta

Os descontos na reserva aplicam-se às seguintes assinaturas e tipos de oferta elegíveis.

- Enterprise Agreement (números da oferta: MS-AZR-0017P ou MS-AZR-0148P)
- Assinaturas do Contrato de Cliente da Microsoft.
- Planos individuais com tarifas de pagamento conforme o uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P)
- Assinaturas de CSP

Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="purchase-reservations"></a>Comprar reservas

Você pode comprar reservas por meio do portal do Azure, das APIs, do PowerShell e da CLI. Leia os seguintes artigos, que se aplicam a você quando você está pronto para fazer uma compra de reserva:

- [Serviço de Aplicativo](prepay-app-service-isolated-stamp.md)
- [Cache Redis do Azure](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Armazenamento em Disco](../../virtual-machines/disks-reserved-capacity.md)
- [Host Dedicado](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Planos de software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Banco de Dados SQL](../../azure-sql/database/reserved-capacity-overview.md)
- [Banco de Dados do Azure para PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Banco de Dados do Azure para MySQL](../../mysql/concept-reserved-pricing.md)
- [Banco de Dados do Azure para MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Máquinas virtuais](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Você pode pagar por elas fazendo pagamentos mensais. Diferente de uma compra antecipada em que você paga o valor total, a forme de pagamento mensal divide o custo total da reserva uniformemente em cada mês do termo. O custo total das reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente.

Se a reserva for comprada usando o MCA (Contrato de Cliente da Microsoft), o valor do pagamento mensal poderá variar de acordo com o câmbio da moeda local do mercado para o mês corrente.

Os pagamentos mensais não estão disponíveis para: Databricks, reservas do SUSE Linux, Planos do Red Hat e Licenças do Red Hat OpenShift no Azure.

### <a name="view-payments-made"></a>Exibir pagamentos feitos

Você pode exibir os pagamentos que foram feitos usando APIs, dados de uso e análise de custo. Para reservas pagas mensalmente, o valor de frequência é mostrado como **recorrente** nos dados de uso e na API de Preços de Reserva. Para reservas pagas antecipadamente, o valor mostrado é **avulso**.

A análise de custo mostra as compras mensais na exibição padrão. Aplique o filtro de **compra** para o **Tipo de preço** e **recorrente** para a **Frequência** para ver todas as compras. Para exibir somente as reservas, aplique um filtro para **Reserva**.

![Exemplo mostrando os custos de compra da reserva na análise de custo](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Trocas e reembolsos

Assim como outras reservas, você pode reembolsar ou trocar reservas adquiridas com cobrança mensal. 

Quando você troca uma reserva paga mensalmente, o custo total do tempo de vida da nova compra deve ser maior do que os pagamentos restantes cancelados referentes à reserva devolvida. Não há outros limites ou taxas para trocas. Você pode trocar uma reserva paga antecipadamente para comprar uma nova reserva cobrada mensalmente. No entanto, o valor do tempo de vida da nova reserva deve ser maior que o valor rateado da reserva que está sendo devolvida.

Se você cancelar uma reserva paga mensalmente, os pagamentos futuros cancelados se acumularão até o limite de reembolso de US$ 50.000.

Para saber mais sobre trocas e reembolsos, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Notificações de reserva

Dependendo de como você paga por sua assinatura do Azure, são enviadas notificações de reserva por email aos usuários de sua organização a seguir. São enviadas notificações referentes a vários eventos, incluindo: 

- Purchase
- Futura expiração da reserva
- Expiry
- Renovação
- Cancelamento
- Alteração de escopo

Para clientes com assinaturas EA:

- São enviadas notificações somente para os contatos de notificação do EA.
- Usuários adicionados a uma reserva usando a permissão do Azure RBAC (IAM) não recebem notificação por email.

Para clientes com assinaturas individuais:

- O comprador recebe uma notificação de compra.
- No momento da compra, o proprietário da conta de cobrança da assinatura recebe uma notificação de compra.
- O proprietário da conta recebe todas as outras notificações.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar reservas para recursos do Azure](manage-reserved-vm-instance.md)