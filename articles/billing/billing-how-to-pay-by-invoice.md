---
title: Pagar assinaturas do Azure por fatura
description: Descreve como pagar assinaturas do Azure por fatura.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491211"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Pagar por sua assinatura do Azure por fatura

Se você alternar para pagamento por fatura, isso significa que você paga sua fatura dentro de 30 dias da data da nota fiscal por transferência eletrônica/seleção. Para se tornar qualificados para pagar sua assinatura do Azure por fatura, envie uma solicitação ao suporte do Azure. Depois que sua solicitação for aprovada, você pode alternar para (transferência de seleção/transmissão) de pagamento por fatura na [portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> * O pagamento da fatura (transferência de seleção/eletrônica) só está disponível para contas comerciais.
> * Você deve pagar todos os encargos pendentes antes de mudar para pagamento por fatura.

## <a name="request-to-pay-by-invoice"></a>Solicitação para pagar por fatura

1. Entre no [Portal do Azure](https://portal.azure.com/). Selecione **Ajuda + Suporte** > **Nova Solicitação de Suporte**.

    ![Ajuda e suporte de link](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Selecione **cobrança** como o **tipo de problema**. O *tipo de problema* é a categoria de solicitação de suporte. Selecione a assinatura para o qual você deseja pagar por fatura, selecione um plano de suporte e, em seguida, selecione **próxima**.

3. Selecione a opção **Pagar por Fatura** na caixa **Tipo de Problema**. O *tipo de problema* é a subcategoria de solicitação de suporte.

4. Insira as informações a seguir na caixa **detalhes** e, em seguida, selecione **Avançar**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - O **nome da empresa** e **endereço da empresa** deve coincidir com as informações que você forneceu para a conta do Azure. Para exibir ou atualizar as informações, consulte [alterar suas informações de perfil de conta do Azure](billing-how-to-change-azure-account-profile.md).
    - Você deve adicionar as informações de contato de cobrança no portal do Azure antes que o limite de crédito poderá ser aprovado. Os detalhes de contato devem estar relacionados ao departamento de contas a pagar ou finanças da empresa. Para atualizar as informações de contato de cobrança, acesse [Centro de contas do Azure](https://account.azure.com/Profile).

5. Verifique suas informações de contato e o método de contato preferencial e clique em **Criar**.

Se é necessário executar uma verificação de crédito devido à quantidade de crédito que você precisa, enviaremos a que você um crédito de verificar o aplicativo.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Mudar para a fatura de pagamento (seleção/cabo de transferência)

Depois de aprovada para pagar por fatura, você pode alternar para pagamento por (transferência de seleção/transmissão) de fatura no portal do Azure.

Se você tiver uma conta do programa Microsoft Online, você pode alternar sua assinatura do Azure para seleção/cabo de transferência. Se você tiver um contrato de cliente da Microsoft, você pode alternar o perfil de cobrança para seleção/cabo de transferência. [Saiba como verificar o tipo de conta](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Trocar a assinatura do Azure para seleção/cabo de transferência

Siga as etapas abaixo para trocar sua assinatura do Azure para (transferência de seleção/transmissão) de pagamento por fatura. **Quando você alternar para (transferência de seleção/transmissão) de pagamento por fatura você não pode alternar novamente para o cartão de crédito**.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **custo de gerenciamento + cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/billing-how-to-pay-by-invoice/search.png)

1. Selecione a assinatura que você gostaria de mudar para pagamento de fatura.
1. Selecione **Formas de pagamento**.
1. Na barra de comandos, clique o **pagar por fatura** botão.

    ![Captura de tela que mostra pagar por botão de nota fiscal](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Alternar o perfil de cobrança para seleção/cabo de transferência

Siga as etapas abaixo para alternar de um perfil de cobrança para seleção/cabo de transferência. Observe que somente a pessoa que se inscreveram para o Azure pode alterar o método de pagamento padrão de um perfil de cobrança.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, clique em **perfis de cobrança**.

    ![captura de tela que mostra o perfil de cobrança no menu](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione **métodos de pagamento**.

   ![Captura de tela que mostra os métodos de pagamento no menu](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Clique na faixa azul que diz que você está qualificado para pagar por transferência eletrônica/seleção.

    ![Captura de tela que mostra a faixa azul para alternar para a seleção/cabo](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Se necessário, atualize suas informações de contato de cobrança na [Centro de contas do Azure](https://account.azure.com/Profile).
