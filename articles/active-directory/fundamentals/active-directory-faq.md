---
title: Perguntas frequentes sobre o Azure Active Directory B2C | Microsoft Docs
description: Perguntas e respostas sobre o Azure e Azure Active Directory, gerenciamento de senhas e acesso a aplicativos comuns.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b9f76eba9697d6ad47ecf8dc51689538923f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094469"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Perguntas frequentes sobre o Azure Active Directory
O Azure AD (Azure Active Directory) é uma solução abrangente de IDaaS (identidade como um serviço) que inclui todos os aspectos de identidade, gerenciamento de acesso e segurança.

Para obter mais informações, confira [O que é Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Acessar o Azure e o Azure Active Directory
**P: Por que é mostrado "Nenhuma assinatura encontrada" quando tento acessar o Microsoft Azure AD no portal do Azure?**

**R:** Para acessar o portal do Azure, cada usuário precisa de permissões com uma assinatura do Azure. Se você não tiver uma assinatura paga do Microsoft 365 ou do Azure AD, será necessário ativar uma [conta gratuita do Azure](https://azure.microsoft.com/free/
) ou uma assinatura paga.

Para obter mais informações, consulte:

* [Como as assinaturas do Azure são associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**P: Qual é a relação entre o Azure AD, o Microsoft 365 e o Azure?**

**R:** O Microsoft Azure Active Directory fornece recursos comuns de identidade e acesso para todos os serviços Web. Se você estiver usando Microsoft 365, Microsoft Azure, Intune ou outros, você já está usando o Azure AD para ajudar a ativar o gerenciamento de acesso e logon para todos esses serviços.

Todos os usuários que estão configurados para usar serviços Web são definidos como contas de usuário em uma ou mais instâncias do Azure AD. Você pode configurar essas contas de recursos do Azure AD gratuitamente, como acesso a aplicativos de nuvem.

Os serviços pagos do Azure AD como Enterprise Mobility + Security complementam outros serviços Web como Microsoft 365 e Microsoft Azure com soluções abrangentes de gerenciamento e segurança de escala empresarial.

---

**P:  Quais são as diferenças entre o proprietário e o Administrador Global?**

**R:** Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de Administrador Global para o diretório. Um proprietário pode usar uma conta da Microsoft ou uma conta corporativa ou de estudante do diretório ao qual a assinatura do Azure está associada.  Essa função está autorizada a gerenciar serviços no portal do Azure.

Se outros usuários precisarem entrar e acessar serviços usando a mesma assinatura, você pode atribuí-los à [função interna apropriada](../../role-based-access-control/built-in-roles.md). Para obter informações adicionais, consulte [atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de Administrador Global para o diretório. O Administrador Global tem acesso a todos os recursos de diretório do Microsoft Azure Active Directory. O Microsoft Azure Active Directory tem um conjunto diferente de funções administrativas para gerenciar o diretório e os recursos relacionados à identidade. Esses administradores terão acesso a vários recursos no portal do Azure. A função do administrador determina o que eles podem fazer, como criar ou editar usuários, atribuir funções administrativas a outros usuários, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.  Para obter informações adicionais sobre os administradores de diretório do Microsoft Azure Active Directory e suas funções, consulte [atribuir um usuário a funções de administrador no Microsoft Azure Active Directory](active-directory-users-assign-role-azure-portal.md) e [atribuindo funções de administrador no Microsoft Azure Active Directory](../roles/permissions-reference.md).

Além disso, os serviços pagos do Azure AD como Enterprise Mobility + Security complementam outros serviços da Web, como Microsoft 365 e Microsoft Azure, com soluções abrangentes de gerenciamento e segurança de escala empresarial.

---
**P: Existe um relatório que mostra quando minhas licenças de usuário do Microsoft Azure Active Directory irão expirar?**

**R:** Não.  Isso não está disponível atualmente.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Introdução ao Azure AD Híbrido


**P: Como sair de um locatário quando eu for adicionado como colaborador?**

**R:** Quando é adicionado ao locatário de outra organização como um colaborador, você pode usar o "alternador de locatário" no canto superior direito para alternar entre locatários.  Atualmente, não há uma maneira de deixar a organização que faz o convite, e a Microsoft está trabalhando para fornecer essa funcionalidade.  Até que esse recurso esteja disponível, você pode pedir que a organização que o está convidando o remova do locatário.

---
**P: Como conectar meu diretório local ao Microsoft Azure Active Directory?**

**R:** Você pode conectar o diretório local ao Microsoft Azure Active Directory usando o Microsoft Azure Active Directory Connect.

Para saber mais, veja [Integrando identidades locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**P: Como configurar o SSO entre meu diretório local e meus aplicativos de nuvem?**

**R:** Você só precisa configurar o SSO (logon único) entre seu diretório local e o Microsoft Azure Active Directory. Contanto que você acesse seus aplicativos na nuvem por meio do Azure AD, o serviço direciona os usuários automaticamente para que se autentiquem corretamente com suas credenciais locais.

A implementação do SSO do local pode ser facilmente realizada com soluções de federação, como AD FS (Serviços de Federação Active Directory) ou configurando a sincronização de hash de senha. Você pode implantar facilmente as duas opções usando o assistente de configuração do Azure AD Connect.

Para saber mais, veja [Integrando identidades locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**P: O Microsoft Azure Active Directory oferece um portal de autoatendimento para usuários em minha organização?**

**R:** Sim, o Microsoft Azure Active Directory oferece o [Painel de Acesso do Azure AD](https://myapps.microsoft.com) para o autoatendimento de usuários e o acesso ao aplicativo. Se você for um cliente Microsoft 365, poderá encontrar muitos dos mesmos recursos no [portal do Office 365](https://portal.office.com).

Para saber mais, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

---
**P: O Microsoft Azure Active Directory me ajuda a gerenciar a infraestrutura local?**

**R:** Sim. O Azure AD Premium Edition fornece o Azure AD Connect Health. O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização.  

Para saber mais, confira [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Gerenciamento de senhas
**P: Posso usar o write-back de senha do Microsoft Azure Active Directory sem sincronização de senhas? (Nesse cenário, é possível usar a SSPR (redefinição de senha de autoatendimento do Azure AD) com write-back de senha e não armazenar senhas na nuvem?)**

**R:** Não é necessário sincronizar suas senhas do Active Directory para que o Azure AD habilite o write-back. Em um ambiente federado, o SSO (logon único) do Azure AD utiliza o diretório local para autenticar o usuário. Esse cenário não requer que a senha local seja acompanhada no Azure AD.

---
**P: Quanto tempo leva para que uma senha seja gravado no Active Directory local?**

**R:** O write-back de senha opera em tempo real.

Para saber mais, confira [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr.md).

---
**P: Posso usar o write-back de senha com senhas que são gerenciadas por um administrador?**

**R:** Sim, se você tiver o write-back de senha habilitado, as operações de senha executadas por um administrador serão gravadas de volta no ambiente local.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Para obter mais respostas a perguntas relacionadas a senhas, confira [Perguntas frequentes sobre gerenciamento de senhas](../authentication/active-directory-passwords-faq.md).
---
**P: o que posso fazer se eu não me lembrar da minha senha existente do Microsoft 365/Azure AD ao tentar alterar minha senha?**

**R:** Para esse tipo de situação, há algumas opções.  Use SSPR (redefinição de senha de autoatendimento), se estiver disponível.  O funcionamento de SSPR dependerá de como está configurado.  Para saber mais, veja [Como funciona o portal de redefinição de senhas](../authentication/howto-sspr-deployment.md).

Para Microsoft 365 usuários, seu administrador pode redefinir a senha usando as etapas descritas em [redefinir senhas de usuário](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Para contas do Azure AD, os administradores podem redefinir senhas usando uma das seguintes opções:

- [Redefinir contas no portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Como usar o PowerShell](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Segurança
**P: As contas são bloqueadas após uma quantidade específica de tentativas com falha, ou uma estratégia mais sofisticada é usada?**

Usamos uma estratégia mais sofisticada para bloquear contas.  Ela se baseia no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque.  

**P:  Determinadas senhas (comuns) são rejeitadas com as mensagens 'essa senha foi usada muitas vezes', isso se refere a senhas usadas no diretório ativo atual?**

Isso se refere a senhas comuns no mundo todo, como qualquer variante de "Senha" e "123456".

**P: Uma solicitação de entrada de fontes questionáveis (botnets, ponto de extremidade tor) será bloqueada em um locatário B2C, ou isso exige um locatário de edição Basic ou Premium?**

Temos um gateway que filtra solicitações e fornece alguma proteção contra botnets, e ele é aplicado a todos os locatários B2C.

## <a name="application-access"></a>Acesso a aplicativos

**P: Onde obter uma lista de aplicativos que estão pré-integrados ao Microsoft Azure Active Directory e seus recursos?**

**R:** O Azure AD tem mais de 2.600 aplicativos pré-integrados da Microsoft, provedores de serviços de aplicativos e parceiros. Todos os aplicativos pré-integrados dão suporte ao SSO (logon único). O SSO permite que você use suas credenciais organizacionais para acessar os aplicativos. Alguns dos aplicativos também dão suporte ao provisionamento e ao desprovisionamento automatizados.

Para obter uma lista completa dos aplicativos pré-integrados, confira o [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**P: E se o aplicativo de que preciso não estiver no marketplace do Microsoft Azure Active Directory?**

**R:** Com o Azure AD Premium, você pode adicionar e configurar qualquer aplicativo que desejar. Dependendo dos recursos do aplicativo e de suas preferências, você pode configurar o SSO e o provisionamento automatizado.  

Para obter mais informações, consulte:

* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../manage-apps/configure-saml-single-sign-on.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**P: Como os usuários entram em aplicativos usando o Microsoft Azure Active Directory?**

**R:** O Microsoft Azure Active Directory fornece várias maneiras para que os usuários exibam e acessem os aplicativos, como:

* O painel de acesso do Azure AD
* O iniciador do aplicativo Microsoft 365
* Logon direto para aplicativos federados
* Links profundos a aplicativos federados, baseado em senha, ou existentes

Para obter mais informações, consulte [Experiências do usuário final para aplicativos](../manage-apps/end-user-experiences.md).

---
**P: Quais são as diferentes maneiras pelas quais o Microsoft Azure Active Directory habilita a autenticação e o logon único para aplicativos?**

**R:** O Microsoft Azure Active Directory dá suporte a vários protocolos padronizados para autenticação e autorização, como SAML 2.0, OpenID Connect, OAuth 2.0 e Especificação Web Services Federation. O Azure AD também dá suporte a cofres de senhas e recursos de entrada automatizada para aplicativos que dão suporte apenas à autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](../develop/authentication-vs-authorization.md)
* [Protocolos de autenticação do Active Directory](/previous-versions/azure/dn151124(v=azure.100))
* [Logon único para aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

---
**P: Posso adicionar aplicativos que estou executando localmente?**

**R:** O Proxy de Aplicativo do Azure Active Directory oferece acesso fácil e seguro aos aplicativos Web locais que você escolhe. Você pode acessar esses aplicativos da mesma maneira como acessa os aplicativos SaaS (software como um serviço) no Azure AD. Não é necessário ter uma VPN nem alterar a infraestrutura de rede.  

Para saber mais, confira [Como fornecer acesso remoto seguro a aplicativos locais](../manage-apps/application-proxy.md).

---
**P: Como exigir a autenticação multifator para usuários que acessam determinado aplicativo?**

**R:** Com o Acesso Condicional do Microsoft Azure AD, você pode atribuir uma política de acesso exclusiva para cada aplicativo. Em sua política, você pode sempre exigir autenticação multifator ou quando os usuários não estiverem conectados à rede local.  

Para obter mais informações, consulte [protegendo o acesso ao Microsoft 365 e a outros aplicativos conectados ao Azure Active Directory](../conditional-access/overview.md).

---
**P: O que é o provisionamento automatizado de usuários para aplicativos SaaS?**

**R:** Use o Microsoft Azure Active Directory para automatizar a criação, a manutenção e a remoção de identidades de usuário em muitos aplicativos SaaS de nuvem populares.

Para saber mais, confira [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---
**P:  Posso configurar uma conexão LDAP segura com o Microsoft Azure Active Directory?**

**R:**  Não. O Azure AD não dá suporte ao protocolo LDAP ou LDAP Seguro diretamente. No entanto, é possível habilitar a instância do Azure AD Domain Services (Azure AD DS) em seu locatário do Azure AD com grupos de segurança de rede configurados corretamente por meio da Rede do Azure para obter conectividade LDAP. Para obter mais informações, confira [Configurar o LDAP Seguro para um domínio gerenciado do Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-configure-ldaps.md)
