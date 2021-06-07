---
title: Fluxos dos usuários no Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Saiba mais sobre a estrutura de política extensível do Azure Active Directory B2C e como criar diversos fluxos dos usuários.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94840180"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Fluxos dos usuários no Azure Active Directory B2C

Para ajudá-lo a configurar as tarefas de identidade mais comuns para seus aplicativos, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos dos usuários**. Um fluxo de usuário permite que você determine como os usuários interagem com seu aplicativo quando fazem coisas como entrar, inscrever-se, editar um perfil ou redefinir uma senha. Com os fluxos de usuário, você pode controlar os seguintes recursos:

- Tipos de conta usados para entrar, por exemplo, contas sociais como as do Facebook ou locais
- Atributos a serem coletados do consumidor, tais como nome, código postal e tamanho do calçado
- Autenticação Multifator do Azure AD
- Personalização da interface do usuário
- Informações de que o aplicativo recebe como declarações em um token

É possível criar muitos fluxos dos usuários de diferentes tipos no locatário e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos. Essa flexibilidade permite definir e modificar experiências de identidade com pouca ou nenhuma alteração no seu código. O aplicativo dispara um fluxo de usuário usando uma solicitação de autenticação HTTP padrão que inclui um parâmetro de fluxo de usuário. Um [token](tokens-overview.md) personalizado é recebido como uma resposta.

Os exemplos a seguir mostram o parâmetro de cadeia de caracteres de consulta "p" que especifica o fluxo de usuário a ser usado:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Versões de fluxo do usuário

Azure AD B2C inclui vários tipos de fluxos de usuário:

- **Inscrever-se e entrar** – controla as experiências de inscrição e entrada do consumidor com uma única configuração. Os consumidores são conduzidos pelo caminho certo, de acordo com o contexto. Também estão incluídos os **fluxos de usuário de inscrição ou** **entrada** separados. Mas, geralmente, recomendamos o fluxo de usuário de inscrição e de entrada combinado.
- **Edição de perfil** – permite que os usuários editem suas informações de perfil.
- **Redefinição de senha** – permite configurar se e como os usuários podem redefinir sua senha.

A maioria dos tipos de fluxo de usuário tem uma versão **recomendada** e uma versão **padrão** . Para obter detalhes, consulte [versões de fluxo de usuário](user-flow-versions.md).

> [!IMPORTANT]
> Se você trabalhou com fluxos de usuário em Azure AD B2C antes, observará que alteramos a maneira como fazemos referência às versões de fluxo do usuário. Anteriormente, oferecíamos versões V1 (prontas para produção) e versões V1.1 e V2 (versão prévia). Agora, consolidamos os fluxos de usuário em duas versões:
>
>- Os fluxos de usuário **recomendados** são as novas versões prévias dos fluxos de usuário. Eles são totalmente testados e combinam todos os recursos das versões herdadas **v2** e **v 1.1** . No futuro, os novos fluxos de usuário recomendados serão mantidos e atualizados. Depois de mover para esses novos fluxos de usuário recomendados, você terá acesso aos novos recursos à medida que eles forem lançados.
>- Os fluxos de usuário **padrão** , anteriormente conhecidos como **v1**, estão geralmente disponíveis e fluxos de usuário prontos para produção. Se seus fluxos de usuário forem de missão crítica e dependerem de versões altamente estáveis, você poderá continuar a usar fluxos de usuário padrão, percebendo que essas versões não serão mantidas e atualizadas.
>
>Todos os fluxos de usuário de visualização herdados (V 1.1 e v2) estão em um caminho para substituição em **1º de agosto de 2021**. Sempre que possível, é altamente recomendável que você [alterne para os novos fluxos de usuário **recomendados**](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) o mais rápido possível, para que você sempre possa aproveitar os recursos e as atualizações mais recentes.

## <a name="linking-user-flows"></a>Vinculação de fluxos dos usuários

Um fluxo de usuário de **inscrição ou de entrada** com contas locais inclui um link **Esqueceu a senha?** na primeira página da experiência. Clicar nesse link não dispara automaticamente um fluxo de usuário de redefinição de senha.

Em vez disso, o código de erro `AADB2C90118` é retornado para seu aplicativo. Seu aplicativo precisa lidar com esse código de erro, executando um fluxo de usuário específico que redefine a senha. Para ver um exemplo, dê uma olhada em um [exemplo simples de ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) que demonstra a vinculação de fluxos dos usuários.

## <a name="email-address-storage"></a>Armazenamento de endereços de email

Um endereço de email pode ser exigido como parte de um fluxo de usuário. Se o usuário é autenticado com um provedor de identidade social, o endereço de email é armazenado na propriedade **otherMails**. Se uma conta local for baseada em um nome de usuário, o endereço de email será armazenado em uma propriedade de detalhe de autenticação forte. Se uma conta local for baseada em um endereço de email, ele será armazenado na propriedade **signInNames**.

Não há garantia de que o endereço de email seja verificado em nenhum desses casos. Um administrador de locatários pode desabilitar a verificação de email nas políticas básicas para contas locais. Mesmo se a verificação de endereço de email estiver habilitada, endereços não serão verificados se eles vierem de um provedor de identidade social e não tiverem sido alterados.

Somente as propriedades **otherMails** e **signInNames** são expostas por meio da API Microsoft Graph. O endereço de email na propriedade de detalhe de autenticação forte não está disponível.

## <a name="next-steps"></a>Próximas etapas

Para criar os fluxos de usuário recomendados, siga as instruções em [tutorial: criar um fluxo de usuário](tutorial-create-user-flows.md).
