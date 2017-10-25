---
title: 'Azure Active Directory B2C: registro de aplicativos | Microsoft Docs'
description: Como registrar seu aplicativo com o B2C do Active Directory do Azure
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.openlocfilehash: b49a894365dd344afb2f3cab51e7cfd28d930b1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrar seu aplicativo

Este Guia de início rápido ajuda você a registrar um aplicativo em um locatário do Microsoft Azure Active Directory (Azure AD) B2C em poucos minutos. Quando terminar, seu aplicativo estará registrado para uso no locatário B2C do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para compilar um aplicativo que aceite a inscrição e a entrada do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Azure Active Directory B2C. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md).

Os aplicativos criados da folha Azure AD B2C no portal do Azure devem ser gerenciados do mesmo local. Se você editar os aplicativos B2C usando o PowerShell ou outro portal, eles deixarão de ter suporte e não funcionarão com o Azure AD B2C. Consulte os detalhes da seção [aplicativos com falha](#faulted-apps). 

Este artigo usa exemplos que ajudarão você a começar a usar nossos exemplos. Você pode aprender mais sobre esses exemplos nos artigos subsequentes.

## <a name="navigate-to-b2c-settings"></a>Navegue até as configurações de B2C

Entre no [Portal do Azure](https://portal.azure.com/) como Administrador Global do locatário B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Escolha as próximas etapas com base em seu tipo de aplicativo

* [Registrar um aplicativo Web](#register-a-web-app)
* [Registrar uma API Web](#register-a-web-api)
* [Registrar um aplicativo móvel ou nativo](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrar um aplicativo Web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Criar um segredo de cliente do aplicativo Web

Se o aplicativo Web chamar uma API da Web protegida pelo Azure AD B2C, execute estas etapas:
   1. Crie um segredo do aplicativo acessando a folha **Chaves** e clicando no botão **Gerar Chave**. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.
   2. Clique em **Acesso à API**, clique em **Adicionar** e selecione sua API da Web e escopos (permissões).

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
> 

[Vá até **próximas etapas**](#next-steps)

### <a name="register-a-web-api"></a>Registrar uma API Web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Clique em **Escopos publicados** para adicionar mais escopos, conforme o necessário. Por padrão, o escopo "user_impersonation" está definido. O escopo user_impersonation dá a outros aplicativos a capacidade de acessar essa API em nome do usuário conectado. Se você quiser, o escopo user_impersonation pode ser removido.

[Vá até **próximas etapas**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registrar um aplicativo móvel ou nativo

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Vá até **próximas etapas**](#next-steps)

## <a name="limitations"></a>Limitações

### <a name="choosing-a-web-app-or-api-reply-url"></a>Escolher aplicativo Web ou URL de resposta da API

No momento, os aplicativos registrados no Azure AD B2C estão restritos a um conjunto limitado de valores para URLs de resposta. O URL de resposta para serviços e aplicativos Web deve começar com o esquema `https`, e todos os valores de URL de resposta devem compartilhar um único domínio DNS. Por exemplo, você não pode registrar um aplicativo Web que tenha uma destas URLs de resposta:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo da URL de resposta existente ao nome DNS da URL de resposta que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:

* Se o nome DNS completo da URL de resposta nova não coincidir com o nome DNS da URL de resposta existente.
* Se o nome DNS completo da URL de resposta nova não for um subdomínio da URL de resposta existente.

Por exemplo, se o aplicativo tiver esta URL de resposta:

`https://login.contoso.com`

Você pode adicionar a ele, desta forma:

`https://login.contoso.com/new`

Nesse caso, os nome DNS corresponde exatamente. Ou você pode fazer isto:

`https://new.login.contoso.com`

Nesse caso, você está se referindo a um subdomínio DNS logon.contoso.com. Se você quiser ter um aplicativo com login-east.contoso.com e login-west.contoso.com como URLs de resposta, deverá adicionar as seguintes URLs de resposta nesta ordem:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Os dois últimos podem ser adicionados porque eles são subdomínios da primeira URL de resposta, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Escolher um URI de redirecionamento do aplicativo nativo

Há duas considerações importantes ao escolher um URI de redirecionamento para aplicativos móveis/nativo:

* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. Em nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), podemos usar com.onmicrosoft.contoso.appname como o esquema. É recomendável seguir esse padrão. Se dois aplicativos compartilharem o mesmo esquema, o usuário verá uma caixa de diálogo "escolher aplicativo". Se o usuário fizer uma escolha incorreta, o logon falhará.
* **Completo**: o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra “/” depois do domínio (por exemplo, //contoso/ funciona e //contoso falha).

Verifique se não há caracteres especiais, como sublinhados, no uri de redirecionamento.

### <a name="faulted-apps"></a>Aplicativos com falha

Os aplicativos B2C NÃO devem ser editados:

* Em outros portais de gerenciamento de aplicativo, como o [portal clássico do Azure](https://manage.windowsazure.com/) e o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/).
* Uso da API do Graph ou do PowerShell

Se você editar o aplicativo B2C conforme descrito acima e tentar editá-lo novamente na folha de recursos do Azure AD B2C no Portal do Azure, ele se tornará um aplicativo com falha e o aplicativo não poderá ser usado com o Azure AD B2C. Você precisa excluir o aplicativo e criá-lo novamente.

Para excluir o aplicativo, vá para o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/) e exclua o aplicativo localmente. Para que o aplicativo fique visível, você precisa ser o proprietário do aplicativo (e não apenas um administrador do locatário).

## <a name="next-steps"></a>Próximas etapas

Agora que já tem um aplicativo registrado com o Azure AD B2C, você pode concluir um de nossos [tutoriais de início rápido](active-directory-b2c-overview.md#get-started) para começar a trabalhar.

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET com inscrição, entrada e redefinição de senha](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
