---
title: Vincule sua ID de parceiro para acompanhar seu impacto em recursos delegados
description: Saiba como associar sua ID de parceiro para receber o PEC (crédito ganho) do parceiro nos recursos do cliente que você gerencia por meio do Azure Lighthouse.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372086"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Vincule sua ID de parceiro para acompanhar seu impacto em recursos delegados 

Se você for membro da [Microsoft Partner Network](https://partner.microsoft.com/), poderá vincular sua ID de parceiro com as credenciais usadas para gerenciar recursos de cliente delegados, permitindo que a Microsoft identifique e reconheça os parceiros que impulsionam o sucesso do cliente do Azure. Esse link também permite que os parceiros [do CSP (provedor de soluções de nuvem)](/partner-center/csp-overview) recebam [crédito de contrato de serviço gerenciado (PEC)](/partner-center/partner-earned-credit) para clientes que [assinaram o Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) e estão [no plano do Azure](/partner-center/azure-plan-get-started).

Para obter o reconhecimento das atividades do Azure Lighthouse, você precisará [vincular sua ID do MPN](../../cost-management-billing/manage/link-partner-id.md) a pelo menos uma conta de usuário no seu locatário de gerenciamento e garantir que a conta vinculada tenha acesso a cada uma de suas assinaturas integradas.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associe sua ID de parceiro ao integrar novos clientes

Use o processo a seguir para vincular sua ID de parceiro (e habilitar crédito de parceiro obtido, se aplicável). Você precisará saber sua [ID de parceiro do MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) para concluir estas etapas. Use a **ID do MPN Associado** mostrada em seu perfil de parceiro.

Para simplificar, é recomendável criar uma conta de entidade de serviço em seu locatário, vinculá-la à **ID do MPN associada** e, em seguida, conceder acesso a todos os clientes que você integrar com uma [função interna do Azure qualificada para o PEC](/partner-center/azure-roles-perms-pec).

1. [Crie uma conta de usuário de entidade de serviço](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) em seu locatário de gerenciamento. Para este exemplo, usaremos a *conta de automação do provedor* de nomes para essa conta de entidade de serviço.
1. Usando essa conta de entidade de serviço, [vincule à sua ID de MPN associada](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu locatário de gerenciamento. Você só precisa fazer isso uma vez.
1. Quando você integra um cliente [usando modelos ARM](onboard-customer.md) ou [ofertas de serviço gerenciado](publish-managed-services-offers.md), certifique-se de incluir uma autorização que inclua a conta de automação do provedor como um usuário com uma [função interna do Azure qualificada para o PEC](/partner-center/azure-roles-perms-pec).

Seguindo essas etapas, todos os locatários de clientes que você gerenciar serão associados à sua ID de parceiro. A conta de automação do provedor não precisa autenticar ou executar nenhuma ação no locatário do cliente.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagrama mostrando o processo de vinculação de ID de parceiro com o Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adicione sua ID de parceiro aos clientes integrados anteriormente

Se você já tiver integrado um cliente, talvez não queira executar outra implantação para adicionar a entidade de serviço da conta de automação do provedor. Em vez disso, você pode vincular sua **ID de MPN associada** a uma conta de usuário que já tem acesso ao trabalho no locatário desse cliente. Verifique se a conta recebeu uma [função interna do Azure qualificada para o PEC](/partner-center/azure-roles-perms-pec).

Depois que a conta tiver sido [vinculada à ID de MPN associada](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu locatário de gerenciamento, você poderá acompanhar o reconhecimento do impacto sobre esse cliente.

## <a name="confirm-partner-earned-credit"></a>Confirmar crédito de parceiro ganho

Você pode [exibir os detalhes do PEC no portal do Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e confirmar quais custos receberam o benefício do PEC. Lembre-se de que o PEC só se aplica aos clientes do CSP que assinaram o MCA e estão no plano do Azure.

Se você seguiu as etapas acima e não vê a associação esperada, abra uma solicitação de suporte no portal do Azure.

Você também pode usar o [SDK do Partner Center](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) e filtrar `rateOfPartnerEarnedCredit` para automatizar a verificação de PEC para uma assinatura.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Microsoft Partner Network](/partner-center/mpn-overview).
- Saiba [como o PEC é calculado e pago](/partner-center/partner-earned-credit-explanation).
- Integrar [clientes](onboard-customer.md) ao Azure Lighthouse.