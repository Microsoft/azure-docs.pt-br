---
title: "Recursos de segurança do Azure que ajudam com o gerenciamento de identidade | Microsoft Docs"
description: " Este artigo fornece uma visão geral dos recursos de segurança centrais do Azure que ajudam com o gerenciamento de identidades. As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a TI a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis de validação adicionais, como Multi-Factor Authentication e políticas de acesso condicional. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: terrylan
ms.openlocfilehash: 8d00882caf5411240c5f0a3533c78c3dbe361ef2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-identity-management-security-overview"></a>Visão geral da segurança de gerenciamento de identidade do Azure
As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a TI a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis de validação adicionais, como Multi-Factor Authentication e políticas de acesso condicional. O monitoramento de atividade suspeita por meio de alertas, auditoria e relatórios de segurança avançados ajuda a reduzir potenciais problemas de segurança. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) fornece logon único para milhares de aplicativos (SaaS) de nuvem e acesso a aplicativos Web executados de forma local.

Os benefícios de segurança do Azure AD (Active Directory) incluem a capacidade de:

* Criar e gerenciar uma identidade única para cada usuário em sua empresa híbrida, mantendo os usuários, grupos e dispositivos em sincronia
* Fornecer acesso de logon único para seus aplicativos, incluindo milhares de aplicativos SaaS pré-integrados
* Habilitar segurança de acesso do aplicativo por meio da aplicação do Multi-Factor Authentication com base em regras para aplicativos locais e na nuvem
* Provisionar o acesso remoto seguro a aplicativos Web locais por meio do Proxy de Aplicativo do Azure AD

O objetivo deste artigo é fornecer uma visão geral dos recursos de segurança centrais do Azure que ajudam com o gerenciamento de identidades. Também fornecemos links para artigos que dão os detalhes de cada recurso para que você possa saber mais.  

O artigo se concentra nas seguintes funcionalidades de gerenciamento de identidade centrais do Azure:

* Logon único
* Proxy reverso
* Autenticação multifator
* Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança
* Gerenciamento de acesso e identidade do consumidor
* Registro de dispositivos
* Privileged Identity Management
* Identity Protection
* Gerenciamento de identidade híbrida

## <a name="single-sign-on"></a>Logon único
SSO (logon único) significa poder acessar todos os aplicativos e recursos necessários para fazer negócios, conectando-se apenas uma vez usando uma única conta de usuário. Depois de conectado, você pode acessar todos os aplicativos necessários sem a exigência de autenticação (por exemplo, digitar uma senha) uma segunda vez.

Muitas organizações contam com o software como um aplicativo de serviço (SaaS) como o Office 365, Box e Salesforce para produtividade do usuário final. Historicamente, a equipe de TI precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS e os usuários precisavam lembrar uma senha para cada aplicativo SaaS.

O Azure AD estende os ambientes do Active Directory local para a nuvem, permitindo que os usuários usem suas contas organizacionais primárias não apenas para se conectar a recursos corporativos e dispositivos ingressados no domínio, mas também a todos os aplicativos Web e de SaaS necessários para seu trabalho.

Os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, o acesso a aplicativos pode ser provisionado ou desconfigurado automaticamente com base em grupos organizacionais e seu status como funcionário. O Azure AD introduz controles de governança de segurança e acesso que permitem que você gerencie centralmente o acesso dos usuários nos aplicativos SaaS.

Saiba mais:

