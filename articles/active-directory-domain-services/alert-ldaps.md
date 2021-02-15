---
title: Resolver alertas LDAP seguros no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas e resolver alertas comuns com LDAP seguro para Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620300"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: LDAP Seguro alertas no Azure Active Directory Domain Services

Os aplicativos e serviços que usam o protocolo LDAP para se comunicar com Azure Active Directory Domain Services (AD DS do Azure) podem ser [configurados para usar o LDAP seguro](tutorial-configure-ldaps.md). Um certificado apropriado e as portas de rede necessárias devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda você a entender e resolver alertas comuns com acesso LDAP seguro no Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: configuração de rede LDAP Seguro

### <a name="alert-message"></a>Mensagem de alerta

*LDAP Seguro pela Internet está habilitada para o domínio gerenciado. No entanto, o acesso à porta 636 não é bloqueado usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado para ataques de força bruta de senha.*

### <a name="resolution"></a>Resolução

Quando você habilita o LDAP seguro, é recomendável criar regras adicionais que restrinjam o acesso de LDAPs de entrada a endereços IP específicos. Essas regras protegem o domínio gerenciado contra ataques de força bruta. Para atualizar o grupo de segurança de rede para restringir o acesso à porta TCP 636 para LDAP seguro, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG* e selecione **regras de segurança de entrada**
1. Selecione **+ Adicionar** para criar uma regra para a porta TCP 636. Se necessário, selecione **avançado** na janela para criar uma regra.
1. Para a **origem**, escolha *endereços IP* no menu suspenso. Insira os endereços IP de origem que você deseja conceder acesso para tráfego LDAP seguro.
1. Escolha *qualquer* como **destino** e, em seguida, insira *636* para os intervalos de **porta de destino**.
1. Defina o **protocolo** como *TCP* e a **ação** a *permitir*.
1. Especifique a prioridade para a regra e insira um nome como *RestrictLDAPS*.
1. Quando estiver pronto, selecione **Adicionar** para criar a regra.

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

> [!TIP]
> A porta TCP 636 não é a única regra necessária para que o AD DS do Azure seja executado sem problemas. Para saber mais, confira o [Azure AD DS grupos de segurança de rede e portas necessárias](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Expiração do certificado LDAP Seguro

### <a name="alert-message"></a>Mensagem de alerta

*O certificado LDAP seguro para o domínio gerenciado expirará em [data]].*

### <a name="resolution"></a>Resolução

Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Aplique o certificado de substituição à AD DS do Azure e distribua o certificado para todos os clientes que se conectam usando o LDAP seguro.

## <a name="next-steps"></a>Próximas etapas

Se ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
