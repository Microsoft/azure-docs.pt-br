---
title: Resolver alertas do grupo de segurança de rede no Azure AD DS | Microsoft Docs
description: Saiba como solucionar problemas e resolver alertas de configuração de grupo de segurança de rede para Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693912"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: alertas de configuração de rede no Azure Active Directory Domain Services

Para permitir que aplicativos e serviços se comuniquem corretamente com um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS), as portas de rede específicas devem estar abertas para que o tráfego flua. No Azure, você controla o fluxo de tráfego usando grupos de segurança de rede. O status de integridade de um domínio gerenciado do Azure AD DS mostrará um alerta se as regras do grupo de segurança de rede necessárias não estiverem em vigor.

Este artigo ajuda você a entender e resolver alertas comuns para problemas de configuração do grupo de segurança de rede.

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: erro de rede

### <a name="alert-message"></a>Mensagem de alerta

*A Microsoft não consegue acessar os controladores de domínio para este domínio gerenciado. Isso pode acontecer se um NSG (grupo de segurança de rede) configurado em sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é se há uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.*

Regras de grupo de segurança de rede inválidas são a causa mais comum de erros de rede para o Azure AD DS. O grupo de segurança de rede para a rede virtual deve permitir o acesso a portas e protocolos específicos. Se essas portas estiverem bloqueadas, a plataforma do Azure não poderá monitorar ou atualizar o domínio gerenciado. A sincronização entre o diretório do Azure AD e o Azure AD DS também é afetada. Certifique-se de manter as portas padrão abertas para evitar a interrupção no serviço.

## <a name="default-security-rules"></a>Regras de segurança padrão

As regras de segurança de entrada e saída padrão a seguir são aplicadas ao grupo de segurança de rede para um domínio gerenciado. Essas regras mantêm o Azure AD DS seguro e permitem que a plataforma do Azure monitore, gerencie e atualize o domínio gerenciado.

### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Prioridade | Nome | Porta | Protocolo | Fonte | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Qualquer | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Qualquer | Negar<sup>1</sup> |
| 65000    | AllVnetInBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Qualquer | Qualquer | AzureLoadBalancer | Qualquer | Allow |
| 65500    | DenyAllInBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |


<sup>1</sup> Opcional para depuração. Permitir quando necessário para solução de problemas avançada.

> [!NOTE]
> Você também pode ter uma regra adicional que permita o tráfego de entrada se [Configurar o LDAP seguro][configure-ldaps]. Essa regra adicional é necessária para a comunicação de LDAPs correta.

### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Prioridade | Nome | Porta | Protocolo | Fonte | Destino | Ação |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualquer | Qualquer | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Qualquer | Qualquer |  Qualquer | Internet | Allow |
| 65500    | DenyAllOutBound | Qualquer | Qualquer | Qualquer | Qualquer | Negar |

>[!NOTE]
> O Azure AD DS precisa de acesso irrestrito de saída da rede virtual. Não recomendamos que você crie regras adicionais que restrinjam o acesso de saída para a rede virtual.

## <a name="verify-and-edit-existing-security-rules"></a>Verificar e editar as regras de segurança existentes

Para verificar as regras de segurança existentes e verificar se as portas padrão estão abertas, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Na página **visão geral** , as regras de segurança de entrada e saída existentes são mostradas.

    Examine as regras de entrada e saída e compare com a lista de regras necessárias na seção anterior. Se necessário, selecione e exclua as regras personalizadas que bloqueiam o tráfego necessário. Se alguma das regras necessárias estiver ausente, adicione uma regra na próxima seção.

    Depois de adicionar ou excluir regras para permitir o tráfego necessário, a integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

### <a name="add-a-security-rule"></a>Adicionar uma regra de segurança

Para adicionar uma regra de segurança ausente, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione grupos de **segurança de rede**.
1. Escolha o grupo de segurança de rede associado ao domínio gerenciado, como *AADDS-contoso.com-NSG*.
1. Em **configurações** no painel esquerdo, clique em regras de *segurança de entrada* ou em *regras de segurança de saída* , dependendo de qual regra você precisa adicionar.
1. Selecione **Adicionar** e crie a regra necessária com base na porta, no protocolo, na direção, etc. Quando estiver pronto, selecione **OK**.

Leva alguns minutos para que a regra de segurança seja adicionada e mostrada na lista.

## <a name="next-steps"></a>Próximas etapas

Se ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
