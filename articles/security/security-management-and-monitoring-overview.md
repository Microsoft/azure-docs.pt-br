---
title: Visão geral de monitoramento e gerenciamento de segurança do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos de segurança e serviços que o Azure fornece para auxiliar no gerenciamento e no monitoramento de serviços de nuvem e máquinas virtuais do Azure.
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
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: f79f94c277b02a9f377b90bf74763ac617f65c16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597915"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral de gerenciamento e monitoramento de segurança do Azure

O Azure fornece mecanismos de segurança para auxiliar no gerenciamento e monitoramento de serviços de nuvem e máquinas virtuais (VMs) do Azure. Este artigo fornece uma visão geral desses recursos e serviços de segurança básicos. São fornecidos links para artigos que apresentam detalhes de cada um para que você possa saber mais sobre eles.

A segurança de seus serviços em nuvem da Microsoft é uma parceria e uma responsabilidade compartilhada entre você e a Microsoft. A Microsoft é responsável pela plataforma do Azure e pela segurança física de seus data centers (com o uso de proteções de segurança, como portas de entrada bloqueadas com acesso por crachá, grades e vigilância). O Azure fornece fortes níveis de segurança de nuvem na camada de software que atendem às necessidades de segurança, privacidade e conformidade de seus clientes.

Você é o proprietário de seus dados e identidades, tem a responsabilidade de protegê-los, além de ser responsável pela segurança de seus recursos locais e dos componentes de nuvem que estão sob seu controle. A Microsoft fornece controles de segurança e funcionalidades para ajudá-lo a proteger seus dados e aplicativos. Seu grau de responsabilidade pela segurança se baseia no tipo de serviço de nuvem.

A tabela a seguir resume a proporção de responsabilidade assumida entre a Microsoft e o cliente.

![Responsabilidade compartilhada][1]