* [Visão geral do logon único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
* [Integrar o logon único do Azure AD com aplicativos de SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy reverso
O Proxy de Aplicativo do Azure AD permite que você publique aplicativos locais, como sites do [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US), o [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx) e aplicativos baseados no [IIS](http://www.iis.net/), em sua rede privada e fornece acesso seguro aos usuários fora da rede. O Proxy de Aplicativo fornece acesso removo e SSO (logon único) para diversos tipos de aplicativos Web locais com milhares de aplicativos SaaS para os quais o Azure AD já dá suporte. Os funcionários podem fazer logon nos aplicativos em casa, em seus próprios dispositivos, e se autenticarem por meio desse proxy baseado em nuvem.

Saiba mais:

* [Habilitando o Proxy de Aplicativo do AD do Azure.](../active-directory/active-directory-application-proxy-enable.md)
* [Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](../active-directory/active-directory-application-proxy-publish.md)
* [Logon único com Proxy de Aplicativo](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
* [Trabalhando com acesso condicional](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Autenticação multifator
O MFA (Multi-Factor Authentication) é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança às entradas e transações dos usuários. O MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação – chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou código de verificação e tokens OAuth de terceiros.

Saiba mais:

* [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* [Como funciona o Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança
Monitoramento e alertas de segurança e relatórios baseados no aprendizado de máquina que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger seus negócios. Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

No Portal Clássico do Azure, os relatórios são categorizados das seguintes maneiras:

* Relatórios de anomalias: contêm eventos de entrada que nós identificamos como anômalos. Nosso objetivo é que você fique ciente dessas atividades e permitir que você possa tomar uma decisão quanto a um evento ser suspeito ou não.
* Relatórios de aplicativos integrados: fornecem um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
* Relatórios de erros: indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
* Relatórios específicos do usuário: exibem dados de atividade de entrada/dispositivo de um usuário específico.
* Logs de atividades – contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou 30 dias, bem como alterações de atividades do grupo e atividades de registro e redefinição de senha.

Saiba mais:

* [Exibir relatórios de acesso e de uso](../active-directory/active-directory-view-access-usage-reports.md)
* [Introdução aos Relatórios do Active Directory do Azure](../active-directory/active-directory-reporting-getting-started.md)
* [Guia de Relatórios do Active Directory do Azure](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gerenciamento de acesso e identidade do consumidor
O Azure Active Directory B2C é um serviço de gerenciamento de identidade global, altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Ele pode ser integrado a plataformas móveis e da Web. Seus clientes podem fazer logon em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicativos que desejavam inscrever e conectar consumidores a seus aplicativos teriam escrito o seu próprio código. E eles teriam usado sistemas ou bancos de dados locais para armazenar nomes de usuário e senhas. O Azure Active Directory B2C oferece à sua organização uma maneira melhor de integrar o gerenciamento de identidades do consumidor em aplicativos com a ajuda de uma plataforma segura baseada em padrões e um grande conjunto de políticas extensíveis.

Quando você usa o Azure Active Directory B2C, os consumidores poderão se inscrever nos seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha ou o nome de usuário e a senha).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Visualização do Active Directory B2C do Azure: inscrever e conectar consumidores em seus aplicativos](../active-directory-b2c/active-directory-b2c-overview.md)
* [Visualização do Azure Active Directory B2C: tipos de aplicativos](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registro de dispositivos
O registro de dispositivo do Azure AD é a base para cenários de [acesso condicional](../active-directory/active-directory-conditional-access-device-registration-overview.md) com base no dispositivo. Quando um dispositivo é registrado, o Registro de Dispositivo do Azure Active Directory fornece ao dispositivo uma identidade, que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos desse dispositivo podem, em seguida, ser usados para impor políticas de acesso condicional para aplicativos locais e hospedados em nuvem.

Quando combinado com uma solução de MDM (gerenciamento de dispositivo móvel), como Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que imponham que o acesso dos dispositivos atendam aos padrões de segurança e conformidade.

Saiba mais:

* [Introdução ao registro de dispositivos do Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Configurar o registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
O Azure AD (Active Directory) Privileged Identity Management permite gerenciar, controlar e monitorar suas identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas em recursos do Azure ou Office 365 ou outros aplicativos SaaS. Isso geralmente significa que as organizações precisam conceder acesso privilegiado permanente no Azure AD. Esse é um risco de segurança cada vez maior para recursos hospedados em nuvem porque as organizações não podem monitorar de maneira suficiente o que esses usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa falha poderá afetar a segurança geral da nuvem. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

O Gerenciamento de identidades com privilégios do AD do Azure:

* Ver quais usuários são administradores do Azure AD
* Habilitar o acesso administrativo “just in time” para Microsoft Online Services como Office 365 e Intune
* Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador
* Receber alertas sobre o acesso a uma função com privilégios

Saiba mais:

* [Gerenciamento de identidades com privilégios do AD do Azure](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Funções no Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Privileged Identity Management do Azure AD: como adicionar ou remover uma função de usuário](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection
O Azure AD Identity Protection é um serviço de segurança que fornece uma exibição consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. O Identity Protection aproveita as funcionalidades de detecção de anomalias existentes do Azure Active Directory (disponíveis por meio dos Relatórios de Atividade Anômala do Azure AD) e apresenta novos tipos de evento de risco que podem detectar anomalias em tempo real.

Saiba mais:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Canal 9: Azure AD e Identity Show: visualização do Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gerenciamento de identidade híbrida
A abordagem de identidade da Microsoft abrange a nuvem e o local, criando uma única identidade de usuário para autenticação e autorização para todos os recursos, independentemente do local.

Saiba mais:

* [Hybrid identity white paper](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog da equipe do Active Directory](https://blogs.technet.microsoft.com/ad/)
