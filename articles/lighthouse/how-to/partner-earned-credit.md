---
title: Vincular a ID de parceiro para acompanhar o seu impacto em recursos delegados
description: Saiba como associar a sua ID de parceiro para receber o PEC (crédito obtido por parceiro) nos recursos do cliente que você gerencia por meio do Azure Lighthouse.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372086"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Vincular a ID de parceiro para acompanhar o seu impacto em recursos delegados 

Se você for membro do [Microsoft Partner Network](https://partner.microsoft.com/), poderá vincular a sua ID de parceiro às credenciais usadas para gerenciar recursos de cliente delegados, permitindo que a Microsoft identifique e reconheça os parceiros que impulsionam o sucesso do cliente do Azure. Esse link também poermite que os parceiros de [CSP (provedor de solução de nuvem)](/partner-center/csp-overview) recebam [créditos ganhos pelo parceiro por serviços gerenciados (PEC)](/partner-center/partner-earned-credit) para clientes que tenham [assinado o Contrato de Cliente da Microsoft (MCA)](/partner-center/confirm-customer-agreement) e estiverem [no plano do Azure](/partner-center/azure-plan-get-started).

Para obter o reconhecimento das atividades do Azure Lighthouse, você precisará [vincular a sua ID do MPN](../../cost-management-billing/manage/link-partner-id.md) a pelo menos uma conta de usuário no locatário de gerenciamento e garantir que a conta vinculada tenha acesso a cada uma das assinaturas integradas.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associar a sua ID de parceiro ao integrar novos clientes

Use o processo a seguir para vincular a sua ID de parceiro (e habilitar o crédito obtido por parceiro, se aplicável). Você precisará saber a sua [ID de parceiro do MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) para concluir estas etapas. Use a **ID do MPN Associado** mostrada em seu perfil de parceiro.

Para simplificar, recomendamos criar uma conta de entidade de serviço no locatário, vinculando-a à **ID do MPN Associado** e concedendo a ela acesso a todos os clientes que você integrar com uma [função interna do Azure qualificada para PEC](/partner-center/azure-roles-perms-pec).

1. [Crie uma conta de usuário da entidade de serviço](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) no seu locatário de gerenciamento. Neste exemplo, usaremos o nome *Conta de Automação do Provedor* para essa conta da entidade de serviço.
1. Usando essa conta da entidade de serviço, [vincule à sua ID do MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) ao seu locatário de gerenciamento. Você só precisa fazer isso uma vez.
1. Quando você integra um cliente [usando modelos do ARM](onboard-customer.md) ou [ofertas do Serviço Gerenciado](publish-managed-services-offers.md), verifique se foi incluída uma autorização que inclua a Conta de Automação do Provedor como um usuário com uma [função interna do Azure qualificada para o PEC](/partner-center/azure-roles-perms-pec).

Seguindo essas etapas, todos os locatários de clientes que você gerenciar serão associados à sua ID de parceiro. A Conta de Automação do Provedor não precisa autenticar ou executar nenhuma ação no locatário do cliente.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Diagrama mostrando o processo de vinculação de ID de parceiro com o Azure Lighthouse.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Adicione a sua ID de parceiro aos clientes integrados anteriormente

Se você já tiver integrado um cliente, talvez não queira executar outra implantação para adicionar a entidade de serviço da Conta de Automação do Provedor. Em vez disso, você pode vincular a sua **ID do MPN Associado** a uma conta de usuário que já tem acesso de trabalho ao locatário desse cliente. Verifique se a conta recebeu uma [função interna do Azure qualificada para o PEC](/partner-center/azure-roles-perms-pec).

Depois que a conta tiver sido [vinculada à ID do MPN Associado](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) no seu locatário de gerenciamento, você poderá acompanhar o reconhecimento do impacto sobre esse cliente.

## <a name="confirm-partner-earned-credit"></a>Confirmar o crédito obtido por parceiro

Você pode [ver os detalhes do PEC no portal do Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) e confirmar quais custos receberam o benefício de PEC. Lembre-se de que o PEC só se aplica aos clientes do CSP que assinaram o MCA e estão no plano do Azure.

Se você seguiu as etapas acima e não encontrou a associação esperada, abra uma solicitação de suporte no portal do Azure.

Você também pode usar o [SDK do Partner Center](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) e filtrar `rateOfPartnerEarnedCredit` para automatizar a verificação de PEC de uma assinatura.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Microsoft Partner Network](/partner-center/mpn-overview).
- Saiba [como o PEC é calculado e pago](/partner-center/partner-earned-credit-explanation).
- [Integre clientes](onboard-customer.md) ao Azure Lighthouse.