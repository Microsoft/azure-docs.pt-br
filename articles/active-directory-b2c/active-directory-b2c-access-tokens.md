---
title: Solicitar tokens de acesso - Azure AD B2C | Microsoft Docs
description: Este artigo mostra como configurar um aplicativo cliente e adquirir um token de acesso.
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: parakhj
ms.openlocfilehash: 7202be4e0e9b8b28b5ec1443d6d248c1738da6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: Solicitando tokens de acesso

Um token de acesso (chamado de **token\_de acesso** nas respostas do Azure AD B2C) é uma forma de token de segurança que um cliente pode usar para acessar os recursos que são protegidos por um [servidor de autorização](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), por exemplo, uma API Web. Tokens de acesso são representados como [JWTs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) e contêm informações sobre o servidor do recurso pretendido e as permissões concedidas ao servidor. Ao chamar o servidor do recurso, o token de acesso deve estar presente na solicitação HTTP.

Este artigo descreve como configurar um aplicativo cliente e uma API Web para obter um **token\_ de acesso**.

> [!NOTE]
> **Não há suporte a cadeias de API Web (em nome de) no Azure AD B2C.**
>
> Muitas arquiteturas incluem uma API da Web que precisa chamar outra API Web downstream, ambas protegidas pelo Azure AD B2C. Este cenário é comum em clientes nativos que têm um back-end de API Web que, por sua vez, chama um serviço online da Microsoft, como a API do Azure AD Graph.
>
> Este cenário de API Web encadeada pode ter suporte usando a concessão de Credencial de Portador JWT do OAuth 2.0, também conhecida como fluxo Em nome de. No entanto, o fluxo Em Nome de não está implementado atualmente no Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrar uma API Web e permissões de publicação

Antes de solicitar um token de acesso, você primeiro precisa registrar uma API Web e permissões de publicação (escopos) que podem ser concedidas ao aplicativo cliente.

### <a name="register-a-web-api"></a>Registrar uma API Web

1. No menu de recursos do Azure AD B2C no Portal do Azure, clique em **Aplicativos**.
1. Clique em **+Adicionar**, na parte superior do menu.
1. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "API Contoso".
1. Ativar/desativar a opção **Incluir API da Web/aplicativo Web** como **Sim**.
1. Insira um valor arbitrário para as **URLs de resposta**. Por exemplo, insira: `https://localhost:44316/`. O valor não importa já que uma API não deve receber o token diretamente no Azure AD B2C.
1. Insira um **URI da ID do aplicativo**. Esse é o identificador usado para a API Web. Por exemplo, insira “notas” na caixa. O **URI da ID do aplicativo** seria então `https://{tenantName}.onmicrosoft.com/notes`.
1. Clique em **Criar** para registrar seu aplicativo.
1. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.

### <a name="publishing-permissions"></a>Permissões de publicação

Os escopos, que são análogos às permissões, são necessários quando seu aplicativo chama uma API. Alguns exemplos de escopos são "leitura" ou "gravação". Suponha que você deseja que seu aplicativo nativo ou Web faça a "leitura" de uma API. Seu aplicativo chamaria o Azure AD B2C e solicitaria um token de acesso que oferece acesso ao escopo de "leitura". Para que o Azure AD B2C emita esse token de acesso, o aplicativo precisa ter a permissão de "leitura" da API específica. Para fazer isso, sua API precisa primeiro publicar o escopo de "leitura".

1. Dentro do menu **Aplicativos** do Azure AD B2C, abra a API Web do aplicativo ("API Contoso").
1. Clique em **Escopos publicados**. Isso é onde você define as permissões (escopos) que podem ser concedidas a outros aplicativos.
1. Adicionar **Valores do escopo** conforme necessário (por exemplo, "leitura"). Por padrão, o escopo "user_impersonation" será definido. Você pode ignorar isso se desejar. Insira uma descrição do escopo na coluna **Nome do escopo**.
1. Clique em **Salvar**.

> [!IMPORTANT]
> O **Nome do escopo** é a descrição do **Valor do escopo**. Ao usar o escopo, certifique-se de usar o **Valor do escopo**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Conceder a um aplicativo nativo ou Web permissões para uma API Web

