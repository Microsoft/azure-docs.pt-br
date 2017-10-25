---
title: "Azure Active Directory B2C: políticas internas | Microsoft Docs"
description: "Um tópico sobre a estrutura de política extensível do Active Directory B2C do Azure e como criar vários tipos de política"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: daad3af089afdf76b930053728bb11a5cf4c2a92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: políticas internas


A estrutura de política extensível do Azure AD (Azure Active Directory) B2C é o principal ponto forte do serviço. As políticas descrevem totalmente as experiências de identidade do consumidor, como inscrição, entrada ou edição de perfil. Por exemplo, uma política de inscrição permite controlar comportamentos definindo as seguintes configurações:

* Tipos de conta (contas sociais, como o Facebook ou contas locais, como um endereço de email) que os consumidores podem usar para se inscrever no aplicativo
* Atributos (como nome, código postal e tamanho do calçado, por exemplo) que serão coletados junto ao consumidor durante a inscrição
* Uso da Autenticação Multifator do Microsoft Azure
* A aparência e funcionalidade de todas as páginas de inscrição
* Informações (que se manifestam como declarações em um token) que o aplicativo recebe quando a execução da política é concluída

Você pode criar várias políticas de tipos diferentes em seu locatário e usá-las em seus aplicativos, conforme necessário. As políticas podem ser reutilizadas entre os aplicativos. Essa flexibilidade permite aos desenvolvedores definir e modificar experiências de identidade do consumidor com pouca ou nenhuma alteração no código.

As políticas estão disponíveis para uso por meio de uma interface simples do desenvolvedor. O aplicativo dispara uma política usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de política na solicitação) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre solicitações que invocam uma política de inscrição e aquelas que invocam uma política de entrada é o nome da política usado no parâmetro de cadeia de caracteres de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obter mais informações sobre a estrutura das políticas, consulte [Esta postagem de blog sobre o Azure AD B2C no Blog do Enterprise Mobility and Security](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou credenciais

Esta política controla as duas experiências de inscrição e credenciais do consumidor com uma única configuração. Os consumidores são conduzidos para o caminho certo (inscrição ou credenciais), dependendo do contexto. Ele também descreve o conteúdo de tokens que o aplicativo receberá mediante inscrições ou entradas bem-sucedidas.  Há um exemplo de código para a política de inscrição ou de entrada [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  É recomendável que você use esta política em vez de uma política de inscrição e entrada.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Usar uma política de entrada

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Como fazer para vincular uma política de inscrição ou entrada a uma política de redefinição de senha?
Quando cria uma política de inscrição ou entrada (com contas locais), você vê um link **Esqueceu a senha?** na primeira página da experiência. Clicar nesse link não dispara automaticamente uma política de redefinição de senha. 

Em vez disso, o código de erro **`AADB2C90118`** é retornado para seu aplicativo. Seu aplicativo precisa lidar com esse código de erro invocando uma política de redefinição de senha específica. Para obter mais informações, consulte um [exemplo que demonstra a abordagem de vinculação de políticas](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Eu devo usar uma política de inscrição ou entrada ou uma política de inscrição e uma política de entrada?
Recomendamos que você use uma política de inscrição ou entrada em vez de usar uma política de inscrição e uma política de entrada.  

A política de inscrição ou entrada tem mais recursos do que a política de entrada. Ela também permite que você use a personalização da interface do usuário da página e tem melhor suporte para localização. 

A política de entrada é recomendada se você não precisar localizar suas políticas, precisar apenas de recursos de personalização secundários para a identidade visual e quiser a redefinição de senha embutida.

## <a name="next-steps"></a>Próximas etapas
* [Configuração de token, de sessão e de logon único](active-directory-b2c-token-session-sso.md)
* [Desabilitar a verificação de email durante a inscrição do consumidor](active-directory-b2c-reference-disable-ev.md)

