---
title: 'Início Rápido: Excluir um aplicativo do seu locatário do Azure AD (Azure Active Directory)'
description: Este início rápido usa o portal do Azure para excluir um aplicativo de seu locatário do Azure AD (Azure Active Directory).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: iangithinji
ms.openlocfilehash: 55607d520affe704cecd2e16fb64b3a65c04e167
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374294"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Excluir um aplicativo do seu locatário do Azure AD (Azure Active Directory)

Este início rápido usa o portal do Azure para excluir um aplicativo que foi adicionado ao seu locatário do Azure AD (Azure Active Directory).

Saiba mais sobre o SSO e o Azure. Confira o artigo [O que é o SSO (Logon Único)](what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para excluir um aplicativo do seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).
- Opcional: conclusão de [Configurar um aplicativo](add-application-portal-configure.md).
- Opcional: A conclusão de [Atribuir usuários a um aplicativo](add-application-portal-assign-users.md).
- Opcional: conclusão de [Configurar o logon único](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste guia de início rápido.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Excluir um aplicativo do seu locatário do Azure AD

Para excluir um aplicativo do seu locatário do Azure AD:

1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo que você deseja excluir. Nesse caso, excluímos o aplicativo **GitHub_test** que adicionamos no guia de início rápido anterior.
1. Na seção **Gerenciar** do painel esquerdo, selecione **Propriedades**.
1. Selecione **Excluir** e **Sim** para confirmar que deseja excluir o aplicativo do seu locatário do Azure AD.

> [!TIP]
> Para automatizar o gerenciamento de aplicativos usando a API do Graph, confira [Automatizar o gerenciamento de aplicativos com a API do Microsoft Graph](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando concluir esta série de inícios rápidos, considere a possibilidade de excluir o aplicativo para limpar seu locatário de teste. A exclusão do aplicativo foi abordada neste guia de início rápido.

## <a name="next-steps"></a>Próximas etapas

Você concluiu a série de guias de início rápido. Em seguida, saiba mais sobre o SSO (Logon Único). Confira o artigo [O que é o SSO?](what-is-single-sign-on.md) Ou leia informações sobre práticas recomendadas no gerenciamento de aplicativos.
> [!div class="nextstepaction"]
> [Melhores práticas de gerenciamento de aplicativos](application-management-fundamentals.md)
