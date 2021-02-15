---
title: Introdução à integração do Azure AD com aplicativos
description: Este artigo é um guia de introdução para a integração do AD do Azure (Active Directory do Azure) com aplicativos locais e aplicativos em nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 8b321acb00e6e9b4b6cca117afba8bf0c9432719
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258459"
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
  * Todas as anteriores
* Quais permissões e atribuições de função os usuários individuais têm atualmente? Você precisa examinar seu acesso ou tem certeza de que o acesso do usuário e as atribuições de função são apropriadas agora?
* Os grupos já estão estabelecidos em seu Active Directory local?
  * Como os grupos são organizados?
  * Quem são os membros do grupo?
  * Quais permissões/atribuições de função os grupos têm atualmente?
* Você precisará limpar os bancos de dados de usuários/grupos antes da integração?  (Essa é uma pergunta muito importante. Entrada e saída de lixo.)

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

### <a name="authentication-types"></a>Tipos de autenticação
Cada um dos seus aplicativos pode ter requisitos de autenticação diferentes. Com o AD do Azure, pode-se usar certificados de autenticação com aplicativos que usam os Protocolos SAML 2.0, WS-Federation ou OpenID Connect, bem como Logon Único com Senha. Para saber mais sobre os tipos de autenticação de aplicativo para uso com o Azure AD, veja [Gerenciando certificados para Logon Único Federado no Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) e [Logon único baseado em senha](what-is-single-sign-on.md).

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

Forneça comentários sobre planos de implantação executando a [Pesquisa de plano de implantação](https://aka.ms/DeploymentPlanFeedback).