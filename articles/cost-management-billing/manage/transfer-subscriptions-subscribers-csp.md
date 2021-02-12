---
title: Transferir assinaturas do Azure entre assinantes e CSPs
description: Saiba como você pode transferir assinaturas do Azure entre assinantes e CSPs.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: 4cc213db19a567f18c5ff483b64a75e3bbeef892
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962103"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Transferir assinaturas do Azure entre assinantes e CSPs

Este artigo fornece as etapas de alto nível usadas para transferir assinaturas do Azure de/para parceiros CSP (provedor de soluções de nuvem) e seus clientes. O objetivo das informações aqui é ajudar o assinante do Azure a fazer a coordenação com o parceiro dele. As informações que os parceiros da Microsoft usam para o processo de transferência estão documentadas em [Saiba como transferir as assinaturas do Azure de um cliente para outro parceiro](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Antes de iniciar uma solicitação de transferência, você deve baixar ou exportar qualquer informação de custo e cobrança que deseja manter. As informações de cobrança e utilização não são transferidas com a assinatura. Para obter mais informações de como exportar dados de gerenciamento de custos, confira [Criar e gerenciar dados exportados](../costs/tutorial-export-acm-data.md). Para obter informações sobre como baixar sua fatura e os dados de uso, confira [Baixar ou exibir sua fatura de cobrança e dados de uso diário do Azure](download-azure-invoice-daily-usage-date.md).

Se você tiver reservas existentes, elas deixarão de ser aplicadas depois que você transferir uma assinatura. Lembre-se de [cancelar as reservas e reembolsá-las](../reservations/exchange-and-refund-azure-reservations.md) antes de transferir uma assinatura.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Transferir assinaturas EA para um parceiro CSP

Os parceiros CSP de cobrança direta certificados como um [Provedor de Serviços Gerenciados (MSP) Especialista em Azure](https://partner.microsoft.com/membership/azure-expert-msp) podem solicitar a transferência de assinaturas do Azure para seus clientes que têm um EA (Contrato Enterprise) direto. Transferências de assinatura são permitidas somente para clientes que aceitaram um MCA (Contrato de Cliente da Microsoft) e compraram um plano do Azure com o Programa CSP.

Quando a solicitação é aprovada, o CSP pode fornecer uma fatura combinada para seus clientes. Para saber mais sobre a transferência de assinaturas por CSPs, confira [Obter a propriedade de cobrança das assinaturas do Azure para sua conta do MPA](mpa-request-ownership.md).

>[!IMPORTANT]
> Depois de transferir uma assinatura do EA para um parceiro CSP, qualquer aumento na cota aplicado anteriormente à assinatura do EA será redefinido para o valor padrão. Se uma cota adicional for necessária após a transferência da assinatura, peça ao provedor do CSP para enviar uma solicitação de [aumento de cota](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Outras transferências de assinatura para um parceiro CSP

Para transferir qualquer outra assinatura do Azure para um parceiro CSP, o assinante precisa mover recursos das assinaturas de origem para as assinaturas do CSP. Use as diretrizes a seguir para mover recursos entre assinaturas.

1. Trabalhe com seu parceiro CSP para criar assinaturas do CSP do Azure de destino.
1. Verifique se as assinaturas de origem e destino do CSP estão no mesmo locatário do Azure AD (Active Directory).  
    Não é possível alterar o locatário do Azure AD para uma assinatura do CSP do Azure. Em vez disso, você precisa adicionar ou associar a assinatura de origem ao locatário do CSP do Azure AD. Para obter mais informações, confira [Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Quando você associa uma assinatura a um diretório do Azure AD diferente, os usuários que têm funções atribuídas usando o [Azure RBAC (controle de acesso baseado em função do Azure)](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de assinatura clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    > - As Atribuições de Política também são removidas de uma assinatura quando ela é associada a um diretório diferente.
1. A conta de usuário que você usa para fazer a transferência precisa ter acesso de proprietário do [Azure RBAC](add-change-subscription-administrator.md) nas duas assinaturas.
1. Antes de começar, [valide](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da assinatura de origem para a assinatura de destino.  
    Alguns recursos do Azure não podem ser movidos entre assinaturas. Para exibir a lista completa de recursos do Azure que podem ser movidos, confira [Suporte à operação de mover para recursos](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - O CSP do Azure oferece suporte apenas aos recursos do Azure Resource Manager. Se algum recurso do Azure Resource Manager na assinatura de origem tiver sido criado usando o modelo de implantação clássico do Azure, você precisará migrá-los para o [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) antes da migração. Você precisa ser um parceiro para exibir a página da Web.

1. Verifique se todos os serviços da assinatura de origem usam o modelo do Azure Resource Manager. Em seguida, transfira recursos da assinatura de origem para a assinatura de destino usando o [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Mover recursos do Azure entre assinaturas pode resultar em tempo de inatividade do serviço, dependendo dos recursos presentes nas assinaturas.

## <a name="transfer-csp-subscription-to-other-offer"></a>Transferir assinatura do CSP para outra oferta

Para transferir qualquer outra assinatura de um parceiro CSP para outra oferta do Azure, o assinante precisa mover recursos entre assinaturas de CSP de origem e as assinaturas de destino.

1. Criar assinaturas do Azure de destino.
1. Verifique se as assinaturas de origem e destino estão no mesmo locatário do Azure Active Directory. Para obter mais informações sobre como alterar um locatário do Azure AD, confira [Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Observe que não há suporte para a opção alterar diretório na assinatura CSP. Por exemplo, você está transferindo de um CSP para uma assinatura paga conforme o uso. Você precisa alterar o diretório da assinatura paga conforme o uso de maneira a corresponder ao diretório.

    > [!IMPORTANT]
    >  - Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando o [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de assinatura clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    >  - As Atribuições de Política também são removidas de uma assinatura quando ela é associada a um diretório diferente.

1. A conta de usuário que você usa para fazer a transferência precisa ter acesso de proprietário do [Azure RBAC](add-change-subscription-administrator.md) nas duas assinaturas.
1. Antes de começar, [valide](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da assinatura de origem para a assinatura de destino.
    > [!IMPORTANT]
    >  - Alguns recursos do Azure não podem ser movidos entre assinaturas. Para exibir a lista completa de recursos do Azure que podem ser movidos, confira [Suporte à operação de mover para recursos](../../azure-resource-manager/management/move-support-resources.md).

1. Transfira recursos da assinatura de origem para a assinatura de destino usando o [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Mover recursos do Azure entre assinaturas pode resultar em tempo de inatividade do serviço, dependendo dos recursos presentes nas assinaturas.

## <a name="next-steps"></a>Próximas etapas
- [Obter a propriedade de cobrança das assinaturas do Azure para sua conta do MPA](mpa-request-ownership.md).
- Leia sobre como [Gerenciar contas e assinaturas com a Cobrança do Azure](../index.yml).
