---
title: Recursos de segurança do Azure que ajudam com o gerenciamento de identidade | Microsoft Docs
description: Saiba mais sobre os principais recursos de segurança do Azure que ajudam com o gerenciamento de identidades. Consulte informações sobre tópicos como logon único e proxy reverso.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: d931d3923ff49dde2bea234278c995e79670429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627626"
---
# <a name="azure-identity-management-security-overview"></a>Visão geral da segurança de gerenciamento de identidade do Azure

 Gerenciamento de identidade é o processo de autenticação e autorização [das entidades de segurança](/windows/security/identity-protection/access-control/security-principals). Também envolve controlar informações sobre as entidades (identidades). As entidades de segurança (identidades) podem incluir serviços, aplicativos, usuários, grupos, etc. As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a ti a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem. Essa proteção permite níveis adicionais de validação, como a autenticação multifator e políticas de acesso condicional. O monitoramento de atividade suspeita por meio de alertas, auditoria e relatórios de segurança avançados ajuda a reduzir potenciais problemas de segurança. O [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) fornece SSO (logon único) para milhares de aplicativos SaaS (software como serviço) de nuvem e acesso a aplicativos Web executados localmente.
 
Aproveitando os benefícios de segurança do Microsoft Azure AD (Azure Active Directory), você pode:

* Crie e gerencie uma identidade única para cada usuário em sua empresa híbrida, mantendo usuários, grupos e dispositivos em sincronia. 
* Fornecer acesso de SSO para seus aplicativos, incluindo milhares de aplicativos de SaaS pré-integrados.
* Habilitar a segurança de acesso do aplicativo por meio da imposição da Autenticação Multifator baseada em regras para aplicativos locais e na nuvem.
* Provisionar o acesso remoto seguro a aplicativos Web locais por meio do Proxy de Aplicativo Azure AD.

O objetivo deste artigo é fornecer uma visão geral dos recursos de segurança centrais do Azure que ajudam com o gerenciamento de identidades. Também fornecemos links para artigos que dão os detalhes de cada recurso para que você possa saber mais.  

O artigo se concentra nas seguintes funcionalidades de gerenciamento de identidade centrais do Azure:

* Logon único
* Proxy reverso
* Autenticação Multifator
* RBAC do Azure (controle de acesso baseado em função do Azure)
* Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança
* Gerenciamento de acesso e identidade do consumidor
* Registro de dispositivos
* Privileged Identity Management
* Proteção de identidade
* Gerenciamento de identidade híbrida/conectar Azure Active Directory
* Revisões de acesso do Azure AD

## <a name="single-sign-on"></a>Logon único

SSO significa poder acessar todos os aplicativos e recursos de que você precisa para fazer negócios, conectando-se apenas uma vez usando uma única conta de usuário. Depois de conectado, você pode acessar todos os aplicativos necessários sem a exigência de autenticação (por exemplo, digitar uma senha) uma segunda vez.

Muitas organizações contam com aplicativos SaaS como Microsoft 365, Box e Salesforce para produtividade do usuário. Historicamente, a equipe de TI precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS e os usuários precisavam lembrar uma senha para cada aplicativo SaaS.

O Azure AD estende os ambientes do Active Directory local para a nuvem, permitindo que os usuários usem suas contas organizacionais primárias para se conectar não apenas a recursos corporativos e dispositivos ingressados no domínio, mas também a todos os aplicativos Web e de SaaS necessários para seus trabalhos.

Os usuários não precisam apenas gerenciar vários conjuntos de nomes de usuário e senhas, mas também podem provisionar ou desprovisionar o acesso automaticamente com base nos grupos organizacionais e no status de funcionário deles. O Azure AD introduz controles de governança de acesso e segurança com os quais você pode gerenciar centralmente o acesso dos usuários a aplicativos de SaaS.

Saiba mais:

