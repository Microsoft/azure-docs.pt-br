---
title: "Alterações no ponto de extremidade do Azure AD v2.0 | Microsoft Docs"
description: "Uma descrição das alterações que estão sendo feitas nos protocolos de visualização pública do modelo de aplicativo v 2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ae73833a68db14804dc40eaf07ff7d3effaa9052
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>Atualizações Importantes para os Protocolos de Autenticação v 2.0
Atenção, desenvolvedores! Nas próximas duas semanas, realizaremos algumas atualizações em nossos protocolos de autenticação v 2.0 que podem significar alterações de interrupção em qualquer aplicativo que você tenha gravado durante o período de visualização.  

## <a name="who-does-this-affect"></a>A quem isso afeta?
Todos os aplicativos que foram gravados para usar o ponto de extremidade de autenticação convergido v 2.0, 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Mais informações sobre o ponto de extremidade v 2.0 podem ser encontradas [aqui](active-directory-appmodel-v2-overview.md).

Se você tiver criado um aplicativo usando o ponto de extremidade v 2.0 codificando diretamente para o protocolo v 2.0, usando qualquer um de nossos middlewares da Web OAuth ou OpenID Connect, ou outras bibliotecas de terceiros para realizar a autenticação, deverá estar preparado para testar seus projetos e fazer as alterações necessárias.

## <a name="who-doesnt-this-affect"></a>A quem isso não afeta?
Quaisquer aplicativos que foram gravados no ponto de extremidade de autenticação de produção do AD do Azure

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Esse protocolo é imutável e não terá qualquer alteração.

Além disso, se seu aplicativo usa **somente** a biblioteca do ADAL para realizar a autenticação, você não precisará alterar nada.  O ADAL protegeu seu aplicativo contra alterações.  

## <a name="what-are-the-changes"></a>Quais são as alterações?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Remover o valor x5t dos cabeçalhos JWT
O ponto de extremidade v 2.0 usa muito os tokens JWT que contêm uma seção de parâmetros do cabeçalho com metadados relevantes sobre o token.  Se você decodificasse o cabeçalho de um dos nossos JWTs atuais, encontraria algo como:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Onde as propriedades "x5t" e "kid" identificam a chave pública que deve ser usada para validar a assinatura do token, conforme recuperada no ponto de extremidade de metadados OpenID Connect.

A alteração que estamos fazendo aqui é remover a propriedade "x5t".  Você pode continuar a usar os mesmos mecanismos para validar os tokens, mas deve contar somente com a propriedade "kid" para recuperar a chave pública correta, conforme especificado no protocolo OpenID Connect. 

> [!IMPORTANT]
> **Seu trabalho: Verifique se seu aplicativo não depende da existência do valor x5t.**
> 
> 

### <a name="removing-profileinfo"></a>Remover profile_info
Anteriormente, o ponto de extremidade v 2.0 retornava um objeto JSON codificado na base64 nas respostas do token chamadas `profile_info`.  Ao solicitar um token de acesso no ponto de extremidade v 2.0 enviando uma solicitação para:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A resposta pareceria com o seguinte objeto JSON:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O valor `profile_info` continha informações sobre o usuário que entrou no aplicativo: seu nome de exibição, primeiro nome, sobrenome, endereço de email, identificador, etc.  Basicamente, `profile_info` foi usado para o cache do token e a exibição.

Agora, estamos removendo o valor `profile_info` , mas não se preocupe, ainda fornecemos essas informações para os desenvolvedores em um local ligeiramente diferente.  Em vez de `profile_info`, o ponto de extremidade v 2.0 agora retornará um `id_token` em cada resposta do token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Você pode decodificar e analisar o id_token para recuperar as mesmas informações recebidas do profile_info.  O id_token é um JSON Web Token (JWT), contendo conteúdo conforme especificado pelo OpenID Connect.  O código para fazer isso deve ser bastante similar. Você só precisa extrair o segmento intermediário (corpo) do id_token e decodificá-lo com base64 para acessar o objeto JSON.

Nas próximas duas semanas, você deverá codificar seu aplicativo para recuperar as informações do usuário em `id_token` ou `profile_info`, o que estiver presente.  Dessa forma, quando a alteração for feita, seu aplicativo poderá lidar com a transição de `profile_info` para `id_token` sem interrupção.

> [!IMPORTANT]
> **Seu trabalho: Verifique se seu aplicativo não depende da existência do valor `profile_info`.**
> 
> 

### <a name="removing-idtokenexpiresin"></a>Remover id_token_expires_in
Semelhante ao ocorrido com `profile_info`, também estamos removendo o parâmetro `id_token_expires_in` das respostas.  Anteriormente, o ponto de extremidade v 2.0 retornaria um valor `id_token_expires_in` junto com cada resposta id_token, por exemplo, em uma resposta de autorização:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou em uma resposta do token:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O valor `id_token_expires_in` indicaria o número de segundos que o id_token permaneceria válido.  Agora, estamos removendo o valor `id_token_expires_in` em sua totalidade.  Em vez disso, você pode usar as declarações `nbf` e `exp` padrão do OpenID Connect para examinar a validade de um id_token.  Confira a [referência do token v 2.0](active-directory-v2-tokens.md) para obter mais informações sobre essas declarações.

