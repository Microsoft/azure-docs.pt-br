---
title: Configurar o consentimento do usuário para um aplicativo - Azure Active Directory | Microsoft Docs
description: Aprenda a gerenciar a maneira como os usuários consentem com as permissões de aplicativos. Você pode simplificar a experiência do usuário concedendo o consentimento do administrador. Esses métodos se aplicam a todos os usuários finais do locatário do Azure AD (Azure Active Directory).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4951984d05e75b0271cf6592c77c54ad13678994
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476548"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Configurar o modo como os usuários finais consentem em um aplicativo no Active Directory do Azure
Aprenda a configurar a maneira como os usuários consentem com as permissões do aplicativo. Você pode simplificar a experiência do usuário concedendo o consentimento do administrador. Este artigo fornece as diferentes maneiras de configurar o consentimento do usuário. Os métodos se aplicam a todos os usuários finais do locatário do Azure AD (Azure Active Directory). 

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

A concessão do consentimento do administrador exige que você faça login como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.

Para restringir o acesso aos aplicativos, você precisará exigir a atribuição do usuário e, em seguida, atribuir usuários ou grupos ao aplicativo.  Para obter mais informações, confira [Métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Conceder autorização do administrador para aplicativos corporativos no portal do Azure

Para conceder o consentimento do administrador a um aplicativo corporativo:

1. Faça login no [Portal do Azure](https://portal.azure.com) como administrador global, administrador do aplicativo ou administrador do aplicativo em nuvem.
2. Clique em **Todos os serviços** na parte superior do menu de navegação à esquerda. A **Extensão do Active Directory do Azure** é aberta.
3. Na caixa de pesquisa de filtros, digite **"Azure Active Directory"** e selecione o item **Active Directory do Azure**.
4. No menu de navegação, clique em **Aplicativos corporativos**.
5. Selecione o aplicativo de consentimento.
6. Selecione **permissões** e, em seguida, clique em **conceder consentimento do administrador**. Você será solicitado a entrar para administrar o aplicativo.
7. Faça login com uma conta que tenha permissões para conceder o consentimento do administrador para o aplicativo. 
8. Consentimento para as permissões do aplicativo.

Esta opção só funciona se o aplicativo for: 

- Registrado em seu inquilino ou
- Registrado em outro locatário do Azure AD e autorizado por pelo menos um usuário final. Depois que um usuário final consentir em um aplicativo, o Azure AD lista o aplicativo em **Aplicativos corporativos** no portal do Azure.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Conceder o consentimento do administrador ao registrar um aplicativo no portal do Azure

Para conceder o consentimento do administrador ao registrar um aplicativo: 

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Navegue para o blade **Registros de aplicativo**.
3. Selecione o aplicativo para o consentimento.
4. Selecione **Permissões de API**.
5. Clique em **conceder consentimento do administrador**.


## <a name="grant-admin-consent-through-a-url-request"></a>Conceder consentimento do administrador por meio de uma solicitação de URL

Para conceder o consentimento do administrador por meio de uma solicitação de URL:

1. Construa uma solicitação para *login.microsoftonline.com* com as configurações do aplicativo e acrescente `&prompt=admin_consent`. 
2. Após entrar com credenciais de administrador, o aplicativo recebe consentimento para todos os usuários.


## <a name="force-user-consent-through-a-url-request"></a>Forçar o consentimento do usuário por meio de uma solicitação de URL

Para exigir que os usuários finais consintam com um aplicativo sempre que autenticarem, anexe `&prompt=consent` ao URL de solicitação de autenticação.

## <a name="next-steps"></a>Próximas etapas

[Consentir e integrar aplicativos ao AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Consentimento e permissão para aplicativos convergidos do AzureAD v2.0](../develop/active-directory-v2-scopes.md)

[StackOverflow do AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
