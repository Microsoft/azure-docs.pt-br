---
title: Sobre a transferência da propriedade da cobrança para uma assinatura do Azure
description: Este artigo explica o que você precisa saber antes de transferir a propriedade da cobrança de uma assinatura do Azure para outra conta.
keywords: transferir a assinatura do Azure, assinatura de transferência do Azure, mover a assinatura do Azure para outra conta, alterar o proprietário da assinatura do Azure, transferir a assinatura do Azure para outra conta, cobrança de transferência do Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 163bd7dbdac4de7b7d9c1657ac3debf45382a27a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027264"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Sobre a transferência da propriedade da cobrança para uma assinatura do Azure

Este artigo ajuda você a entender o que você deve saber antes de transferir a propriedade da cobrança de uma assinatura do Azure para outra conta. 

Talvez você queria transferir a propriedade de cobrança da sua assinatura do Azure, caso esteja se desligando da empresa ou queira que sua assinatura seja cobrada em outra conta. A transferência da propriedade de cobrança para outra conta fornece aos administradores da noca conta a permissão para tarefas de cobrança. Eles podem alterar o método de pagamento, exibir encargos e cancelar a assinatura.

Se você quiser manter a propriedade de cobrança, mas alterar o tipo de assinatura, confira [Mudar sua assinatura do Azure para outra oferta](../manage/switch-azure-offer.md). Para controlar quem pode acessar recursos na assinatura, confira [Funções internas do Azure](../../role-based-access-control/built-in-roles.md).

Se você for um cliente EA (Contrato Enterprise), seus administradores corporativos poderão transferir a propriedade da cobrança de suas assinaturas entre contas.

Somente o administrador de cobrança de uma conta pode transferir a propriedade de uma assinatura.

## <a name="determine-account-billing-administrator"></a>Determinar o administrador de cobrança da conta

<a name="whoisaa"></a>

