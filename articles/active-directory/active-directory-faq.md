---
title: Perguntas frequentes sobre o Azure Active Directory | Microsoft Docs
description: As Respostas Frequentes do Azure Active Directory respondem a perguntas sobre o acesso ao Azure e o Azure Active Directory, o gerenciamento de senhas e o acesso ao aplicativo.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2017
ms.author: markvi
ms.openlocfilehash: 8d4460b3059558de2253c6f6a2d2fc8e7564d6d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-faq"></a>Perguntas frequentes sobre o Azure Active Directory
O Azure AD (Azure Active Directory) é uma solução abrangente de IDaaS (identidade como um serviço) que inclui todos os aspectos de identidade, gerenciamento de acesso e segurança.

Para obter mais informações, confira [O que é Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Acessar o Azure e o Azure Active Directory
**P: por que é mostrado "Nenhuma assinatura encontrada" quando tento acessar o Azure AD no Portal Clássico do Azure?**

**R:** para acessar o portal clássico do Azure, cada usuário precisa de permissões com uma assinatura do Azure. Se tiver uma assinatura paga do Office 365 ou Azure AD, acesse [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para obter uma etapa de ativação única. Caso contrário, você precisará ativar uma [conta do Azure](https://azure.microsoft.com/pricing/free-trial/) grátis ou uma assinatura paga.

Para obter mais informações, consulte:

* [Como as assinaturas do Azure são associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerenciar o diretório para sua assinatura do Office 365 no Azure](active-directory-manage-o365-subscription.md)

- - -
**P: qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** o Azure AD fornece recursos comuns de identidade e acesso para todos os serviços Web. Se estiver usando o Office 365, o Microsoft Azure, o Intune ou outras ferramentas, você já estará usando o Azure AD para ajudar a ativar a entrada e o gerenciamento de acesso para todos esses serviços.

Todos os usuários que estão configurados para usar serviços Web são definidos como contas de usuário em uma ou mais instâncias do Azure AD. Você pode configurar essas contas de recursos do Azure AD gratuitamente, como acesso a aplicativos de nuvem.

Serviços pagos do Azure AD, como Enterprise Mobility + Security, complementam outros serviços Web, como Office 365 e Microsoft Azure, com soluções abrangentes de segurança e gerenciamento de escala empresarial.
- - -
**P: por que posso entrar no portal do Azure, mas não no portal clássico do Azure?**

**R:** o portal do Azure não requer uma assinatura válida, e o portal clássico requer uma assinatura válida.  Se não tiver uma assinatura, você não poderá entrar no portal clássico.
- - -
**P: quais são as diferenças entre o Administrador de Assinatura e o Administrador de Diretório?**

**R:** Por padrão, você recebe a função de Administrador de Assinatura quando se inscreve no Azure. Um administrador de assinatura pode usar uma conta da Microsoft ou uma conta corporativa ou de estudante do diretório ao qual a assinatura do Azure está associada.  Essa função está autorizada a gerenciar serviços no portal do Azure.

Se outros usuários precisarem entrar e acessar serviços usando a mesma assinatura, você poderá adicioná-los como coadministradores. Essa função tem os mesmos privilégios de acesso que o administrador de serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure.  Para obter informações adicionais sobre os administradores de assinatura, confira [Como adicionar ou alterar as funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md) e [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).


O Azure AD tem um conjunto diferente de funções administrativas para gerenciar o diretório e os recursos relacionados à identidade.  Esses administradores terão acesso a vários recursos no portal do Azure ou no portal clássico do Azure. A função do administrador determina o que ele pode fazer, como criar ou editar usuários, atribuir funções administrativas a outras pessoas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.  Para obter informações adicionais sobre administradores de diretório do Azure AD e suas funções, confira [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Além disso, os serviços pagos do Azure AD, como Enterprise Mobility + Security, complementam outros serviços Web, como Office 365 e Microsoft Azure, com soluções abrangentes de segurança e gerenciamento de escala empresarial.

- - -
**P: existe um relatório que mostra quando meu licenças de usuário do AD do Azure irá expirar?**

**R:** Não.  Isso não está disponível atualmente.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Introdução ao Azure AD Híbrido


**P: como sair de um locatário quando eu for adicionado como colaborador?**

**R:** quando é adicionado ao locatário de outra organização como um colaborador, você pode usar o "alternador de locatário" no canto superior direito para alternar entre locatários.  Atualmente, não há uma maneira de deixar a organização que faz o convite, e a Microsoft está trabalhando para fornecer essa funcionalidade.  Até que esse recurso esteja disponível, você pode pedir que a organização que o está convidando o remova do locatário.
- - -
**P: como conectar meu diretório local ao Azure AD?**

**R:** você pode conectar o diretório local ao Azure AD usando o Azure AD Connect.

Para saber mais, veja [Integrando identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: como configurar o SSO entre meu diretório local e meus aplicativos de nuvem?**

**R:** você só precisa configurar o SSO (logon único )entre seu diretório local e o Azure AD. Contanto que você acesse seus aplicativos na nuvem por meio do Azure AD, o serviço direciona os usuários automaticamente para que se autentiquem corretamente com suas credenciais locais.

A implementação do SSO do local pode ser facilmente realizada com soluções de federação, como AD FS (Serviços de Federação Active Directory) ou configurando a sincronização de hash de senha. Você pode implantar facilmente as duas opções usando o assistente de configuração do Azure AD Connect.

Para saber mais, veja [Integrando identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: o Azure AD oferece um portal de autoatendimento para usuários em minha organização?**

**R:** sim, o Azure AD oferece o [Painel de Acesso do Azure AD](http://myapps.microsoft.com) para o autoatendimento de usuários e o acesso ao aplicativo. Se é cliente do Office 365, você pode encontrar muitos dos mesmos recursos no portal do Office 365.

Para saber mais, confira [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

- - -
**P: o Azure AD me ajuda a gerenciar a infraestrutura local?**

**R:** Sim. O Azure AD Premium Edition fornece o Azure AD Connect Health. O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização.  

Para saber mais, confira [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Gerenciamento de senhas
**P: posso usar o write-back de senha do Azure AD sem sincronização de senhas? (Nesse cenário, é possível usar a SSPR (redefinição de senha de autoatendimento do Azure AD) com write-back de senha e não armazenar senhas na nuvem?)**

**R:** não é necessário sincronizar suas senhas do Active Directory para que o Azure AD habilite o write-back. Em um ambiente federado, o SSO (logon único) do Azure AD utiliza o diretório local para autenticar o usuário. Esse cenário não requer que a senha local seja acompanhada no Azure AD.

- - -
**P: quanto tempo leva para que uma senha seja gravado no Active Directory local?**

**R:** o write-back de senha opera em tempo real.

Para saber mais, confira [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md).

- - -
**P: posso usar o write-back de senha com senhas que são gerenciadas por um administrador?**

**R:** sim, se você tiver o write-back de senha habilitado, as operações de senha executadas por um administrador serão gravadas de volta no ambiente local.  

Para obter mais respostas a perguntas relacionadas a senhas, confira [Perguntas frequentes sobre gerenciamento de senhas](active-directory-passwords-faq.md).
- - -
**P: o que fazer se eu não lembrar de minha senha existente do Office 365/Azure AD ao tentar alterar a senha?**

**R:** para esse tipo de situação, há algumas opções.  Use SSPR (redefinição de senha de autoatendimento), se estiver disponível.  O funcionamento de SSPR dependerá de como está configurado.  Para saber mais, veja [Como funciona o portal de redefinição de senhas](active-directory-passwords-best-practices.md).

Para usuários do Office 365, o administrador pode redefinir a senha usando as etapas descritas em [Redefinir senhas de usuário](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Para contas do Azure AD, os administradores podem redefinir senhas usando uma das seguintes opções:

- [Redefinir contas no portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Redefinir contas no portal clássico](active-directory-create-users-reset-password.md)
- [Usando o PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Segurança
**P: As contas são bloqueadas após uma quantidade específica de tentativas com falha, ou uma estratégia mais sofisticada é usada?**</br>
Usamos uma estratégia mais sofisticada para bloquear contas.  Ela se baseia no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque.  

**P: Determinadas senhas (comuns) são rejeitadas com as mensagens 'essa senha foi usada muitas vezes', isso se refere a senhas usadas no diretório ativo atual?**</br>
Isso se refere a senhas comuns no mundo todo, como qualquer variante de "Senha" e "123456".

**P: Uma solicitação de entrada de fontes questionáveis (botnets, ponto de extremidade tor) será bloqueada em um locatário B2C, ou isso exige um locatário de edição Basic ou Premium?**</br>
Temos um gateway que filtra solicitações e fornece alguma proteção contra botnets, e ele é aplicado a todos os locatários B2C.

## <a name="application-access"></a>Acesso a aplicativos
**P: onde obter uma lista de aplicativos que estão pré-integrados ao Azure AD e seus recursos?**

**R:** o Azure AD tem mais de 2.600 aplicativos pré-integrados da Microsoft, provedores de serviços de aplicativos e parceiros. Todos os aplicativos pré-integrados dão suporte ao SSO (logon único). O SSO permite que você use suas credenciais organizacionais para acessar os aplicativos. Alguns dos aplicativos também dão suporte ao provisionamento e ao desprovisionamento automatizados.

Para obter uma lista completa dos aplicativos pré-integrados, confira o [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**P: e se o aplicativo de que preciso não estiver no Azure AD Marketplace?**

**R:** com o Azure AD Premium, você pode adicionar e configurar qualquer aplicativo que desejar. Dependendo dos recursos do aplicativo e de suas preferências, você pode configurar o SSO e o provisionamento automatizado.  

Para obter mais informações, consulte:

* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)

- - -
**P: como os usuários entram em aplicativos usando o Azure AD?**

**R:** o Azure AD fornece várias maneiras para que os usuários exibam e acessem os aplicativos, como:

* O painel de acesso do Azure AD
* O iniciador de aplicativos do Office 365
* Logon direto para aplicativos federados
* Links profundos a aplicativos federados, baseado em senha, ou existentes

Para saber mais, confira [Implantar os aplicativos integrados do Azure AD para usuários](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**P: quais são as diferentes maneiras pelas quais o Azure AD habilita a autenticação e o logon único para aplicativos?**

**R:** o Azure AD dá suporte a vários protocolos padronizados para autenticação e autorização, como SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. O Azure AD também dá suporte a cofres de senhas e recursos de entrada automatizada para aplicativos que dão suporte apenas à autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)
* [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Como funciona o logon único com o Active Directory do Azure?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**P: posso adicionar aplicativos que estou executando no local?**

**R:** o Proxy de Aplicativo do Azure AD oferece acesso fácil e seguro aos aplicativos Web locais que você escolhe. Você pode acessar esses aplicativos da mesma maneira como acessa os aplicativos SaaS (software como um serviço) no Azure AD. Não é necessário ter uma VPN nem alterar a infraestrutura de rede.  

Para saber mais, confira [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).

- - -
**P: como exigir a autenticação multifator para usuários que acessam determinado aplicativo?**

**R:** com o acesso condicional do Azure AD, você pode atribuir uma política de acesso exclusiva para cada aplicativo. Em sua política, você pode sempre exigir autenticação multifator ou quando os usuários não estiverem conectados à rede local.  

Para saber mais, confira [Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Azure Active Directory](active-directory-conditional-access.md).

- - -
**P: o que é o provisionamento automatizado de usuário para aplicativos SaaS?**

**R:** use o Azure AD para automatizar a criação, a manutenção e a remoção de identidades de usuário em muitos aplicativos SaaS de nuvem populares.

Para saber mais, confira [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md).

- - -
**P: posso configurar uma conexão LDAP segura com o Azure AD?**

**R:**  Não. O Azure AD não dá suporte ao protocolo LDAP.
