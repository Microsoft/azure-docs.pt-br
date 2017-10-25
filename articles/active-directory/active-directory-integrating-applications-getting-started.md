---
title: "Introdução à integração do Azure AD aos aplicativos | Microsoft Docs"
description: "Este artigo é um guia de introdução para a integração do AD do Azure (Active Directory do Azure) com aplicativos locais e aplicativos em nuvem."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: db6d210d-c970-49e9-bd20-36d984bcd1c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: e273d27bacf6978c5056c0ab09846c26426dd12b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guia de introdução: integrando o Active Directory do Azure com aplicativos
## <a name="overview"></a>Visão geral
Este tópico destina-se a fornecer um roteiro para integrar aplicativos com o Azure AD (Azure Active Directory). Cada uma das seções abaixo contém um breve resumo de um tópico mais detalhado, para que você possa identificar quais partes deste guia de introdução são relevantes para você.  Siga os links para se aprofundar em cada assunto.

## <a name="before-you-begin-take-inventory"></a>Antes de começar, faça o inventário
Antes de ir para a integração de aplicativos com o AD do Azure, é importante saber onde você está e onde deseja ir.  As perguntas a seguir destinam-se a ajudá-lo a pensar em seu projeto de integração de aplicativos do AD do Azure.

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
  * AD do Azure
  * Em um banco de dados de aplicativo separado que você possui
  * Em aplicativos não autorizados
  * Todos os itens acima
* Quais permissões e atribuições de função os usuários individuais têm atualmente? Você precisa examinar seu acesso ou tem certeza de que o acesso do usuário e as atribuições de função são apropriadas agora?
* Os grupos já estão estabelecidos em seu Active Directory local?
  * Como os grupos são organizados?
  * Quem são os membros do grupo?
  * Quais permissões/atribuições de função os grupos têm atualmente?
* Você precisará limpar os bancos de dados de usuários/grupos antes da integração?  (Essa é uma pergunta muito importante. Entrada e saída de lixo.)

### <a name="access-management-inventory"></a>Inventário de gerenciamento de acesso
* Atualmente, como você gerencia o acesso do usuário aos aplicativos? Isso precisa ser alterado?  Você considerou outras maneiras de gerenciar o acesso, como com o [RBAC](role-based-access-control-configure.md) , por exemplo?
* Quem precisa de acesso ao quê?

Talvez você não tenha as respostas a todas essas perguntas com antecedência, mas tudo bem.  Este guia pode ajudá-lo a responder a algumas dessas perguntas e tomar algumas decisões informadas.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure e um diretório do Azure Active Directory.  Se você ainda não tem uma assinatura do Azure, você pode experimentar o Azure gratuitamente por 30 dias. [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integração de aplicativos com o AD do Azure
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Encontrando aplicativos em nuvem não autorizados com o Cloud App Discovery
Como mencionado acima, pode haver aplicativos que ainda não foram gerenciados pela sua organização até agora.  Como parte do processo de inventário, é possível encontrar aplicativos em nuvem não autorizados. Veja [Encontrando aplicativos em nuvem não autorizados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Tipos de autenticação
Cada um dos seus aplicativos pode ter requisitos de autenticação diferentes. Com o AD do Azure, pode-se usar certificados de autenticação com aplicativos que usam os Protocolos SAML 2.0, WS-Federation ou OpenID Connect, bem como Logon Único com Senha. Para saber mais sobre os tipos de autenticação de aplicativo para uso com o Azure AD, veja [Gerenciando certificados para Logon Único Federado no Azure Active Directory](active-directory-sso-certs.md) e [Logon único baseado em senha](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitando o SSO com o Proxy de Aplicativo do AD do Azure
Com o Proxy de Aplicativo do AD do Microsoft Azure, você pode fornecer acesso a aplicativos localizados em sua rede privada com segurança, de qualquer lugar e em qualquer dispositivo. Depois de instalar um conector de proxy de aplicativo em seu ambiente, ele pode ser facilmente configurado com o Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrando aplicativos com o AD do Azure
Os artigos a seguir abordam as diferentes maneiras pelas quais os aplicativos são integrados com o AD do Azure, além de fornecer uma orientação.

* [Determinando qual Active Directory será usado](active-directory-administer.md)
* [Usando aplicativos na galeria de aplicativos do Azure](active-directory-appssoaccess-whatis.md)
* [Integrando a lista de tutoriais de aplicativos SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gerenciando o acesso a aplicativos
Os artigos a seguir descrevem as maneiras pelas quais você pode gerenciar o acesso aos aplicativos depois de serem integrados ao AD do Azure usando os Conectores do AD do Azure e o AD do Azure.

* [Gerenciando o acesso a aplicativos com o AD do Azure](active-directory-managing-access-to-apps.md)
* [Automatizando com os Conectores do AD do Azure](active-directory-saas-app-provisioning.md)
* [Atribuindo usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md)
* [Atribuindo grupos a um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md)
* [Compartilhando contas](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrando aplicativos personalizados
Se você estiver escrevendo um novo aplicativo e desejar ajudar os desenvolvedores a aproveitar o potencial do AD do Azure, veja [Orientando desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md).

Se desejar adicionar seu aplicativo personalizado à Galeria de Aplicativos do Azure, veja [“Traga seu próprio aplicativo” com a configuração do SAML por autoatendimento do AD do Azure](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Consulte também
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