* [Visão geral do SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Vídeo sobre conceitos básicos de autenticação](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Série de início rápido sobre o gerenciamento de aplicativos](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Proxy reverso

O Proxy de Aplicativo do Azure AD permite que você publique aplicativos locais, como sites do [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US), o [Outlook Web App](/Exchange/clients/outlook-on-the-web/outlook-on-the-web) e aplicativos baseados no [IIS](https://www.iis.net/), em sua rede privada e fornece acesso seguro aos usuários fora da rede. O Proxy de Aplicativo fornece acesso removo e SSO para diversos tipos de aplicativos Web locais com milhares de aplicativos SaaS para os quais o Azure AD já oferece suporte. Os funcionários podem entrar nos aplicativos em casa, em seus próprios dispositivos, e se autenticarem por meio desse proxy baseado em nuvem.

Saiba mais:

* [Habilitar o Proxy de Aplicativo do Azure AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Logon único com Proxy de Aplicativo](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Trabalhando com acesso condicional](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Autenticação Multifator

A autenticação multifator do Azure AD é um método de autenticação que requer o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança a entradas e transações do usuário. A Autenticação Multifator ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ela fornece autenticação forte por meio de uma variedade de opções de verificação: chamada telefônica, mensagem de texto, notificações de aplicativo móvel ou códigos de verificação e tokens OAuth de terceiros.

Saiba mais:

* [Autenticação Multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Autenticação Multifator do Azure AD?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Como a Autenticação Multifator do Azure AD funciona](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>RBAC do Azure

O RBAC do Azure é um sistema de autorização criado em Azure Resource Manager que fornece gerenciamento de acesso refinado de recursos no Azure. O RBAC do Azure permite controlar de forma granular o nível de acesso que os usuários têm. Por exemplo, você pode limitar um usuário para somente gerenciar redes virtuais e outro usuário para gerenciar todos os recursos em um grupo de recursos. O Azure inclui várias funções internas que você pode usar. A seguir são listadas quatro funções internas fundamentais. As três primeiras se aplicam a todos os tipos de recursos.

- [Proprietário](../../role-based-access-control/built-in-roles.md#owner) - Possui acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários. 
- [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) – Pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode permitir acesso a outras pessoas.
- [Leitor](../../role-based-access-control/built-in-roles.md#reader) - Pode exibir os recursos existentes do Azure.
- [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) - Permite gerenciar o acesso do usuário aos recursos do Azure.

Saiba mais:

* [O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?](../../role-based-access-control/overview.md)
* [Funções internas do Azure](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança

Monitoramento de segurança, alertas e relatórios baseados no aprendizado de máquina que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger seus negócios. Você pode usar os relatórios de uso e de acesso do Azure AD para obter visibilidade quanto à integridade e segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

No portal do Azure, os relatórios se enquadram nas categorias a seguir:

* **Relatórios de anomalias**: contêm eventos de entrada que nós identificamos como anômalos. Nossa meta é que você fique ciente dessas atividades e possa determinar se um evento é suspeito ou não.
* **Relatórios de aplicativos integrados**: fornecem um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Azure AD oferece integração com milhares de aplicativos em nuvem.
* **Relatórios de erros**: indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
* **Relatórios específicos do usuário**: exibem dados de atividade de entrada/dispositivo de um usuário específico.
* **Logs de atividades**: contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou 30 dias, bem como alterações de atividades do grupo e atividades de registro e redefinição de senha.

Saiba mais:

* [Exibir seus relatórios de acesso e uso](../../active-directory/reports-monitoring/overview-reports.md)
* [Introdução aos relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)
* [Guia de relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Gerenciamento de acesso e identidade do consumidor

O Azure AD B2C é um serviço de gerenciamento de identidade global e altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Ele pode ser integrado a plataformas móveis e da Web. Seus clientes podem fazer logon em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando novas credenciais.

No passado, desenvolvedores de aplicativos que desejavam inscrever e conectar clientes a seus aplicativos teriam escrito seu próprio código. E eles teriam usado sistemas ou bancos de dados locais para armazenar nomes de usuário e senhas. O Azure AD B2C oferece à sua organização uma maneira melhor de integrar o gerenciamento de identidade do consumidor a aplicativos com a ajuda de uma plataforma segura baseada em padrões e um grande conjunto de políticas extensíveis.

Quando você usa o Azure AD B2C, os consumidores poderão se inscrever nos seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha ou o nome de usuário e a senha).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Visualização do Active Directory B2C do Azure: inscrever e conectar consumidores em seus aplicativos](../../active-directory-b2c/overview.md)
* [Visualização de Azure Active Directory B2C: tipos de aplicativos](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registro de dispositivos

O registro de dispositivos do Azure AD é a base para cenários de [acesso condicional](../../active-directory/devices/device-management-azure-portal.md) com base em dispositivo. Quando um dispositivo é registrado, o registro de dispositivos do Azure AD fornece ao dispositivo uma identidade que ele usa para autenticar o dispositivo quando um usuário entra. O dispositivo autenticado e os atributos do dispositivo podem ser usados para impor políticas de acesso condicional para aplicativos hospedados na nuvem e localmente.

Quando combinada com uma solução de gerenciamento de dispositivo móvel, como o Intune, os atributos do dispositivo no Azure AD são atualizados com informações adicionais sobre o dispositivo. Você pode criar regras de acesso condicional que impõem o acesso de dispositivos para atender aos seus padrões de segurança e conformidade.

Saiba mais:

* [Introdução ao registro de dispositivos do Azure AD](../../active-directory/devices/device-management-azure-portal.md)
* [Registro de dispositivo automático com o Azure AD para dispositivos ingressados no domínio do Windows](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Configurar o registro de dispositivo automático com o Azure AD para dispositivos ingressados no domínio do Windows](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar suas identidades com privilégios e o acesso aos recursos no Azure AD, bem como a outros serviços online da Microsoft, como Microsoft 365 e Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas no Azure ou Microsoft 365 recursos ou em outros aplicativos SaaS. Essa necessidade geralmente significa que as organizações precisam conceder acesso privilegiado permanente no Azure AD aos usuários. Esse acesso é um risco de segurança cada vez maior para recursos hospedados em nuvem porque as organizações não podem monitorar de maneira suficiente o que esses usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa violação de segurança poderá afetar a segurança geral da nuvem da organização. O Azure AD Privileged Identity Management ajuda a mitigar esse risco.

Com Azure AD Privileged Identity Management, você pode:

* Ver quais usuários são administradores do Azure AD.
* Habilite o acesso administrativo just-in-time (JIT) sob demanda aos serviços da Microsoft, como o Microsoft 365 e o Intune.
* Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador.
* Receber alertas sobre o acesso a uma função com privilégios.

Saiba mais:

* [O que é o Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Atribuir funções de diretório do Azure AD no PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade

Azure AD Identity Protection é um serviço de segurança que fornece uma visão consolidada das detecções de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. O Identity Protection tira proveito das funcionalidades de detecção de anomalias existentes do Azure AD, que estão disponíveis por meio dos Relatórios de atividades anômalas do Azure AD. A proteção de identidade também apresenta novos tipos de detecção de riscos que podem detectar anomalias em tempo real.

Saiba mais:

* [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Canal 9: Azure AD e Identity Show: visualização do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Gerenciamento de identidade híbrida/conectar Azure Active Directory

As soluções de identidade da Microsoft abrangem locais e recursos baseados em nuvem, criando uma identidade de usuário único para autenticação e autorização em todos os recursos, independentemente do local. Chamamos isso de identidade híbrida. O Azure AD Connect é a ferramenta da Microsoft criada para atender e atingir suas metas de identidade híbrida. Isso permite que você forneça uma identidade comum para os usuários dos aplicativos do Microsoft 365, Azure e SaaS integrados ao Azure AD. Ela fornece os seguintes recursos:

* Sincronização
* AD FS e integração de federação
* Autenticação de passagem
* Monitoramento de integridade

Saiba mais:

* [Hybrid identity white paper](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog da equipe do Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Revisões de acesso do Azure AD

As revisões de acesso do Azure AD (Azure Active Directory) permitem que as organizações gerenciem com eficiência as associações de grupo e o acesso a aplicativos empresariais e atribuições de função com privilégios.

Saiba mais:

* [Revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Gestão do acesso do usuário com revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)