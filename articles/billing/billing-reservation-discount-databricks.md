---
title: Como um desconto de pré-compra do Azure Databricks é aplicado
description: Saiba como um desconto de pré-compra do Azure Databricks aplica-se ao seu uso.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827632"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Como o desconto de pré-compra do Azure Databricks é aplicado

Você pode usar unidades de confirmação pré-adquiridas Azure Databricks (DBCU) a qualquer momento durante o período de compra. Qualquer uso é do Azure Databricks deduz dos DBCUs pré-adquiridas automaticamente.

Ao contrário das VMs, unidades pré-adquiridas não expirarão por hora. Você pode usá-los a qualquer momento durante o prazo da compra. Para obter os descontos de pré-aquisição, você não precisa reimplantar ou atribuir um plano previamente adquirido para seus espaços de trabalho do Azure Databricks para o uso.

O desconto de pré-aquisição se aplica somente ao uso de uma unidade (DBU) do Azure Databricks. Outros encargos, como computação, armazenamento e rede são cobrados separadamente.

## <a name="pre-purchase-discount-application"></a>Aplicativo pré-aquisição de desconto

Pré-aquisição da Databricks se aplica a todas as camadas e cargas de trabalho do Databricks. Você pode pensar em pré-aquisição como um pool de unidades de confirmação do Databricks pré-pagos. Uso é deduzido do pool, independentemente da camada ou a carga de trabalho. Uso é deduzido na seguinte razão:

| **Carga de trabalho** | **Taxa de aplicativo da DBU – camada Standard** | **Taxa de aplicativo da DBU – camada Premium** |
| --- | --- | --- |
| Análise de dados | 0,4 | 0.55 |
| Engenharia de Dados | 0.15 | 0.30 |
| Engenharia de Dados Leve | 0,07 | 0.22 |

Por exemplo, quando uma quantidade de análise de dados – camada Standard é consumida, as unidades de confirmação de Databricks pré-adquiridas é deduzido pelas unidades de 0,4. Quando uma quantidade de luz de engenharia de dados – camada Standard é usada, a unidade de confirmação de Databricks pré-adquiridas é deduzida pelas unidades 0,07

## <a name="determine-plan-use"></a>Determinar o uso de plano

Para determinar o uso de plano DBCU, vá para o portal do Azure > **reservas** e clique no plano de Databricks adquirido. Sua utilização até a data é exibida com quaisquer unidades restantes. Para obter mais informações sobre como determinar sua reserva usar, consulte o [consulte o uso de reserva](billing-reservation-apis.md#see-reservation-usage) artigo.

## <a name="how-discount-application-shows-in-usage-data"></a>Como o aplicativo de desconto mostra em dados de uso

Quando o desconto de pré-aquisição aplica-se ao seu uso do Databricks, encargos de sob demanda são exibidos como zero nos dados de uso. Para obter mais informações sobre o uso e custos de reserva, consulte [uso e custos de reserva de obter o Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre como comprar o Azure Databricks para economizar dinheiro previamente, consulte [os custos de otimizar o Azure Databricks com um pré-aquisição da](billing-prepay-databricks-reserved-capacity.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](billing-manage-reserved-vm-instance.md)
  - [Entender o uso de reserva para uma assinatura com taxas pagas conforme o uso](billing-understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