O administrador de cobrança é a pessoa que tem permissão para gerenciar a cobrança de uma conta. Ele tem autoridade para acessar a cobrança no [portal do Azure](https://portal.azure.com) e executar várias tarefas de cobrança, como criar assinaturas, exibir e pagar faturas ou atualizar formas de pagamento.

Para identificar contas das quais você é administrador de cobrança, acesse a [página Gerenciamento de Custos e Cobrança no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Em seguida, selecione **Todos os escopos de cobrança** no painel esquerdo. A página de assinaturas mostra todas as assinaturas nas quais você é um Administrador de cobrança.

Se você não tiver certeza de quem é o administrador da conta de uma assinatura, acesse a [página Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Em seguida, selecione a assinatura que você deseja verificar e olhe em **Configurações**. Selecione **Propriedades** e o administrador da conta da assinatura é mostrado na caixa **Administrador da Conta**.


## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

A transferência de assinatura no portal do Azure está disponível para os tipos de assinatura listados abaixo. Atualmente, não é possível transferir assinaturas de [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [AIO (Azure via Open)](https://azure.microsoft.com/offers/ms-azr-0111p/). Para uma solução alternativa, consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Para transferir outras assinaturas, como planos de suporte, [entre em contato com o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Usando o portal do EA.

<sup>2</sup> Só há suporte para contas criadas durante a inscrição no site do Azure.

## <a name="resources-transferred-with-subscriptions"></a>Recursos transferidos com assinaturas

Todos os recursos – como VMs, discos e sites – são transferidos para a nova conta. No entanto, se você transferir uma assinatura para uma conta em outro locatário do Azure AD, as [funções de administrador](../manage/add-change-subscription-administrator.md) e as [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) na assinatura não serão transferidas. Além disso, os [Registros de aplicativo](../../active-directory/develop/quickstart-register-app.md) e outros serviços específicos do locatário também não serão transferidos juntamente com a assinatura.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Transferir a propriedade da conta para outro país/região

Não é possível transferir assinaturas entre países ou regiões usando o portal do Azure. No entanto, elas poderão ser transferidas se você abrir uma solicitação de suporte do Azure. Para criar uma solicitação de suporte, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Transferir uma assinatura de uma conta para outra

Se for administrador de duas contas, você poderá transferir uma assinatura entre suas contas. Conceitualmente, suas contas são consideradas pertencentes a dois usuários distintos; portanto, você pode transferir assinaturas entre suas contas.
Para exibir as etapas necessárias para transferir sua assinatura, confira [Transferir a propriedade da cobrança de uma assinatura do Azure](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>A transferência de uma assinatura não deve criar tempo de inatividade

Se você transferir uma assinatura para uma conta no mesmo locatário do Azure AD, não haverá nenhum impacto sobre os recursos em execução na assinatura. No entanto, as informações de contexto salvas no PowerShell não são atualizadas, portanto, talvez seja necessário limpá-las ou alterar as configurações. Se você transferir a assinatura para uma conta em outro locatário e decidir mover a assinatura para esse locatário, todos os usuários, grupos e entidades de serviço que tinham [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) para acessar recursos na assinatura perderão o acesso. Como resultado, pode ocorrer o tempo de inatividade do serviço.

## <a name="new-account-usage-and-billing-history"></a>Uso e histórico de cobrança da nova conta

As únicas informações disponíveis para os usuários da nova conta são os custos do mês passado para sua assinatura. O restante do histórico de cobrança e de uso não será transferido com a assinatura.

## <a name="manually-migrate-data-and-services"></a>Migre dados e serviços manualmente

Quando você transfere uma assinatura, os recursos dela permanecem com ela. Se não for possível transferir a propriedade da assinatura, migre os recursos dela manualmente. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Créditos restantes da assinatura 

Se você tiver uma assinatura do Visual Studio ou Microsoft Partner Network, receberá créditos mensais. Seu crédito não é transferido com a assinatura na nova conta. O usuário de destino precisa ter uma licença do Visual Studio para aceitar a solicitação de transferência. A assinatura usa o crédito do Visual Studio que está disponível na conta do usuário. Para saber mais, confira [Como transferir assinaturas do Visual Studio e do Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Os usuários mantêm o acesso aos recursos transferidos

Tenha em mente que os usuários com acesso a recursos em uma assinatura mantêm o acesso deles quando a propriedade é transferida. No entanto, [funções de administrador](../manage/add-change-subscription-administrator.md) e [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) podem ser removidas. A perda de acesso ocorre quando sua conta está em um locatário do Azure AD diferente do locatário da assinatura, e o usuário que enviou a solicitação de transferência move a assinatura para o locatário da sua conta. 

Você pode exibir os usuários que têm atribuições de função do Azure para acessar recursos na assinatura no portal do Azure. Acesse a [página de Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Em seguida, selecione a assinatura que deseja verificar e, em seguida, selecione **Controle de acesso (IAM)** no painel à esquerda. Depois, na parte superior da página, selecione **Atribuições de função**. A página de atribuições de função lista todos os usuários que têm acesso à assinatura.

Mesmo que as [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) sejam removidas durante a transferência, os usuários da conta do proprietário original continuarão tendo acesso à assinatura por meio de outros mecanismos de segurança, incluindo:

* Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso para serviços como Armazenamento. Para saber mais, confira [Sobre as contas de armazenamento do Azure](../../storage/common/storage-account-create.md).
* Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se o destinatário precisar restringir o acesso aos recursos, ele deverá considerar a atualização dos segredos associados ao serviço. A maioria dos recursos pode ser atualizada. Entre no [portal do Azure](https://portal.azure.com) e, no menu Hub, selecione **Todos os recursos**. Em seguida, selecione o recurso. Depois, na página de recursos, selecione **Configurações**. Aqui você pode exibir e atualizar os segredos existentes.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Você paga pelo uso quando recebe a propriedade

Sua conta será responsável pelo pagamento de todo uso registrado a partir do momento da transferência. Pode haver algum uso que ocorreu antes da transferência, mas foi relatado posteriormente. O uso está incluído na fatura da sua conta.

## <a name="use-a-different-payment-method"></a>Usar uma forma de pagamento diferente

Ao aceitar a solicitação de transferência, você pode selecionar uma forma de pagamento existente que esteja vinculada à sua conta ou adicionar uma nova forma de pagamento.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Transferir a propriedade da assinatura do Contrato Enterprise

O Administrador Corporativo pode atualizar a propriedade da conta de qualquer conta, mesmo se o proprietário original da conta não faz mais parte da organização. Para obter mais informações sobre como transferir contas do Contrato Enterprise do Azure, confira [Transferências do Azure Enterprise](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Transferir a propriedade de cobrança de uma assinatura do Azure](../manage/billing-subscription-transfer.md)
