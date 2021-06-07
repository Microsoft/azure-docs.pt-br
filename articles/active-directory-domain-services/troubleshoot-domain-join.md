---
title: Solucionar problemas de ingresso no domínio com Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas comuns ao tentar ingressar em um domínio uma VM ou conectar um aplicativo a Azure Active Directory Domain Services e você não pode se conectar ou autenticar no domínio gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618596"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Solucionar problemas de ingresso no domínio com um Azure Active Directory Domain Services domínio gerenciado

Ao tentar ingressar em uma VM (máquina virtual) ou conectar um aplicativo a um domínio gerenciado Azure Active Directory Domain Services (AD DS do Azure), você poderá receber um erro informando que não é possível fazer isso. Para solucionar problemas de ingresso no domínio, examine em quais dos seguintes pontos você tem um problema:

* Se você não receber um prompt de autenticação, a VM ou o aplicativo não poderá se conectar ao domínio gerenciado AD DS do Azure.
    * Comece a solucionar [problemas de conectividade para ingressar no domínio](#connectivity-issues-for-domain-join).
* Se você receber um erro durante a autenticação, a conexão com o domínio gerenciado será bem-sucedida.
    * Comece a solucionar problemas [relacionados a credenciais durante o ingresso no domínio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemas de conectividade para ingresso no domínio

Se a VM não conseguir localizar o domínio gerenciado, geralmente há um problema de conexão de rede ou configuração. Examine as seguintes etapas de solução de problemas para localizar e resolver o problema:

1. Verifique se a VM está conectada à mesma rede virtual, ou emparelhada, que o domínio gerenciado. Caso contrário, a VM não poderá localizar e se conectar ao domínio para ingressar.
    * Se a VM não estiver conectada à mesma rede virtual, confirme se o emparelhamento de rede virtual ou a conexão VPN está *ativa* ou *conectada* para permitir que o tráfego flua corretamente.
1. Tente executar o ping no domínio usando o nome de domínio do domínio gerenciado, como `ping aaddscontoso.com` .
    * Se a resposta de ping falhar, tente executar ping nos endereços IP do domínio exibido na página Visão geral no portal para seu domínio gerenciado, como `ping 10.0.0.4` .
    * Se você puder executar o ping com êxito no endereço IP, mas não no domínio, o DNS poderá estar configurado incorretamente. Verifique se você [configurou os servidores DNS de domínio gerenciado para a rede virtual][configure-dns].
1. Tente liberar o cache do resolvedor de DNS na máquina virtual, como `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Configuração do NSG (grupo de segurança de rede)

Quando você cria um domínio gerenciado, um grupo de segurança de rede também é criado com as regras apropriadas para uma operação de domínio bem-sucedida. Se você editar ou criar regras de grupo de segurança de rede adicionais, poderá bloquear involuntariamente as portas necessárias para que o Azure AD DS forneça serviços de conexão e autenticação. Essas regras de grupo de segurança de rede podem causar problemas como a sincronização de senha não ser concluída, os usuários não conseguem entrar ou problemas de ingresso no domínio.

Se você continuar tendo problemas de conexão, examine as seguintes etapas de solução de problemas:

1. Verifique o status de integridade do domínio gerenciado no portal do Azure. Se você tiver um alerta para *AADDS001*, uma regra de grupo de segurança de rede estará bloqueando o acesso.
1. Examine as [portas necessárias e as regras do grupo de segurança de rede][network-ports]. Certifique-se de que nenhuma regra de grupo de segurança de rede aplicada à VM ou à rede virtual à qual você está se conectando bloqueie essas portas de rede.
1. Depois que qualquer problema de configuração do grupo de segurança de rede for resolvido, o alerta *AADDS001* desaparece da página de integridade em cerca de 2 horas. Com a conectividade de rede agora disponível, tente adicionar o domínio à VM novamente.

## <a name="credentials-related-issues-during-domain-join"></a>Problemas relacionados a credenciais durante o ingresso no domínio

Se você receber uma caixa de diálogo que solicita credenciais para ingressar no domínio gerenciado, a VM será capaz de se conectar ao domínio usando a rede virtual do Azure. O processo de ingresso no domínio falha ao autenticar no domínio ou autorização para concluir o processo de ingresso no domínio usando as credenciais fornecidas.

Para solucionar problemas relacionados a credenciais, examine as seguintes etapas de solução de problemas:

1. Tente usar o formato UPN para especificar as credenciais, como `dee@contoso.onmicrosoft.com`. Verifique se esse UPN está configurado corretamente no Azure AD.
    * O *sAMAccountName* para sua conta pode ser gerado automaticamente se houver vários usuários com o mesmo prefixo UPN em seu locatário ou se o prefixo UPN for muito longo. Portanto, o formato *sAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.
1. Tente usar as credenciais para uma conta de usuário que faça parte do domínio gerenciado para ingressar VMs no domínio gerenciado.
1. Certifique-se de que você [habilitou a sincronização de senha][enable-password-sync] e esperou tempo suficiente para a conclusão da sincronização de senha inicial.

## <a name="next-steps"></a>Próximas etapas

Para obter uma compreensão mais profunda dos processos de Active Directory como parte da operação de ingresso no domínio, consulte [problemas de ingresso e autenticação][join-authentication-issues].

Se você ainda tiver problemas para ingressar sua VM no domínio gerenciado, [Encontre ajuda e abra um tíquete de suporte para Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
