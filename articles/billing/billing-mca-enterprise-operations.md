---
title: Concluir tarefas de Enterprise Agreement no contrato de cliente da Microsoft - Azure
description: Saiba como concluir tarefas de Enterprise Agreement em sua nova conta de cobrança.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9404908b7c486801480474c5a2c9ff7688e1de48
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490705"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Concluir tarefas de Enterprise Agreement em sua conta de cobrança para um contrato de cliente da Microsoft

Se sua organização assinou um contrato de cliente do Microsoft para renovar seu registro do Enterprise Agreement, uma nova conta de cobrança será criada para o contrato. A cobrança na sua nova conta é organizada Diferentemente de seu contrato Enterprise. Este artigo descreve como você pode usar a nova conta de cobrança para executar tarefas que você executou no seu contrato Enterprise.

## <a name="billing-organization-in-the-new-account"></a>A organização na nova conta de cobrança

O diagrama a seguir descreve como a cobrança é organizada em sua nova conta de cobrança.

![Imagem de ea-mca-post-transição-hierarquia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Contrato Enterprise   | Contrato de Cliente da Microsoft    |
|------------------------|--------------------------------------------------------|
| Registro            | Você pode usar um perfil de cobrança para gerenciar a cobrança para sua organização, semelhante ao seu registro de Enterprise Agreement. Os administradores de empresa se tornarem proprietários de perfil de cobrança. Para saber mais sobre perfis de cobrança, consulte [entender a cobrança perfis](billing-mca-overview.md#billing-profiles).
| department            | Você pode usar uma seção de nota fiscal para organizar seus custos, semelhantes aos departamentos em seu registro de Enterprise Agreement. Departamento se torna a seções de nota fiscal e administradores de departamento se tornarem proprietários das seções respectivo da nota fiscal. Para saber mais sobre as seções de fatura, consulte [seções de nota fiscal de compreender](billing-mca-overview.md#invoice-sections). |
| Conta               | As contas que foram criadas no seu contrato Enterprise não têm suporte na nova conta de cobrança. Assinaturas da conta pertencem à seção respectivos fatura para seus departamentos. Os proprietários de conta podem criar e gerenciar assinaturas para suas seções de nota fiscal. |

## <a name="changes-for-enterprise-administrators"></a>Alterações para os administradores de empresa

As alterações a seguir se aplicam a administradores de empresa em um Enterprise Agreement que obteve renovada para um contrato de cliente da Microsoft.

- Um perfil de cobrança é criado para seu registro. Você usará o perfil de cobrança para gerenciar a cobrança para sua organização, como o seu registro do Enterprise Agreement. Para saber mais sobre perfis de cobrança [entender a cobrança perfis](billing-mca-overview.md#billing-profiles).
- Uma seção de nota fiscal é criada para cada departamento na sua inscrição de Enterprise Agreement. Você usará as seções de nota fiscal para gerenciar seus departamentos. Você pode criar novas seções de nota fiscal para configurar outros departamentos. Para saber mais sobre as seções de fatura, consulte [entender as seções de nota fiscal](billing-mca-overview.md#invoice-sections).
- Você usará a função de criador da assinatura do Azure em seções de nota fiscal que outros usuários tenham permissão para criar a assinatura do Azure, como as contas que foram criados no registro do Enterprise Agreement.
- Você usará o [portal do Azure](https://portal.azure.com) para gerenciar a cobrança para sua organização, em vez do portal de EA do Azure.

Você recebe as funções a seguir sobre a nova conta de cobrança:

**Proprietário do perfil de cobrança** -recebem a função de proprietário de perfil de cobrança no perfil de cobrança que foi criada quando o contrato foi assinado. A função permite gerenciar a cobrança para sua organização. Você pode exibir cobranças e notas fiscais, organizar custos na fatura, gerenciar métodos de pagamento e controlar o acesso a cobrança da sua organização.

**Proprietário da seção de fatura** -recebem a função de proprietário de seção de nota fiscal em todas as seções de nota fiscal que são criados para os departamentos de seu registro do Enterprise Agreement. A função permite controlar quem pode criar assinaturas do Azure e outros produtos de compra.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Exibir o saldo de cobranças e créditos para sua organização

Você pode usar o perfil de cobrança para rastrear cobranças e saldo de créditos do Azure para sua organização semelhante ao seu registro de Enterprise Agreement.

Para saber como exibir o saldo de crédito para um perfil de cobrança, consulte [acompanhar o saldo de crédito do Azure para o perfil de cobrança](billing-mca-check-azure-credits-balance.md).

Para saber como exibir os encargos para um perfil de cobrança, consulte [entender os encargos na fatura do Microsoft Customer do seu contrato](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Encargos de exibição para um departamento

Uma seção de nota fiscal é criada para cada departamento que você tinha no seu contrato Enterprise. Você pode exibir cobranças para uma seção de fatura no portal do Azure. Para obter mais informações, consulte [exibir transações pelas seções de nota fiscal](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Encargos de exibição para uma conta

Não há suporte para as contas que foram criadas em seu registro do Enterprise Agreement na nova conta de cobrança. Assinaturas da conta pertencem à seção respectivos fatura para seus departamentos. Os proprietários de conta podem criar e gerenciar assinaturas para suas seções de nota fiscal.

Para exibir o custo agregado para assinaturas que pertenciam a uma conta, você deve definir um centro de custo para cada assinatura. Em seguida, você pode usar o arquivo csv de uso e encargos do Azure para filtrar as assinaturas pelo Centro de custo.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Documentos de imposto, folha de preços e csv de uso e encargos de download

Uma fatura mensal é gerada para cada perfil de cobrança na sua conta de cobrança. Para cada fatura, você pode baixar arquivos de csv de uso e os encargos do Azure, folha de preços e documentos de imposto (se aplicável). Você também pode baixar o Azure encargos e o uso de arquivo csv para encargos do mês atual.

Para saber como baixar arquivos de csv de uso e os encargos do Azure, consulte [baixar uso para o seu contrato de cliente do Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Para saber como baixar a folha de preços, consulte [baixar o preço para o contrato de cliente da Microsoft](billing-ea-pricing.md#microsoft-customer-agreement-pricing).

Para saber como baixar documentos de imposto, consulte [exibir os documentos de imposto para o seu contrato de cliente do Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Adicionar um administrador corporativo adicionais

Fornecer aos usuários acesso ao perfil de cobrança para deixá-los a exibição e gerenciar a cobrança para sua organização. Você pode usar o **controle de acesso (IAM)** página no portal do Azure para conceder acesso.  Para saber mais sobre funções de perfil de cobrança, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Para saber como fornecer, acesso a seu perfil de cobrança, consulte [gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Criar um novo departamento

Crie uma seção de nota fiscal para organizar seus custos com base em suas necessidades, assim como os departamentos em seu registro de Enterprise Agreement. Você pode criar uma nova seção de fatura no portal do Azure. Para obter mais informações, consulte [criar seções na sua fatura para organizar seus custos](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Criar uma nova conta

Atribua usuários a função de criador de assinatura do Azure em seções de nota fiscal para lhes conceder permissão para criar a assinatura do Azure, como as contas que são criados no registro do Enterprise Agreement. Para obter mais informações, consulte [conceder permissão para criar assinaturas do Azure a outros](billing-mca-create-subscription.md#give-others-permission).

## <a name="changes-for-department-administrators"></a>Alterações para administradores de departamento

As alterações a seguir se aplicam a administradores de departamento em um Enterprise Agreement que obteve renovada para um contrato de cliente da Microsoft.

- Uma seção de nota fiscal é criada para cada departamento na sua inscrição de Enterprise Agreement. Você usará a seção de fatura (ões) para gerenciar seus departamentos. Para saber mais sobre as seções de fatura, consulte [entender as seções de nota fiscal](billing-mca-overview.md#invoice-sections).
- Você usará a função de criador da assinatura do Azure na seção de nota fiscal que outros usuários tenham permissão para criar a assinatura do Azure, como as contas que são criados no registro do Enterprise Agreement.
- Você usará o portal do Azure para gerenciar a cobrança para sua organização, em vez do portal de EA do Azure.

Você recebe as funções a seguir sobre a nova conta de cobrança:

**Proprietário da seção de fatura** -recebem a função de proprietário de seção de nota fiscal na seção de nota fiscal é criada para os departamentos que você tinha no Enterprise Agreement. A função permite exibir e acompanhar os encargos e controle quem pode criar assinaturas do Azure e comprar outros produtos para a seção de nota fiscal.

### <a name="view-charges-for-your-department"></a>Exibir encargos para o seu departamento

Você pode exibir cobranças para a seção de nota fiscal é criada para seu departamento, em que o portal do Azure [gerenciamento de custos + cobrança página](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Adicionar um administrador de departamento adicionais

Uma seção de nota fiscal é criada para cada departamento que você tinha no seu contrato Enterprise. Você pode usar o **(IAM) de acesso** página no portal do Azure para que outras pessoas acesso para exibir e gerenciar a seção de nota fiscal. Para saber mais sobre as funções de seção de fatura, consulte [seção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Para saber como fornecer, o acesso à seção de fatura, consulte [gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Criar uma nova conta em seu departamento

Atribua usuários a função de criador de assinatura do Azure na seção de fatura que é criada para o seu departamento. Para obter mais informações, consulte [conceder permissão para criar assinaturas do Azure a outros](billing-mca-create-subscription.md#give-others-permission).

### <a name="view-charges-for-accounts-in-your-departments"></a>Exibir encargos para contas de seus departamentos

Não há suporte para as contas que foram criadas em seu registro do Enterprise Agreement na nova conta de cobrança. Assinaturas da conta pertencem à seção respectivos fatura para seus departamentos. Os proprietários de conta podem criar e gerenciar assinaturas para suas seções de nota fiscal.

Para exibir o custo agregado para assinaturas que pertenciam a uma conta do seu departamento, você deve definir um centro de custo para cada assinatura. Em seguida, você pode usar o arquivo de uso e os encargos do Azure para filtrar as assinaturas pelo Centro de custo.

## <a name="changes-for-account-owners"></a>Alterações para proprietários de conta

Os proprietários de conta do Enterprise Agreement obtém permissão para criar assinaturas do Azure na nova conta de cobrança. Suas assinaturas do Azure existentes pertencem à seção de nota fiscal é criada para seu departamento. Se sua conta não pertencer a um departamento, suas assinaturas pertencem a uma seção de fatura padrão fatura seção nomeada.

Para criar assinaturas adicionais do Azure, você recebe a seguinte função sobre a nova conta de cobrança.

**Criador da assinatura do Azure** -recebem a função de criador de assinatura do azure na seção de nota fiscal é criada para o seu departamento no Enterprise Agreement. Se sua conta não pertencer a um departamento, você obtém a função de criador da assinatura do Azure em uma seção chamada seção da fatura padrão. A função permite criar assinaturas do Azure para a seção de nota fiscal.

### <a name="create-an-azure-subscription"></a>Cria uma assinatura do Azure

Você pode criar assinaturas do Azure para sua seção de fatura no portal do Azure. Para obter mais informações, consulte [criar uma assinatura do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Exibir encargos para sua conta

Para exibir os encargos para assinaturas que pertenciam a uma conta, vá para o [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. A página de assinaturas exibe encargos para todos os sua assinatura.

### <a name="view-charges-for-a-subscription"></a>Encargos de exibição para uma assinatura

Você pode ver os encargos para uma assinatura ou sobre o [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou análise de custo do Azure. Para obter mais informações sobre análise de custo do Azure, consulte [explorar e analisar os custos com análise de custo](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entender a conta de cobrança para um contrato de cliente da Microsoft](billing-mca-overview.md)
- [Entenda sua fatura](billing-understand-your-bill.md)
- [Entenda sua cobrança](billing-understand-your-invoice.md)
- [Obter propriedade de assinaturas do Azure de outros usuários de cobrança](billing-mca-request-billing-ownership.md)
