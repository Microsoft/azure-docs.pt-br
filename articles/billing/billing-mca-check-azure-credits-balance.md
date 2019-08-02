---
title: Acompanhar o saldo de crédito do Azure para um contrato de cliente da Microsoft
description: Saiba como verificar o saldo de crédito do Azure para um contrato de cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490958"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Acompanhar o saldo de crédito do contrato do cliente Microsoft Azure

Você pode verificar o saldo de crédito do Azure para o acordo de cliente da Microsoft no portal do Azure. Você pode usar créditos para pagar pelos encargos que são cobertos pelos créditos.

Você é cobrado quando você usa os produtos que não são cobertos pelos créditos ou seu uso excede o saldo de crédito. Para obter mais informações, veja [produtos que não são cobertos pelo créditos do Azure. ( #products-that-aren't-covered-by-azure-credits).

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Verificar seu saldo de crédito

1. Entre no [Portal do Azure]( https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

    ![Captura de tela que mostra a pesquisa no portal de gerenciamento de custos + cobrança](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **créditos do Azure** do lado esquerdo. Dependendo do seu acesso, você precisará selecionar uma conta de cobrança ou de um perfil de cobrança e, em seguida, selecione **créditos do Azure**.

4. A página de créditos do Azure exibe as seguintes informações:

   ![Captura de tela de saldo de crédito e transações para um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | A quantidade estimada de créditos que você tem depois de considerar todos os cobrado e as transações pendentes |
   | Saldo atual    | Valor dos créditos a partir de sua última fatura. Ele não inclui as transações pendentes |
   | Transações       | Todas as transações de cobrança que afetava o saldo de crédito do Azure |

   Quando o saldo a estimados cai a 0, você é cobrado para todo o seu uso, incluindo para os produtos que são cobertos pelos créditos.

6. Selecione **lista de créditos** para exibir uma lista de créditos para o perfil de cobrança. A lista de créditos fornece as seguintes informações:

   ![Captura de tela de listas de créditos para um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Saldo estimado | Quantidade de crédito do Azure que têm depois da subtração de encargos de crédito não faturados qualificados de seu saldo atual|
   | Saldo atual | Quantidade de crédito do Azure, que você tem antes de considerar crédito não cobrado encargos qualificados. Ele é calculado pela adição de novos créditos do Azure que você recebeu para o saldo de crédito no momento da sua última fatura|
   | source | A fonte de aquisição do crédito |
   | Data de início | A data quando você tiver adquirido o crédito |
   | Data de validade | A data de vencimento do crédito |
   | Saldo | O saldo a partir de sua última fatura |
   | Valor original | A quantidade original de crédito |
   | Status | O status atual de crédito. Status pode estar ativo, usado, expirado ou expirando |

## <a name="how-credits-are-used"></a>Como os créditos são usados

Em uma conta de cobrança para um contrato de cliente da Microsoft, você pode usar perfis de cobrança para gerenciar seus métodos de pagamento e notas fiscais. Uma fatura mensal é gerada para cada perfil de cobrança e usar os métodos de pagamento para pagar a nota fiscal.

Os créditos do Azure são um dos métodos de pagamento. Você obtém crédito da Microsoft, como o crédito promocional e o crédito de nível de serviço. Esses créditos são atribuídos a um perfil de cobrança. Quando uma fatura é gerada para o perfil de cobrança, os créditos são aplicados automaticamente para a quantidade total cobrada para calcular o valor que você precisa pagar. Pagar o valor restante com outro método de pagamento, como seleção ou transferência de transmissão.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são cobertos pelo créditos do Azure

 Os produtos a seguir não são cobertos por seus créditos do Azure. Você será cobrado para usar esses produtos, independentemente de seu saldo de crédito:

- Canônico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Usuário registrado
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de Suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entenda a conta de cobrança do contrato de cliente da Microsoft](billing-mca-overview.md)
- [Compreender os termos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-invoice.md)
