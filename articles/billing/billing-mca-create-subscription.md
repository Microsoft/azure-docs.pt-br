---
title: Criar uma assinatura do Azure adicional para sua conta de cobrança
description: Saiba como adicionar uma nova assinatura do Azure no portal do Azure.
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
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490927"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Criar uma assinatura do Azure adicional para o contrato de cliente da Microsoft

Crie assinaturas adicionais para sua conta de cobrança para configurar ambientes separados para desenvolvimento e teste, segurança ou para isolar dados por motivos de conformidade.

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access). Se você quiser criar assinaturas para outros tipos de contas de cobrança, consulte [criar uma assinatura adicional no portal do Azure](billing-create-subscription.md).

Para criar uma assinatura, você deve ser um **proprietário da seção de nota fiscal**, **colaborador da seção de nota fiscal**, ou **criador da assinatura do Azure**. Para obter mais informações, consulte [funções de cobrança da assinatura e tarefas](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Para fornecer a outros usuários permissão para criar assinaturas do Azure para sua conta de cobrança, consulte [conceder permissão para criar assinaturas do Azure a outros](#give-others-permission).

## <a name="create-a-subscription"></a>Criar uma assinatura

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Assinaturas**.

   ![Captura de tela que mostra a pesquisa no portal de assinaturas](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Selecione **Adicionar**

4. Se você tiver acesso a várias contas de cobrança, selecione a conta de cobrança para o contrato de cliente da Microsoft.

   ![Captura de tela que mostra página Criar assinatura](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selecione um perfil de cobrança. Os encargos para sua assinatura serão cobrados para o perfil de cobrança selecionado. Se você tiver acesso a somente um perfil de cobrança, a seleção ficará esmaecida.

6. Selecione uma seção de nota fiscal. Os encargos para sua assinatura serão cobrados para esta seção da fatura de cobrança do perfil. Se você tiver acesso a apenas uma seção de fatura, a seleção ficará esmaecida.

7. Selecione um plano para a assinatura. Selecione **plano do Microsoft Azure para desenvolvimento/teste**, se você planeja usar essa assinatura para o desenvolvimento ou testes cargas de trabalho mais usam **plano do Microsoft Azure**. Se você tiver acesso a apenas um plano, a seleção ficará esmaecida.

8. Insira um nome para a assinatura. O nome ajuda a identificar facilmente a assinatura no portal do Azure.

9. Selecione **Criar**.

## <a name="give-others-permission"></a>Permitir que outras pessoas

Adicione usuários como criadores de assinatura do Azure em uma seção de nota fiscal para lhes conceder permissão para criar assinaturas do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa no portal de assinaturas](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Acesse a seção de fatura. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança ou o perfil de cobrança. Na conta ou perfil de cobrança, selecione **seções de nota fiscal** e, em seguida, uma seção de nota fiscal na lista. Todas as assinaturas que serão criadas pelos usuários serão cobradas para esta seção da nota fiscal.
   
   ![Captura de tela que mostra como selecionar seções de nota fiscal](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Selecione **gerenciamento de acesso (IAM)** do lado do canto superior esquerdo.

5. Na parte superior da página, selecione **Adicionar**.

6. Selecione **criador da assinatura do Azure** para a função.

7. Insira o endereço de email do usuário a quem você deseja conceder acesso.

8. Clique em **Salvar**.

## <a name="check-access"></a>Verificar acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Permitir que outras pessoas para criar recursos do Azure usando funções internas](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Criar uma máquina virtual windows](../virtual-machines/windows/quick-create-portal.md)
- [Criar uma máquina virtual linux](../virtual-machines/linux/quick-create-portal.md)
- [Crie grupos de gerenciamento para organização e gerenciamento de recursos](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
