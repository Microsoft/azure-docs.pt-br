---
title: 'Início Rápido: configurar o SSO (logon único) baseado em OIDC para um aplicativo no locatário do Azure AD (Azure Active Directory)'
description: Este início rápido orienta você ao longo do processo de configuração do SSO (logon único) baseado em OIDC para um aplicativo em seu locatário do Azure AD (Azure Active Directory).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 2e0b49a73422a2f71af37e103e9d4fd6a18a1f61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378934"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: configurar o SSO (logon único) baseado em OIDC para um aplicativo no locatário do Azure AD (Azure Active Directory)

Comece a usar logons de usuário simplificados configurando o SSO (logon único) para um aplicativo que você adicionou ao seu locatário do Azure AD (Azure Active Directory). Depois de configurar o SSO, os usuários poderão entrar em um aplicativo usando as respectivas credenciais do Azure AD. O SSO está incluído na edição gratuita do Azure AD.

Para saber mais sobre o SSO baseado em OIDC, confira [Entender o logon único baseado em OIDC](configure-oidc-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o SSO para um aplicativo que você adicionou ao seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Um aplicativo compatível com SSO e que já foi pré-configurado e adicionado à galeria do Azure AD. A maioria dos aplicativos pode usar o Azure AD para SSO. Os aplicativos na galeria do Azure AD foram pré-configurados. Se o aplicativo não estiver listado ou for um aplicativo desenvolvido personalizado, você ainda poderá usá-lo com o Azure AD. Confira os tutoriais e outras documentações no sumário. Este início rápido se concentra em aplicativos que foram pré-configurados para SSO e adicionados à galeria do Azure AD pelos desenvolvedores de aplicativos.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).
- Opcional: conclusão de [Configurar um aplicativo](add-application-portal-configure.md).
- Opcional: A conclusão de [Atribuir usuários a um aplicativo](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste guia de início rápido.

## <a name="enable-single-sign-on-for-an-app"></a>Habilitar logon único para um aplicativo

Quando você adiciona um aplicativo que usa o padrão OIDC para SSO, há um botão de configuração. Ao selecionar o botão, você é direcionado para o site de aplicativos e conclui o processo de inscrição para o aplicativo. O processo de adicionar um aplicativo é abordado no início rápido Adicionar um aplicativo, apresentado anteriormente nesta série. Se você estiver configurando um aplicativo que já tenha sido adicionado, confira o primeiro início rápido. Ele orienta você ao longo da exibição dos aplicativos que já estão no locatário. 

Para configurar o logon único para um aplicativo:

1. no início rápido anterior desta série, você aprendeu a adicionar um aplicativo que usará o locatário do Azure AD para gerenciamento de identidades. Se o desenvolvedor do aplicativo usou o padrão OIDC para implementar o SSO, então você verá o botão de inscrição ao adicionar o aplicativo. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Captura de tela mostra a opção de logon único e o botão de inscrição." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Selecione **Inscrever-se** e você será levado para a página de logon dos desenvolvedores de aplicativos. Entrada usando as credenciais de entrada do Azure Active Directory. 

   > [!IMPORTANT]
    > Se você já tiver uma assinatura para o aplicativo, a validação dos detalhes do usuário e das informações de locatário/diretório ocorrerão. Se o aplicativo não puder verificar o usuário, ele redirecionará você para se inscrever no serviço de aplicativo ou para a página de erro.

3. Após a autenticação bem-sucedida, uma caixa de diálogo será exibida solicitando o consentimento do administrador. Selecione **Consentir em nome da organização** e selecione **Aceitar**. Para saber mais sobre o consentimento do usuário e do administrador, confira [Entender o consentimento do usuário e do administrador](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Captura de tela mostra a tela de consentimento de um aplicativo." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. O aplicativo é adicionado ao locatário e a home page do aplicativo é exibida.


> [!TIP]
> Para automatizar o gerenciamento de aplicativos usando a API do Graph, confira [Automatizar o gerenciamento de aplicativos com a API do Microsoft Graph](/graph/application-saml-sso-configure-api).

Assista a um vídeo que aborda detalhes adicionais sobre como adicionar um aplicativo baseado em OIDC ao Azure AD.

Adicionar um aplicativo baseado em OIDC no Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando concluir esta série de guias de início rápido, considere a possibilidade de excluir o aplicativo para limpar seu locatário de teste. A exclusão do aplicativo é abordada no último guia de início rápido desta série; confira [Excluir um aplicativo](delete-application-portal.md).

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como excluir um aplicativo.
> [!div class="nextstepaction"]
> [Excluir um aplicativo](delete-application-portal.md)
