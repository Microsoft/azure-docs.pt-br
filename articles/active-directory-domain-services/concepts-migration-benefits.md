---
title: Benefícios da migração de implantação clássica no Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre os benefícios da migração de uma implantação clássica do Azure Active Directory Domain Services para o modelo de implantação do Gerenciador de recursos
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620079"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Benefícios da migração do modelo de implantação clássico para o Gerenciador de recursos no Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS do Azure) permite migrar um domínio gerenciado existente que usa o modelo de implantação clássico para o modelo de implantação do Gerenciador de recursos. Os domínios gerenciados do Azure AD DS que usam o modelo de implantação do Gerenciador de recursos fornecem recursos adicionais, como política de senha refinada, logs de auditoria e proteção de bloqueio de conta.

Este artigo descreve os benefícios da migração. Para começar, consulte [migrar Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos][howto-migrate].

> [!NOTE]
> Em 2017, Azure AD Domain Services tornou-se disponível para hospedar em uma rede Azure Resource Manager. Desde então, conseguimos criar um serviço mais seguro usando os recursos modernos do Azure Resource Manager. Como as implantações Azure Resource Manager substituem totalmente as implantações clássicas, as implantações de rede virtual clássica do Azure AD DS serão desativadas em 1º de março de 2023.
>
> Para obter mais informações, consulte o [aviso oficial de reprovação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Benefícios da migração

O processo de migração usa um domínio gerenciado existente que usa o modelo de implantação clássico e se move para usar o modelo de implantação do Gerenciador de recursos. Ao migrar um domínio gerenciado do modelo de implantação clássico para o Gerenciador de recursos, você evita a necessidade de reingressar computadores no domínio gerenciado ou excluir o domínio gerenciado e criar um do zero. As VMs continuam a ser unidas ao domínio gerenciado no final do processo de migração.

Após a migração, o Azure AD DS fornece muitos recursos que estão disponíveis somente para domínios que usam o modelo de implantação do Resource Manager, como o seguinte:

* Suporte refinado [à política de senha][password-policy].
* Velocidades de sincronização mais rápidas entre o Azure AD e o Azure AD Domain Services.
* Dois novos [atributos que são sincronizados do Gerenciador do Azure ad][attributes]  -  *manager* e *EmployeeID*.
* Acesso a controladores de domínio com maior capacidade ao [atualizar a SKU][skus].
* Proteção de bloqueio de conta do AD.
* [Notificações por email para alertas em seu domínio gerenciado][email-alerts].
* [Use pastas de trabalho do Azure e o Azure monitor para exibir logs de auditoria e atividade de entrada][workbooks].
* Em regiões com suporte, [zonas de disponibilidade do Azure][availability-zones].
* Integrações com outros produtos do Azure, como [arquivos do Azure][azure-files], [insights de HD][hd-insights]e [área de trabalho virtual do Windows][wvd].
* O suporte tem acesso a mais telemetria e pode ajudar a solucionar problemas com mais eficiência.
* Criptografia em repouso usando o [Azure Managed disks][managed-disks] para os dados nos controladores de domínio gerenciados.

Os domínios gerenciados que usam um modelo de implantação do Gerenciador de recursos ajudam você a se manter atualizado com os novos recursos mais recentes. Novos recursos não estão disponíveis para domínios gerenciados que usam o modelo de implantação clássico.

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte [migrar Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