> [!IMPORTANT]
> **Seu trabalho: Verifique se seu aplicativo não depende da existência do valor `id_token_expires_in`.**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>Alterar as declarações retornadas por scope=openid
Essa alteração será a mais importante. Na verdade, isso afetará quase todos os aplicativos que usam o ponto de extremidade v 2.0.  Muitos aplicativos enviam solicitações para o ponto de extremidade v 2.0 usando o escopo de `openid`, como:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoje, quando o usuário concede permissão para o escopo de `openid`, seu aplicativo recebe uma grande quantidade de informações sobre o usuário do id_token resultante.  As declarações em um id_token podem incluir o nome, nome de usuário preferido, endereço de email, ID do objeto e mais.

Nesta atualização, estamos alterando as informações às quais o escopo de `openid` dá acesso ao aplicativo para ter uma melhor conformidade com a especificação do OpenID Connect.  O escopo `openid` apenas permitirá que seu aplicativo conecte o usuário e receba um identificador específico do aplicativo para o usuário na declaração `sub` do id_token.  As declarações em um id_token apenas com o escopo de `openid` concedido serão destituídas de qualquer informação de identificação pessoal.  Os exemplos de declarações do id_token são:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Se você quiser obter informações de identificação pessoal (PII) sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário.  Estamos introduzindo o suporte para dois novos escopos da especificação OpenID Connect, os escopos `email` e `profile`, que permitem que você faça isso.

* O escopo `email` é muito simples — permite que seu aplicativo acesse o endereço de email principal do usuário por meio da declaração `email` no id_token.  Observe que a declaração `email` nem sempre estará presente nos id_tokens. Ela só será incluída se estiver disponível no perfil do usuário.
* O escopo `profile` permite que seu aplicativo acesse todas as outras informações básicas sobre o usuário – seu nome, nome de usuário preferido, ID do objeto etc.

Isso permite que você codifique seu aplicativo com uma divulgação mínima – você pode solicitar ao usuário apenas o conjunto de informações que seu aplicativo precisa para fazer seu trabalho.  Se você quiser continuar a obter o conjunto completo de informações do usuário que seu aplicativo recebe atualmente, deverá incluir todos os três escopos em suas solicitações de autorização:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Seu aplicativo pode começar a enviar imediatamente os escopos de `email` e `profile` e o ponto de extremidade v 2.0 aceitará esses dois escopos e começará a solicitar as permissões dos usuários conforme necessário.  No entanto, a alteração na interpretação do escopo de `openid` não entrará em vigor por algumas semanas.

> [!IMPORTANT]
> **Seu trabalho: adicione os escopos `profile` e `email` se seu aplicativo requer informações sobre o usuário.**  Observe que o ADAL incluirá essas duas permissões nas solicitações por padrão. 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>Remover o emissor da barra à direita.
Anteriormente, o valor do emissor que aparece nos tokens do ponto de extremidade v 2.0 assumiu a forma

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Onde o guid era o tenantId do locatário do AD do Azure que emitiu o token.  Com essas alterações, o valor do emissor transforma-se

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

em ambos os tokens e no documento de descoberta do OpenID Connect.

> [!IMPORTANT]
> **Seu trabalho: Verifique se seu aplicativo aceita o valor do emissor com e sem uma barra à direita durante a validação do emissor.**
> 
> 

## <a name="why-change"></a>Por que alterar?
A principal motivação para introduzir essas alterações é para que seja compatível com a especificação padrão do OpenID Connect.  Ao ser compatível com o OpenID Connect, esperamos minimizar as diferenças entre a integração com serviços de identidade do Microsoft e outros serviços de identidade do setor.  Queremos permitir que os desenvolvedores usem suas bibliotecas de autenticação de fonte aberta favoritas sem ter que alterar as bibliotecas para aceitarem as diferenças da Microsoft.

## <a name="what-can-you-do"></a>O que você pode fazer?
A partir de hoje, você pode começar a fazer todas as alterações descritas acima.  Você deve imediatamente:

1. **Remova qualquer dependência no parâmetro do cabeçalho `x5t`.**
2. **Lide de modo elegante com a transição de `profile_info` para `id_token` nas respostas de tokens.**
3. **Remova qualquer dependência do parâmetro de resposta `id_token_expires_in`.**
4. **Adicione os escopos `profile` e `email` ao seu aplicativo se ele precisar de informações básicas do usuário.**
5. **Aceite os valores do emissor nos tokens com e sem uma barra à direita.**

Nossa [documentação do protocolo v 2.0](active-directory-v2-protocols.md) já foi atualizada para refletir essas alterações, portanto, você pode usá-la como referência ao ajudar a atualizar seu código.

Se você tiver mais dúvidas sobre o escopo das alterações, fique à vontade para nos contactar no Twitter em @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Com que frequência as alterações do protocolo ocorrerão?
Não podemos prever qualquer alteração de interrupção nos protocolos de autenticação.  Podemos intencionalmente agrupar essas alterações em uma versão para que você não tenha que passar por esse tipo de processo de atualização novamente pouco tempo depois.  Claro, continuaremos a adicionar recursos ao serviço de autenticação v 2.0 convergido que você pode aproveitar, mas essas alterações devem ser aditivas e não interromperem o código existente.

Por fim, gostaríamos de agradecer você por experimentar as coisas durante esse período de visualização.  As informações e experiências de nossos primeiros participantes foram inestimáveis até o momento e esperamos que você continue a compartilhar suas opiniões e ideias.

Boa codificação!

Divisão de Identidade da Microsoft

