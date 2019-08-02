---
title: Comece com sua conta de cobrança do contrato de cliente da Microsoft - Azure
description: Entender o contrato de cliente da Microsoft conta de cobrança
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490745"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Comece com sua conta de cobrança do contrato de cliente da Microsoft

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Usar sua conta de cobrança para notas fiscais, pagamentos, de gerenciar e controlar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter entrado do Azure para seus projetos pessoais. Você também pode ter acesso ao Azure por meio do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um desses cenários, você teria uma conta de cobrança separada.

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Sua conta de cobrança

Sua conta de cobrança para o contrato de cliente da Microsoft contém um ou mais perfis de cobrança que permitem que você gerencie seus métodos de pagamento e notas fiscais. Cada perfil de cobrança contém um ou mais seções de nota fiscal que permitem que você organize os custos de fatura de cobrança do perfil.

O diagrama a seguir mostra a relação entre uma conta de cobrança, perfis de cobrança e seções de nota fiscal.

![Diagrama que mostra a hierarquia de cobrança do contrato de cliente do Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Funções na conta de cobrança têm o nível mais alto de permissões. Por padrão, somente o usuário que se inscreveram para o Azure obtém acesso para a conta de cobrança. Essas funções devem ser atribuídas a usuários que precisam exibir faturas e controlar os custos para toda a organização, como finanças ou gerentes de TI. Para obter mais informações, consulte [tarefas e funções da conta de cobrança](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Perfis de cobrança

Use um perfil de cobrança para gerenciar seus métodos de pagamento da nota fiscal. Uma fatura mensal é gerada no início do mês para cada perfil de cobrança na sua conta. A nota fiscal contém respectivas cobranças para todas as assinaturas do Azure e outras compras do mês anterior.

Um perfil de cobrança é automaticamente criado para sua conta de cobrança. Ele contém uma seção de nota fiscal por padrão. Você pode criar seções adicionais para acompanhar e organizar os custos com base em suas necessidades com facilidade se é para cada ambiente de desenvolvimento, departamento ou projeto. Você verá essas seções na fatura de cobrança do perfil refletindo o uso de cada assinatura e compras que você atribuiu a ele.

Funções nos perfis de cobrança têm permissões para exibir e gerenciar as notas fiscais e métodos de pagamento. Atribua essas funções para usuários que pagar notas fiscais, como membros da equipe de contabilidade em sua organização. Para obter mais informações, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Cada perfil de cobrança obtém uma fatura mensal

Uma fatura mensal é gerada no início do mês para cada perfil de cobrança. A nota fiscal contém todos os encargos do mês anterior.

Você pode exibir a fatura, baixar documentos e alterar a configuração para obter o futuras faturas por email, no portal do Azure. Para obter mais informações, consulte [download de faturas para um contrato de cliente do Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Métodos de pagamento de fatura

Cada perfil de cobrança tem seus próprios métodos de pagamento que são usados para pagar suas faturas. Há suporte para os seguintes métodos de pagamento:

| Type             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são aplicados automaticamente a qualificados encargos na sua fatura, reduzindo a quantidade que você precisa pagar. Para obter mais informações, consulte [acompanhar o saldo de crédito do Azure para o perfil de cobrança](billing-mca-check-azure-credits-balance.md). |
|Seleção/cabo de transferência | Se sua conta for aprovada para pagamento por meio de transferência eletrônica/seleção. Você pode pagar o valor devido para sua fatura por meio da transferência eletrônica/seleção. As instruções de pagamento são fornecidas na nota fiscal |
|Cartão de crédito | Clientes que se inscreverem para o Azure por meio do site do Azure podem pagar por meio de um cartão de crédito. |

### <a name="apply-policies-to-control-purchases"></a>Aplicar políticas para controlar as compras

Aplica políticas de controle do Azure Marketplace e compras de reserva usando um perfil de cobrança. Você pode definir políticas para desabilitar a compra de reservas do Azure e produtos do Marketplace. Quando as políticas são aplicadas, as assinaturas que são cobradas ao perfil de cobrança não podem ser usadas para fazer essas compras.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Contrato de nível de serviço e preços para assinaturas de determinar os planos do Azure

Os planos do Azure determinam os preços e contratos de nível de assinaturas do Azure do serviço. Elas são habilitadas automaticamente quando você cria um perfil de cobrança. Todas as seções de nota fiscal que estão associadas com o perfil de cobrança podem usar esses planos. Os usuários com acesso à seção de nota fiscal usar os planos de criar assinaturas do Azure. Os planos do Azure a seguir têm suporte em contas de cobrança para o contrato de cliente da Microsoft:

| Plano             | Definição  |
|------------------|-------------|
|Microsoft Azure Plan   | Permitir que os usuários criem assinaturas que podem executar qualquer carga de trabalho. Para obter mais informações, consulte [plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plano do Microsoft Azure para desenvolvimento/teste | Permitir que os assinantes do Visual Studio criar assinaturas que são restritas para o desenvolvimento ou teste de cargas de trabalho. Essas assinaturas obtêm os benefícios como acesso a imagens de máquina de virtual exclusivo e taxas menores no portal do Azure. Para obter mais informações, consulte [plano do Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Seções de nota fiscal

Crie seções de nota fiscal para organizar os custos em sua fatura. Por exemplo, talvez seja necessário uma única fatura para a sua organização, mas deseja organizar os custos por departamento, equipe ou projeto. Para este cenário, você tem um único perfil de cobrança em que você criar uma seção de nota fiscal para cada departamento, equipe ou projeto.

Quando uma seção de nota fiscal é criada, você pode dar a outros usuários permissão para criar assinaturas do Azure são cobradas a seção. Todos os encargos de uso e compras para as assinaturas, em seguida, são cobradas à seção.

Funções na seção de nota fiscal têm permissões para controlar quem cria assinaturas do Azure. Atribua essas funções para usuários que configurar o ambiente do Azure para as equipes em nossa organização, como líderes de engenharia e arquitetos técnicos. Para obter mais informações, consulte [seção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre sua conta de cobrança:

- [Entender as funções administrativas do contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md)
- [Criar uma assinatura do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
- [Criar seções na sua fatura para organizar seus custos](billing-mca-section-invoice.md)
