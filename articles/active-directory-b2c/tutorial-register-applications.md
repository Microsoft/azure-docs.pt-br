---
title: 'Tutorial: Registrar um aplicativo'
titleSuffix: Azure AD B2C
description: Siga este tutorial para saber como registrar seus aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ec67669edeb52af1044c97c984eb6ba36fd1a0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579630"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Tutorial: Registrar um aplicativo Web no Azure Active Directory B2C

Antes que os [aplicativos](application-types.md) possam interagir com o Azure Active Directory B2C (Azure AD B2C), eles deverão ser registrados em um locatário gerenciado por você. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure. 

Um "aplicativo Web" refere-se a um aplicativo Web tradicional que executa a maior parte da lógica do aplicativo no servidor. Eles podem ser criados usando estruturas como ASP.NET Core, Maven (Java), Flask (Python) e Express (Node.js).

> [!IMPORTANT]
> Se você estiver usando um "SPA" (aplicativo de página única), por exemplo, usando o Angular, Vue ou React, saiba [como registrar um aplicativo de página única](tutorial-register-spa.md).
> 
> Se você estiver usando um aplicativo nativo (por exemplo, iOS, Android, móvel e de área de trabalho), saiba [como registrar um aplicativo cliente nativo](add-native-application.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

Para registrar um aplicativo Web no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativo** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *webapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos dos usuários)** .
1. Em **URI de Redirecionamento**, selecione **Web** e, em seguida, insira `https://jwt.ms` na caixa de texto da URL.

    O URI de redirecionamento é o ponto de extremidade para o qual o usuário é enviado pelo servidor de autorização (Azure AD B2C, nesse caso) depois de concluir sua interação com o usuário e para o qual um token de acesso ou código de autorização é enviado após uma autorização bem-sucedida. Em um aplicativo de produção, normalmente é um ponto de extremidade de acesso público no qual o aplicativo está em execução, como `https://contoso.com/auth-response`. Para fins de teste, como este tutorial, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca deixa o navegador). Durante o desenvolvimento do aplicativo, é possível adicionar o ponto de extremidade onde o aplicativo escuta localmente, como `https://localhost:5000`. Adicione e modifique URIs de redirecionamento nos aplicativos registrados a qualquer momento.

    As restrições a seguir se aplicam a URLs de redirecionamento:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc` como parte de seu caminho, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.

1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
1. Para **incluir o aplicativo web/API web**, selecione **Sim**.
1. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente em `http://localhost:5000`. Adicione e modifique URIs de redirecionamento nos aplicativos registrados a qualquer momento.

    As restrições a seguir se aplicam a URLs de redirecionamento:

    * A URL de resposta precisa começar com o esquema `https`, a menos que esteja usando `localhost`.
    * A URL de resposta diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc` como parte de seu caminho, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.

1. Selecione **Criar** para concluir o registro do aplicativo.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Para um aplicativo Web, é necessário criar um segredo de aplicativo. O segredo do cliente também é conhecido como *senha do aplicativo*. O segredo será usado pelo seu aplicativo para trocar um código de autorização para um token de acesso.

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Na página **Azure AD B2C – Registros de aplicativo**, selecione o aplicativo criado, por exemplo *webapp1*.
1. No menu à esquerda, em **Gerenciar**, selecione **Certificados e segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição**. Por exemplo, *clientsecret1*.
1. Em **Expirar**, selecione um período durante o qual o segredo será válido e clique em **Adicionar**.
1. Registre o **Valor** do segredo para uso no código do aplicativo cliente. Esse valor secreto nunca será exibido novamente depois que você sair dessa página. Use este valor como o segredo do aplicativo no código do aplicativo.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Na página **Azure AD B2C - Aplicativos**, selecione o aplicativo criado, por exemplo *webapp1*.
1. Selecione **Chaves** e, em seguida, selecione **Gerar chave**.
1. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Use este valor como o segredo do aplicativo no código do aplicativo.

* * *

> [!NOTE]
> Para fins de segurança, você pode substituir o segredo do aplicativo periodicamente ou, em caso de emergência, imediatamente. Um aplicativo que se integra ao Azure AD B2C deve estar preparado para lidar com um evento de substituição de segredo, independentemente da frequência em que pode ocorrer. Você pode definir dois segredos do aplicativo, permitindo que seu aplicativo continue usando o segredo antigo durante um evento de rotação de segredo do aplicativo. Para adicionar outro segredo do cliente, repita as etapas nesta seção. 

## <a name="enable-id-token-implicit-grant"></a>Habilitar concessão implícita de token de ID

A definição da característica da concessão implícita é que os tokens, como a ID e os tokens de acesso, são retornados diretamente do Azure AD B2C para o aplicativo. Para aplicativos Web, como aqueles dos tipos ASP.NET Core e [https://jwt.ms](https://jwt.ms), que solicitam um token de ID diretamente do ponto de extremidade da autorização, habilite o fluxo de concessão implícita no registro do aplicativo.

1. No menu à esquerda, em **Gerenciar**, selecione **Autenticação**.
1. Em Concessão implícita, selecione as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

A seguir você aprenderá como criar fluxos de usuário para permitir que os usuários se inscrevam, acessem e gerenciem perfis.

> [!div class="nextstepaction"]
> [Criação de fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