Depois que uma API é configurada para publicar escopos, o aplicativo cliente precisa receber esses escopos através do portal do Azure.

1. Navegue até o menu **Aplicativos** no menu de recursos do Azure AD B2C.
1. Registre um aplicativo cliente ([aplicativo Web](active-directory-b2c-app-registration.md#register-a-web-app) ou [cliente nativo](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) se você ainda não tem um. Se você estiver seguindo este guia como ponto de partida, você precisará registrar um aplicativo cliente.
1. Clique em **Acesso à API**.
1. Clique em **Adicionar**.
1. Selecione sua API Web e os escopos (permissões) que você deseja conceder.
1. Clique em **OK**.

> [!NOTE]
> O Azure AD B2C não solicitará o consentimento dos usuários do aplicativo cliente. Em vez disso, todo o consentimento é fornecido pelo administrador, com base nas permissões configuradas entre os aplicativos descritos acima. Se uma concessão de permissão de um aplicativo for revogada, todos os usuários que antes podiam adquirir essa permissão não serão mais capazes de fazer isso.

## <a name="requesting-a-token"></a>Solicitar um token

Ao solicitar um token de acesso, o aplicativo cliente precisa especificar as permissões desejadas no parâmetro **escopo** da solicitação. Por exemplo, para especificar o **Valor do escopo** "leitura" para a API que tem o **URI da ID do aplicativo** de `https://contoso.onmicrosoft.com/notes`, o escopo seria `https://contoso.onmicrosoft.com/notes/read`. Abaixo está um exemplo de uma solicitação de código de autorização para o ponto de extremidade `/authorize`.

> [!NOTE]
> Atualmente, não há suporte para os domínios personalizados junto com os tokens de acesso. Você deve usar o seu domínio tenantName.onmicrosoft.com na URL de solicitação.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Para adquirir várias permissões na mesma solicitação, você pode adicionar várias entradas separadas por espaços no único parâmetro **scope**. Por exemplo:

Decodificado em URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Codificado em URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Você pode solicitar mais escopos (permissões) para um recurso do que aquelas concedidas para o seu aplicativo cliente. Quando esse for o caso, a chamada será bem-sucedida se pelo menos uma permissão for concedida. O **token\_de acesso** resultante terá sua declaração "scp" preenchida apenas com as permissões que foram concedidas com êxito.

> [!NOTE] 
> Não damos suporte à solicitação de permissões a dois recursos Web diferentes na mesma solicitação. Esse tipo de solicitação falhará.

### <a name="special-cases"></a>Casos especiais

O padrão de OpenID Connect especifica vários valores especiais de "scope". Os escopos especiais a seguir representam a permissão para "acessar o perfil do usuário":

* **openid**: isso solicita um token de ID
* **acesso\_offline**: Isso solicita um token de atualização (usando fluxos de [Código de Autenticação](active-directory-b2c-reference-oauth-code.md)).

Se o parâmetro `response_type` em uma solicitação `/authorize` inclui `token`, o parâmetro `scope` deve incluir o escopo de pelo menos um recurso (diferente de `openid` e `offline_access`) que será concedido. Caso contrário, a solicitação `/authorize` terminará com falha.

## <a name="the-returned-token"></a>O token retornado

Em um **token\_de acesso** obtido com êxito (do ponto de extremidade `/authorize` ou `/token`), as seguintes declarações estarão presentes:

| Nome | Declaração | Descrição |
| --- | --- | --- |
|Público-alvo |`aud` |A **ID do aplicativo** do recurso único ao qual o token concede acesso. |
|Escopo |`scp` |As permissões concedidas ao recurso. Várias permissões concedidas serão separadas por espaço. |
|Entidade Autorizada |`azp` |A **ID do aplicativo** do aplicativo cliente que iniciou a solicitação. |

Quando sua API recebe o **token\_de acesso**, ela deve [validar o token](active-directory-b2c-reference-tokens.md) para provar que o token é autêntico e que tem as declarações corretas.

Estamos sempre abertos a comentários e sugestões! Se você tiver dificuldade com este tópico, poste no Stack Overflow usando a marca ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Para solicitações de recursos, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Próximas etapas

* Criar uma API Web usando [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Criar uma API Web usando [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
