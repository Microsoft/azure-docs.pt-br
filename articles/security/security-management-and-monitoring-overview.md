---
title: "Visão geral de monitoramento e gerenciamento de segurança do Azure | Microsoft Docs"
description: " O Azure fornece mecanismos de segurança para auxiliar no gerenciamento e monitoramento de serviços de nuvem e máquinas virtuais do Azure.  Este artigo fornece uma visão geral desses recursos e serviços de segurança básicos. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral de monitoramento e gerenciamento de segurança do Azure
O Azure fornece mecanismos de segurança para auxiliar no gerenciamento e monitoramento de serviços de nuvem e máquinas virtuais do Azure. Este artigo fornece uma visão geral desses recursos e serviços de segurança básicos. São fornecidos links para artigos que apresentam detalhes de cada um para que você possa saber mais sobre eles.

A segurança de seus serviços em nuvem da Microsoft é uma parceria e responsabilidade compartilhada entre você e a Microsoft. Responsabilidade compartilhada significa que a Microsoft é responsável pelo Microsoft Azure e pela segurança física de seus data centers (com o uso de proteções de segurança, como portas de entrada bloqueadas com acesso por crachá, grades e vigilância). Além disso, o Azure fornece fortes níveis de segurança de nuvem na camada de software que atendem às necessidades de segurança, privacidade e conformidade de seus clientes exigentes.

Você é o proprietário de seus dados e identidades, tem a responsabilidade de protegê-los, além de ser responsável pela segurança de seus recursos locais e dos componentes de nuvem que estão sob seu controle. A Microsoft fornece controles de segurança e funcionalidades para ajudá-lo a proteger seus dados e aplicativos. Seu grau de responsabilidade pela segurança se baseia no tipo de serviço de nuvem.

A tabela a seguir resume a proporção de responsabilidade assumida pela Microsoft e pelo cliente.

![Responsabilidade compartilhada][1]

Para uma análise aprofundada do gerenciamento de segurança, veja [Gerenciamento de segurança no Azure](azure-security-management.md).

Aqui estão os principais recursos que serão abordados neste artigo:

* Controle de Acesso Baseado em Função
* Antimalware
* Multi-Factor Authentication
* ExpressRoute
* Gateways de rede virtual
* Privileged Identity Management
* Identity Protection
* Central de Segurança

## <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função
O RBAC (Controle de Acesso Baseado em Função) fornece o gerenciamento de acesso refinado para os recursos do Azure. Com o RBAC, é possível conceder às pessoas apenas a quantidade de acesso de que precisam para realizar seus trabalhos.  O RBAC também pode ajudar a garantir que as pessoas perderão o acesso aos recursos na nuvem quando saírem da organização.

Saiba mais:

* [Blog da equipe do Active Directory sobre o RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware
Com o Azure, você pode usar software antimalware dos principais fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky, para ajudar a proteger suas máquinas virtuais contra arquivos maliciosos, adware e outras ameaças.

O Antimalware da Microsoft oferece a capacidade de instalar um agente de antimalware para funções de PaaS e máquinas virtuais. Com base no System Center Endpoint Protection, esse recurso leva para a nuvem a já comprovada tecnologia de segurança local.

Também oferecemos uma integração profunda dos produtos [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ da Trend na plataforma Azure. DeepSecurity é uma solução antivírus e SecureCloud é uma solução de criptografia. O DeepSecurity é implantado nas VMs com um modelo de extensão. Com a interface do usuário do portal e o PowerShell, você pode optar por usar o DeepSecurity em novas VMs que estão sendo iniciadas ou nas VMs virtuais existentes que já foram implantadas.

Também há suporte para o SEP (Symantec End Point Protection) no Azure. Por meio da integração do portal, os clientes podem especificar que pretendem usar o SEP em uma VM. O SEP pode ser instalado em uma nova VM por meio do portal do Azure ou pode ser instalado em uma VM existente usando o PowerShell.

Saiba mais:

* [Implantando soluções antimalware em máquinas virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](azure-security-antimalware.md)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
O MFA (Multi-Factor Authentication) é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança às entradas e transações dos usuários. O MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação – chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou código de verificação e tokens OATH de terceiros.

Saiba mais:

* [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* [Como funciona o Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
A Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização. As conexões do ExpressRoute não passam pela Internet pública. Isso permite que as conexões do ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

Saiba mais:

* [Visão Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual
Os Gateways de VPN, também chamados de Gateways de Rede Virtual do Azure, são usados para enviar o tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure (VNet-to-VNet).  Os gateways de VPN fornecem conectividade segura entre instalações entre o Azure e sua infraestrutura.

Saiba mais:

* [Sobre gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure Network Security Overview](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Às vezes, os usuários precisam executar operações com privilégios em recursos do Azure ou em outros aplicativos SaaS. Em geral, isso significa que as organizações devem conceder a eles acesso com privilégios permanente no Azure AD (Azure Active Directory). Esse é um risco de segurança cada vez maior para os recursos hospedados na nuvem, devido ao fato de as organizações não conseguirem monitorar de maneira adequada o que esses usuários estão fazendo com seu acesso com privilégios.
Além disso, se uma conta de usuário com acesso com privilégios for comprometida, essa violação poderá afetar a segurança geral da nuvem. O Privileged Identity Management do Azure AD ajuda a resolver esse risco, com a redução do tempo de exposição dos privilégios e o aumento da visibilidade do uso.  

O Privileged Identity Management introduz o conceito de um administrador temporário para uma função ou o acesso de administrador “Just-In-Time”, que se trata de um usuário que precisa concluir um processo de ativação para essa função atribuída. O processo de ativação altera a atribuição do usuário para uma função no Azure AD, de inativo para ativo, durante um período especificado, como oito horas.

Saiba mais:

* [Gerenciamento de identidades com privilégios do AD do Azure](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
O Identity Protection do Azure AD (Azure Active Directory) fornece uma exibição consolidada das atividades de entrada suspeitas e das vulnerabilidades potenciais, com o objetivo de ajudar a proteger seus negócios. O Identity Protection detecta atividades suspeitas de usuários e identidades (administrador) com privilégios baseadas em sinais, tais como ataques de força bruta, perda de credenciais e entradas por meio de locais desconhecidos e dispositivos infectados.

Ao fornecer notificações e correções recomendadas, o Identity Protection ajuda a atenuar os riscos em tempo real. Ele calcula a severidade do risco do usuário, e você pode configurar políticas baseadas em risco para ajudar automaticamente a proteger o acesso ao aplicativo contra ameaças futuras.

Saiba mais:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Canal 9: Azure AD e Identity Show: visualização do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Central de Segurança
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança ajuda a otimizar e monitorar a segurança de seus recursos do Azure:

* Permitindo que você defina políticas para seus recursos de assinatura do Azure de acordo com as necessidades de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.
* Monitorando o estado das máquinas virtuais, da redes e dos aplicativos do Azure.
* Fornecendo uma lista de alertas de segurança priorizados, incluindo alertas de soluções de parceiros integradas, juntamente com as informações necessárias para investigar rapidamente e recomendações sobre como corrigir um ataque.

Saiba mais:

* [Introdução à Central de Segurança do Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