Para obter mais informações sobre gerenciamento de segurança, consulte [Gerenciamento de segurança no Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

O RBAC (Controle de Acesso Baseado em Função) fornece o gerenciamento de acesso detalhado para os recursos do Azure. Usando o RBAC, é possível conceder às pessoas apenas a quantidade de acesso de que precisam para realizar seus trabalhos. O RBAC também pode ajudar a garantir que as pessoas perderão o acesso aos recursos na nuvem quando saírem da organização.

Saiba mais:

* [Blog da equipe do Active Directory sobre o RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controle de Acesso Baseado em Função do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Com o Azure, você pode usar um software antimalware dos principais fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky. Esse software ajuda a proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças.

O Antimalware da Microsoft para Máquinas Virtuais e Serviços de Nuvem do Azure oferece a capacidade de instalar um agente de antimalware para funções de PaaS e máquinas virtuais. Com base no System Center Endpoint Protection, esse recurso leva para a nuvem a já comprovada tecnologia de segurança local.

Também oferecemos uma integração profunda dos produtos [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) da Trend na plataforma Azure. Deep Security é uma solução antivírus, e SecureCloud é uma solução de criptografia. O Deep Security é implantado nas VMs por meio de um modelo de extensão. Usando a interface do usuário do portal do Azure e o PowerShell, você pode optar por usar o Deep Security em novas VMs que estão sendo iniciadas ou nas VMs virtuais existentes que já foram implantadas.

Também há suporte para o SEP (Symantec Endpoint Protection) no Azure. Por meio da integração do portal, você pode especificar que pretende usar o SEP em uma VM. O SEP pode ser instalado em uma nova VM por meio do portal do Azure ou instalado em uma VM existente usando o PowerShell.

Saiba mais:

* [Implantando soluções antimalware em máquinas virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](azure-security-antimalware.md)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Novas opções de antimalware para proteção das Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticação Multifator

A Autenticação Multifator do Azure é um método de autenticação que requer o uso de mais de um método de verificação. Ele adiciona uma segunda camada de segurança crítica para logons de usuário e transações. 

A Autenticação Multifator ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação (chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou código de verificação) e tokens OATH de terceiros.

Saiba mais:

* [Autenticação Multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Autenticação Multifator do Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Como funciona a Autenticação Multifator do Azure](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Você pode usar o Azure ExpressRoute para estender suas redes locais até o Microsoft Cloud por meio de uma conexão privada dedicada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Azure, o Office 365 e o CRM Online. A conectividade pode ser de:

* Um rede de qualquer ponto a qualquer ponto (IP VPN).
* Uma rede Ethernet ponto a ponto.
* Um conexão cruzada virtual por meio de um provedor de conectividade em uma instalação de colocalização. 

As conexões do ExpressRoute não passam pela Internet pública. Elas podem oferecer mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.

Saiba mais:

* [Visão Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Os gateways de VPN, também chamados de gateways de rede virtual do Azure, são usados para enviar o tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure (rede para rede). Os gateways de VPN fornecem conectividade segura entre instalações entre o Azure e sua infraestrutura.

Saiba mais:

* [Sobre gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Visão geral da segurança de rede do Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Às vezes, os usuários precisam executar operações com privilégios em recursos do Azure ou em outros aplicativos SaaS. Em geral, isso significa que as organizações concedem a eles acesso com privilégios permanente no Azure AD (Azure Active Directory). 

Esse é um risco de segurança cada vez maior para os recursos hospedados na nuvem, devido ao fato de as organizações não conseguirem monitorar de maneira adequada o que esses usuários estão fazendo com seu acesso com privilégios. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa violação de segurança poderá afetar a segurança geral da nuvem de uma organização. O Privileged Identity Management do Azure AD ajuda a resolver esse risco, com a redução do tempo de exposição dos privilégios e o aumento da visibilidade do uso.  

O Privileged Identity Management apresenta o conceito de um administrador temporário para uma função ou um acesso de administrador "just in time". Esse tipo de administrador é um usuário que precisa concluir um processo de ativação para essa função atribuída. O processo de ativação altera a atribuição do usuário para uma função no Azure AD, de inativo para ativo, para um período de tempo especificado.

Saiba mais:

* [Gerenciamento de identidades com privilégios do AD do Azure](../active-directory/privileged-identity-management/pim-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

O Azure Active Directory Identity Protection fornece uma exibição consolidada das atividades de entrada suspeitas e das vulnerabilidades potenciais, com o objetivo de ajudar a proteger seus negócios. A Proteção de Identidade detecta atividades suspeitas para usuários e identidades com privilégios (administrador), com base em sinais como:

* Ataques de força bruta.
* Credenciais vazadas.
* Logons de locais desconhecidos e dispositivos infectados.

Ao fornecer notificações e correções recomendadas, o Identity Protection ajuda a atenuar os riscos em tempo real. Ela calcula a gravidade de risco do usuário. Você pode configurar políticas baseadas em risco para ajudar automaticamente a proteger o acesso ao aplicativo contra ameaças futuras.

Saiba mais:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Canal 9: Azure AD e Identity Show: Versão Prévia do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Central de Segurança

A Central de Segurança do Azure ajuda você a evitar, detectar e responder a ameaças. A Central de Segurança proporciona a você maior visibilidade e controle da segurança de seus recursos do Azure. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ela ajuda a detectar ameaças que poderiam não ser notadas de outra forma e funciona com um amplo ecossistema de soluções de segurança.

A Central de Segurança ajuda a otimizar e monitorar a segurança de seus recursos do Azure:

* Permitindo que você defina políticas para seus recursos de assinatura do Azure com:
  * As necessidades de segurança da sua empresa.
  * O tipo de aplicativos ou a confidencialidade dos dados em cada assinatura.
* Monitorando o estado das máquinas virtuais, da redes e dos aplicativos do Azure.
* Fornecendo uma lista priorizada de alertas de segurança, incluindo alertas de soluções integradas de parceiros. Ela também fornece as informações de que você precisa para investigar rapidamente um ataque e recomendações sobre como corrigi-lo.

Saiba mais:

* [Introdução à Central de Segurança do Azure](../security-center/security-center-intro.md)
* [Melhorar sua classificação de segurança na Central de Segurança do Azure](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Gráfico de Segurança Inteligente

O Gráfico de Segurança Inteligente fornece proteção contra ameaças em tempo real nos serviços e produtos da Microsoft. Ele usa análises avançadas que vinculam uma grande quantidade de dados de segurança e inteligência de ameaça para fornecer insights que podem reforçar a segurança da organização. A Microsoft usa análises avançadas, processando mais de 450 bilhões de autenticações por mês, verificando a presença de malware e phishing em 400 bilhões de emails e atualizando um bilhão de dispositivos, para gerar insights mais avançados. Esses insights podem ajudar sua organização detectar e responder a ataques rapidamente.

* [Gráfico de Segurança Inteligente](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

