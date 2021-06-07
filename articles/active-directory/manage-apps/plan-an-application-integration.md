---
title: Introdução à integração de Azure Active Directory com aplicativos
description: Este artigo é um guia de introdução para a integração do AD do Azure (Active Directory do Azure) com aplicativos locais e aplicativos em nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: de06bb4f97568eaa40b0b09e9bc2b50608424aa8
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775588"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guia de introdução: integrando o Active Directory do Azure com aplicativos

Este tópico resume o processo de integração de aplicativos com o Azure AD (Azure Active Directory). Cada uma das seções abaixo contém um breve resumo de um tópico mais detalhado, para que você possa identificar quais partes deste guia de introdução são relevantes para você.

Para baixar os planos de implantação detalhados, veja as [Próximas etapas](#next-steps).

## <a name="take-inventory"></a>Obter um inventário
Antes de ir para a integração de aplicativos com o Azure AD, é importante saber onde você está e para onde deseja ir.  As perguntas a seguir destinam-se a ajudá-lo a pensar em seu projeto de integração de aplicativos do AD do Azure.

### <a name="application-inventory"></a>Inventário de aplicativos
* Onde estão todos os seus aplicativos? Quem é seu proprietário?
* Que tipo de autenticação os aplicativos exigem?
* Quem precisa de acesso a quais aplicativos?
* Você deseja implantar um novo aplicativo?
  * Ele será criado internamente e implantado em uma instância de computação do Azure?
  * Você usará uma que está disponível na Galeria de Aplicativos do Azure?

### <a name="user-and-group-inventory"></a>Inventário de usuários e grupos
* Onde residem suas contas de usuário?
  * Active Directory local
  * Azure AD
  * Em um banco de dados de aplicativo separado que você possui
  * Em aplicativos não autorizados
  * Todas as opções acima
* Quais permissões e atribuições de função os usuários individuais têm atualmente? Você precisa examinar seu acesso ou tem certeza de que o acesso do usuário e as atribuições de função são apropriadas agora?
* Os grupos já estão estabelecidos em seu Active Directory local?
  * Como os grupos são organizados?
  * Quem são os membros do grupo?
  * Quais permissões/atribuições de função os grupos têm atualmente?
* Você precisará limpar os bancos de dados de usuários/grupos antes da integração?  (Essa é uma pergunta importante. Entrada e saída de lixo.)

### <a name="access-management-inventory"></a>Inventário de gerenciamento de acesso
* Atualmente, como você gerencia o acesso do usuário aos aplicativos? Isso precisa ser alterado?  Você considerou outras maneiras de gerenciar o acesso, como com o [RBAC do Azure](../../role-based-access-control/role-assignments-portal.md) , por exemplo?
* Quem precisa de acesso ao quê?

Talvez você não tenha as respostas a todas essas perguntas com antecedência, mas tudo bem.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e tomar algumas decisões informadas.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Encontrar aplicativos em nuvem não sancionados com o Cloud Discovery

Como mencionado acima, pode haver aplicativos que ainda não foram gerenciados pela sua organização até agora.  Como parte do processo de inventário, é possível encontrar aplicativos em nuvem não autorizados. Consulte [Configurar Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrando aplicativos com o AD do Azure
Os artigos a seguir abordam as diferentes maneiras pelas quais os aplicativos são integrados com o AD do Azure, além de fornecer uma orientação.

* [Determinando qual Active Directory será usado](../fundamentals/active-directory-whatis.md)
* [Usando aplicativos na galeria de aplicativos do Azure](what-is-single-sign-on.md)
* [Integrando a lista de tutoriais de aplicativos SaaS](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Recursos para aplicativos não listados na galeria do Azure AD

É possível adicionar qualquer aplicativo que já exista em sua organização ou qualquer aplicativo de terceiros, de um fornecedor que ainda não faça parte da galeria do Azure AD. Dependendo do seu [contrato de licença](https://azure.microsoft.com/pricing/details/active-directory/), os seguintes recursos estão disponíveis:

- Integração de autoatendimento de qualquer aplicativo compatível com provedores de identidade [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) (iniciado por SP ou IdP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](sso-options.md#password-based-sso)
- Conexão de autoatendimento para aplicativos que usam o [protocolo SCIM (Sistema de Gerenciamento de Usuários entre Domínios) para provisionamento de usuário](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicativo no [iniciador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou [meus aplicativos](sso-options.md#linked-sign-on)

Se você estiver procurando orientação para desenvolvedores sobre como integrar aplicativos personalizados ao Azure AD, consulte [cenários de autenticação do Azure ad](../develop/authentication-vs-authorization.md). Ao desenvolver um aplicativo que usa um protocolo moderno como [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar usuários, você pode registrá-lo na plataforma de identidade da Microsoft usando a experiência de [Registros de aplicativo](../develop/quickstart-register-app.md) no portal do Azure.

### <a name="authentication-types"></a>Tipos de autenticação
Cada um dos seus aplicativos pode ter requisitos de autenticação diferentes. Com o Azure AD, certificados de autenticação podem ser usados com aplicativos que usam protocolos SAML 2,0, WS-Federation ou OpenID Connect e logon único com senha. Para obter mais informações sobre tipos de autenticação de aplicativo, consulte [gerenciando certificados para Sign-On único federado em Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) e [senha com base em logon único](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitando o SSO com o Proxy de Aplicativo do AD do Azure
Com o Proxy de Aplicativo do AD do Microsoft Azure, você pode fornecer acesso a aplicativos localizados em sua rede privada com segurança, de qualquer lugar e em qualquer dispositivo. Depois de instalar um conector de proxy de aplicativo em seu ambiente, ele pode ser facilmente configurado com o Azure AD.

### <a name="integrating-custom-applications"></a>Integrando aplicativos personalizados
Se você quiser adicionar seu aplicativo personalizado à galeria de Aplicativo Azure, consulte [publicar seu aplicativo na Galeria de aplicativos do Azure ad](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Gerenciando o acesso a aplicativos
Os artigos a seguir descrevem as maneiras pelas quais você pode gerenciar o acesso aos aplicativos depois de serem integrados ao AD do Azure usando os Conectores do AD do Azure e o AD do Azure.

* [Gerenciando o acesso a aplicativos com o AD do Azure](what-is-access-management.md)
* [Automatizando com os Conectores do AD do Azure](../app-provisioning/user-provisioning.md)
* [Atribuindo usuários a um aplicativo](./assign-user-or-group-access-portal.md)
* [Atribuindo grupos a um aplicativo](./assign-user-or-group-access-portal.md)
* [Compartilhando contas](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Próximas etapas
Para obter informações detalhadas, você pode baixar os planos de implantação do Azure Active Directory do [GitHub](../fundamentals/active-directory-deployment-plans.md). Para aplicativos da galeria, você pode baixar planos de implantação para logon único, acesso condicional e provisionamento de usuário por meio do [portal do Azure](https://portal.azure.com).

Para baixar um plano de implantação do portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **aplicativos empresariais**  |  **escolher um plano de implantação de aplicativo**  |  .
