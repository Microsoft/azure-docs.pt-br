---
title: Otimizar os custos do Azure Databricks com um pré-aquisição da
description: Saiba como você pode pagar antecipadamente pelas cobranças do Azure Databricks com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811252"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Otimizar os custos do Azure Databricks com um pré-aquisição da

Você pode salvar no seu Azure Databricks os custos da unidade (DBU) quando você compra o Azure Databricks previamente confirmar unidades (DBCU) para um ou três anos. Você pode usar os DBCUs previamente adquiridas a qualquer momento durante o período de compra. Ao contrário das VMs, as unidades pré-adquiridas não expirarão por hora e usá-los a qualquer momento durante o prazo da compra.

Qualquer uso do Azure Databricks deduz das DBUs pré-adquiridas automaticamente. Você não precisa reimplantar ou atribuir um plano previamente adquirido para seus espaços de trabalho do Azure Databricks para o uso DBU obter os descontos de pré-aquisição.

O desconto de pré-aquisição se aplica somente ao uso DBU. Outros encargos, como computação, armazenamento e rede são cobrados separadamente.

## <a name="determine-the-right-size-to-buy"></a>Determinar o tamanho certo para comprar

Pré-aquisição da Databricks se aplica a todas as camadas e cargas de trabalho do Databricks. Você pode pensar em pré-aquisição como um pool de unidades de confirmação do Databricks pré-pagos. Uso é deduzido do pool, independentemente da camada ou a carga de trabalho. Uso é deduzido na seguinte razão:

| **Carga de trabalho** | **Taxa de aplicativo da DBU – camada Standard** | **Taxa de aplicativo da DBU – camada Premium** |
| --- | --- | --- |
| Análise de dados | 0,4 | 0.55 |
| Engenharia de Dados | 0.15 | 0.30 |
| Engenharia de Dados Leve | 0,07 | 0.22 |

Por exemplo, quando uma quantidade de análise de dados – camada Standard é consumida, as unidades de confirmação de Databricks pré-adquiridas é deduzido pelas unidades de 0,4.

Antes de comprar, calcule a quantidade total de DBU consumida pelas camadas e diferentes cargas de trabalho. Use as proporções anteriores para normalizar a DBCU e, em seguida, execute uma projeção de utilização total pela próxima ou três anos.

## <a name="purchase-databricks-commit-units"></a>Unidades do Databricks confirmação de compra

Você pode comprar planos de Databricks na [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Para comprar capacidade reservada, você deve ter a função de proprietário para a assinatura de pelo menos uma empresa.

- Pré-compra no momento, só está disponível para clientes do Enterprise Agreement.
- Você deve estar em uma função de proprietário pelo menos uma assinatura do Enterprise.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA da assinatura.

**Para comprar:**

1. Vá para o [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selecione uma assinatura. Use o **assinatura** lista para selecionar a assinatura que é usada para pagar pela capacidade reservada. O método de pagamento da assinatura é cobrado os custos iniciais para a capacidade reservada. Os encargos são deduzidos do saldo de compromisso monetário do registro ou cobrados como excedentes.
1. Selecione um escopo. Use o **escopo** lista para selecionar um escopo de assinatura:
    - **Um único escopo de grupo de recursos** — se aplica o desconto de reserva para os recursos correspondentes no somente o grupo de recursos selecionado.
    - **Um único escopo de assinatura** — se aplica o desconto de reserva para os recursos correspondentes na assinatura selecionada.
    - **Escopo compartilhado** — se aplica o desconto de reserva a correspondência de recursos em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Enterprise Agreement, o contexto de cobrança é o registro.
1. Selecione quantas unidades de confirmação Azure Databricks que você deseja comprar e concluir a compra.


![Exemplo mostrando a compra do Azure Databricks no portal do Azure](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Alterar o escopo e a propriedade

Você pode fazer os seguintes tipos de alterações de uma reserva depois da compra:

- Atualizar o escopo de reserva
- Acesso baseado em função

Você não pode dividir ou mesclar o Databricks confirmação unidade pré-aquisição. Para obter mais informações sobre como gerenciar reservas, consulte [gerenciar reservas após a compra](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Cancelar e o exchange não tem suporte para planos de pré-aquisição do Databricks. Todas as compras são definitivas.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Entender como um desconto DBCU de pré-compra do Azure Databricks é aplicado](billing-reservation-discount-databricks.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
