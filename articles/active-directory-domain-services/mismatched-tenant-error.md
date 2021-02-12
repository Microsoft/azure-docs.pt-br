---
title: Corrigir erros de diretório incompatíveis no Azure AD Domain Services | Microsoft Docs
description: Saiba o que significa um erro de diretório incompatível e como resolvê-lo no Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619293"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Resolver erros de diretório incompatíveis para domínios gerenciados Azure Active Directory Domain Services existentes

Se um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS) mostrar um erro de locatário incompatível, você não poderá administrar o domínio gerenciado até ser resolvido. Esse erro ocorrerá se a rede virtual do Azure subjacente for movida para um diretório diferente do Azure AD.

Este artigo explica por que o erro ocorre e como resolvê-lo.

## <a name="what-causes-this-error"></a>O que causa esse erro?

Um erro de diretório incompatível ocorre quando um domínio gerenciado do Azure AD DS e uma rede virtual pertencem a dois locatários diferentes do Azure AD. Por exemplo, você pode ter um domínio gerenciado chamado *aaddscontoso.com* que é executado no locatário do Azure ad da contoso. No entanto, a rede virtual do Azure para o domínio gerenciado faz parte do locatário do Azure AD da Fabrikam.

O controle de acesso baseado em função do Azure (RBAC do Azure) é usado para limitar o acesso aos recursos. Quando você habilita o Azure AD DS em um locatário do Azure AD, os hashes de credenciais são sincronizados com o domínio gerenciado. Esta operação exige que você seja um administrador de locatários para o diretório do Azure AD e o acesso às credenciais deve ser controlado.

Para implantar recursos em uma rede virtual do Azure e controlar o tráfego, você deve ter privilégios administrativos na rede virtual na qual você implanta o domínio gerenciado.

Para que o RBAC do Azure trabalhe de forma consistente e segura para todos os recursos que o Azure AD DS usa, o domínio gerenciado e a rede virtual devem pertencer ao mesmo locatário do Azure AD.

As regras a seguir se aplicam a implantações:

- Um diretório do Azure AD pode ter várias assinaturas do Azure.
- Uma assinatura do Azure pode ter vários recursos, como redes virtuais.
- Um único domínio gerenciado é habilitado para um diretório do Azure AD.
- Um domínio gerenciado pode ser habilitado em uma rede virtual que pertence a qualquer uma das assinaturas do Azure dentro do mesmo locatário do Azure AD.

### <a name="valid-configuration"></a>Configuração válida

No cenário de implantação de exemplo a seguir, o domínio gerenciado da Contoso é habilitado no locatário do Azure AD da contoso. O domínio gerenciado é implantado em uma rede virtual que pertence a uma assinatura do Azure de Propriedade do locatário do Azure AD da contoso.

O domínio gerenciado e a rede virtual pertencem ao mesmo locatário do Azure AD. Esta configuração de exemplo é válida e tem suporte total.

![Configuração válida do locatário do Azure AD DS com o domínio gerenciado e a parte da rede virtual do mesmo locatário do Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configuração de locatários incompatíveis

Neste cenário de implantação de exemplo, o domínio gerenciado da Contoso é habilitado no locatário do Azure AD da contoso. No entanto, o domínio gerenciado é implantado em uma rede virtual que pertence a uma assinatura do Azure de Propriedade do locatário do Azure AD da Fabrikam.

O domínio gerenciado e a rede virtual pertencem a dois locatários diferentes do Azure AD. Esta configuração de exemplo é um locatário incompatível e não tem suporte. A rede virtual deve ser movida para o mesmo locatário do Azure AD que o domínio gerenciado.

![Configuração de locatários incompatíveis](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Resolver erro de locatário incompatível

As duas opções a seguir resolvem o erro de diretório incompatível:

* Primeiro, [exclua o domínio gerenciado](delete-aadds.md) do seu diretório existente do AD do Azure. Em seguida, [crie um domínio gerenciado de substituição](tutorial-create-instance.md) no mesmo diretório do AD do Azure que a rede virtual que você deseja usar. Quando estiver pronto, junte-se a todos os computadores adicionados anteriormente ao domínio excluído ao domínio gerenciado recriado.
* [Mova a assinatura do Azure](../cost-management-billing/manage/billing-subscription-transfer.md) que contém a rede virtual para o mesmo diretório do Azure AD que o domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como solucionar problemas com o Azure AD DS, consulte o [Guia de solução de problemas](troubleshoot.md).
