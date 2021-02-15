---
title: Exibindo aplicativos usando seu locatário Azure Active Directory para gerenciamento de identidades
description: Entenda como exibir todos os aplicativos usando seu locatário de Azure Active Directory para o gerenciamento de identidades.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 443e8c83ec29f9f0478e3881b9e6d6a8eb0bf403
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259779"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Exibindo aplicativos usando seu locatário do Azure AD para o gerenciamento de identidades
A [série de guias de início rápido sobre o gerenciamento de aplicativos](view-applications-portal.md) orienta os conceitos básicos. Nele, você aprende a exibir todos os aplicativos usando seu locatário do Azure AD para o gerenciamento de identidades. Este artigo mergulha um pouco mais sobre os tipos de aplicativos que você encontrará.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que um aplicativo específico aparece em minha lista de todos os aplicativos?
Quando filtrada para **Todos os Aplicativos**, a **Lista** **Todos os Aplicativos** mostra todos os objetos de Entidade de Serviço em seu locatário. Objetos de Entidade de Serviço podem aparecer nessa lista de uma várias maneiras:
- Quando você adiciona qualquer aplicativo da galeria de aplicativos, incluindo:
   - **Azure ad-aplicativos corporativos** – aplicativos adicionados ao seu locatário usando a opção **aplicativos empresariais** no portal do Azure AD. Normalmente, aplicativos integrados usando o padrão SAML.
   - **Azure ad-registros de aplicativo** – aplicativos adicionados ao seu locatário usando a opção **registros de aplicativo** no portal do Azure AD. Normalmente, aplicativos desenvolvidos personalizados usando os padrões do Open ID Connect e do OAuth.
   - **Aplicativos do Proxy de Aplicativo** – um aplicativo em execução em seu ambiente local para o qual você deseja fornecer logon único seguro externamente
- Ao se inscrever ou entrar no, um aplicativo de terceiros integrado ao Azure Active Directory. Um exemplo é o [Smartsheet](https://app.smartsheet.com/b/home) ou o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplicativos da Microsoft, como o Microsoft 365.
- Quando você adiciona um novo registro de aplicativo criando um aplicativo personalizado usando o [Registro de Aplicativo](../develop/quickstart-register-app.md)
- Quando você adiciona um novo registro de aplicativo criando um aplicativo personalizado usando o [Portal de Registro de Aplicativos V2.0](../develop/quickstart-register-app.md)
- Ao adicionar um aplicativo, você está desenvolvendo usando os [métodos de autenticação ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) ou os [Serviços conectados](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) do Visual Studio
- Quando você cria um objeto de entidade de serviço usando o [Módulo do PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2)
- Quando você [dá consentimento a um aplicativo](../develop/howto-convert-app-to-be-multi-tenant.md) como administrador para usar dados em seu locatário
- Quando um [usuário dá consentimento para um aplicativo](../develop/howto-convert-app-to-be-multi-tenant.md) usar dados no seu locatário
- Quando você habilita determinados serviços que armazenam dados em seu locatário. Um exemplo é a Redefinição de Senha, que é modelada como uma entidade de serviço para armazenar sua senha de redefinição de senha com segurança.

Saiba mais sobre como e por que os aplicativos são adicionados ao diretório, consulte [como os aplicativos são adicionados ao Azure ad](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Próximas etapas
[Gerenciamento de aplicativos com o Active Directory do Azure](what-is-application-management.md)
