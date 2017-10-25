---
title: "Referência de tokens do Azure Active Directory v2.0 | Microsoft Docs"
description: "Os tipos de tokens e declarações emitidos pelo ponto de extremidade v2.0 do Azure AD"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ec25d4375647a2c8983d7573b9912e544fc3e7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Referência de tokens do Azure Active Directory v2.0
O ponto de extremidade do Azure AD (Azure Active Directory) v 2.0 emite vários tipos de tokens de segurança em cada [fluxo de autenticação](active-directory-v2-flows.md). Esta referência descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

> [!NOTE]
> O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Tipos de tokens
O ponto de extremidade v2.0 dá suporte ao [protocolo de autorização do OAuth 2.0](active-directory-v2-protocols.md), que usa tokens de acesso e de atualização. O ponto de extremidade v 2.0 também dá suporte à autenticação e à entrada via [OpenID Connect](active-directory-v2-protocols.md). O OpenID Connect introduz um terceiro tipo de token, o token de ID. Cada um desses tokens é representado como um token de *portador*.

Um token de portador é um token de segurança leve que concede ao portador acesso a um recurso protegido. O portador é qualquer parte que possa apresentar o token. Embora uma parte deva se autenticar no Azure AD para receber o token de portador, se não forem tomadas medidas para proteger o token durante a transmissão e o armazenamento, ele poderá ser interceptado e usado por uma parte não planejada. Alguns tokens de segurança têm um mecanismo interno para evitar que partes não autorizadas os utilizem, mas os tokens de portador não têm isso. Os tokens de portador devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido sem esse tipo de segurança, um terceiro mal-intencionado poderá usar um ataque"man-in-the-middle" para adquirir o token e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre verifique se o aplicativo transmite e armazena tokens de portador com segurança. Para obter mais considerações de segurança sobre tokens de portador, confira [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo ponto de extremidade v2.0 são implementados como Tokens Web JSON (JWTs). JWT é uma maneira compacta e protegida por URL para transferir informações entre duas partes. As informações em um JWT são chamadas de *declaração*. É uma asserção de informações sobre o portador e a entidade do token. As declarações JWT são objetos JSON (JavaScript Object Notation) que são codificados e serializados para transmissão. Como os JWTs emitidos pelo ponto de extremidade v2.0 são assinados, mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para fins de depuração. Para obter mais informações sobre JWTs, confira a [especificação JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID
Um token de ID é uma forma de token de segurança de entrada que o aplicativo recebe quando ele executa a autenticação usando [OpenID Connect](active-directory-v2-protocols.md). Tokens de ID são representadas como [JWTs](#types-of-tokens) e contêm declarações que você pode usar para conectar o usuário ao aplicativo. Você pode usar as declarações em um token de ID de várias maneiras. Normalmente, os administradores usam tokens de ID para exibir informações de conta ou para tomar decisões de controle de acesso em um aplicativo. O ponto de extremidade v2.0 emitirá somente um tipo de token de ID, que tem um conjunto consistente de declarações, independentemente do tipo de usuário conectado. O formato e o conteúdo de tokens de ID são os mesmos para usuários de contas pessoais da Microsoft e para contas corporativas ou de estudante.

Atualmente, os tokens de ID são assinados, mas não criptografados. Quando o aplicativo recebe um token de ID, ele deve [validar a assinatura](#validating-tokens) para comprovar a autenticidade do token e validar algumas declarações no token para comprovar sua validade. As declarações validadas por um aplicativo variam dependendo dos requisitos do cenário, mas o aplicativo deve executar algumas [validações de declaração comuns](#validating-tokens) em cada cenário.

Fornecemos os detalhes completos sobre declarações em tokens de ID nas seções a seguir, além de um token de ID de exemplo. Observe que as declarações em tokens de ID não são retornadas em uma ordem específica. Além disso, novas declarações podem ser introduzidas em tokens de ID a qualquer momento. O aplicativo não deve ser interrompido quando novas declarações são introduzidas. A lista a seguir inclui as declarações que o aplicativo pode interpretar atualmente de forma confiável. Você pode encontrar mais detalhes na [especificação OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Token de ID de exemplo
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Para praticar, para inspecionar as declarações no token de ID de exemplo, cole o token de ID de exemplo em [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Declarações em tokens de ID
| Nome | Declaração | Valor de exemplo | Descrição |
| --- | --- | --- | --- |
| audiência |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifica o destinatário pretendido do token. Em tokens de ID, a audiência é a ID do aplicativo, atribuída ao o aplicativo no Portal de Registro de Aplicativos da Microsoft. O aplicativo deve validar esse valor e rejeitar o token, caso o valor não seja correspondente. |
| emissor |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. O aplicativo deve validar a declaração do emissor para garantir que o token venha do ponto de extremidade v2.0. Ele também deve usar a parte GUID da declaração para restringir o conjunto de locatários podem entrar no aplicativo. O GUID que indica que o usuário é um consumidor da conta da Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| emitido em |`iat` |`1452285331` |A hora em que o token foi emitido, representada na época. |
| hora de expiração |`exp` |`1452289231` |A hora em que o token se torna inválido, representada na época. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| não antes de |`nbf` |`1452285331` |O horário em que o token se torna inválido, representado no horário da época. Ele geralmente é o mesmo que o horário de emissão. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| version |`ver` |`2.0` |A versão do token de ID, conforme definida pelo Azure AD. Para o ponto de extremidade da v2.0, o valor é `2.0`. |
| ID do locatário |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Um GUID que representa o locatário do Azure AD do qual o usuário é proveniente. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O escopo `profile` é necessário para receber essa declaração. |
| hash de código |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |O hash de código é incluído em tokens de ID apenas quando eles são emitidos com um código de autorização OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| hash de token de acesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |O hash do token de acesso é incluído em tokens de ID apenas quando eles são emitidos com um token de acesso OAuth 2.0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| nonce |`nonce` |`12345` |O nonce é uma estratégia para migrar ataques de reprodução de token. O aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce` . O valor que você fornece na solicitação é emitido na declaração `nonce` do token de ID, sem modificação. O aplicativo pode verificar o valor em relação ao valor especificado por ele na solicitação, o que associa a sessão do aplicativo a um token de ID específico. O aplicativo deve executar essa validação durante o processo de validação do token de ID. |
| name |`name` |`Babe Ruth` |A declaração de nome fornece um valor legível por humanos que identifica o assunto do token. Não há garantia de que o valor seja exclusivo. Ele é mutável e foi projetado para ser usado apenas para fins de exibição. O escopo `profile` é necessário para receber essa declaração. |
| email |`email` |`thegreatbambino@nyy.onmicrosoft.com` |O endereço de email principal associado à conta de usuário, se houver um. Seu valor é mutável e pode ser alterado ao longo do tempo. O escopo `email` é necessário para receber essa declaração. |
| nome de usuário preferencial |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |O nome de usuário principal que representa o usuário no ponto de extremidade v 2.0. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado ao longo do tempo. Uma vez que é mutável, esse valor não deve ser usado para tomar decisões de autorização. O escopo `profile` é necessário para receber essa declaração. |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Pode ser usado para executar verificações de autorização de forma segura, por exemplo, quando o token é usado para acessar um recurso, e pode ser usado como uma chave nas tabelas de banco de dados. Como a entidade está sempre presente nos tokens emitidos pelo Azure AD, é recomendável usar esse valor em um sistema de autorização de uso geral. O assunto é, no entanto, um identificador de paridade - é exclusivo a uma ID de aplicativo específica.  Portanto, se um único usuário entra em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração do assunto.  Isso pode ou não ser desejável, dependendo dos requisitos de arquitetura e de privacidade. |
| ID do objeto |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | O identificador imutável de um objeto do sistema de identidade da Microsoft, nesse caso, uma conta de usuário.  Também pode ser usada para realizar verificações de autorização com segurança e como uma chave em tabelas de banco de dados. Essa ID identifica exclusivamente o usuário entre os aplicativos - dois aplicativos diferentes autenticando o mesmo usuário receberão o mesmo valor na declaração `oid`.  Isso significa que pode ser usada ao fazer consultas nos serviços online da Microsoft, como o Microsoft Graph.  O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada conta de usuário.  Como o `oid` permite que vários aplicativos correlacionem usuários, o escopo `profile` é necessário a fim de receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferentes em cada locatário - são consideradas contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. |

### <a name="access-tokens"></a>Tokens de acesso
Atualmente, os tokens de acesso emitidos pelo ponto de extremidade v 2.0 só podem ser consumidos por Microsoft Services. Os aplicativos não precisam executar nenhuma validação ou inspeção de tokens de acesso para qualquer um dos cenários que atualmente têm suporte. Você pode tratar os tokens de acesso como completamente opacos. São apenas cadeias de caracteres que o aplicativo pode passar para a Microsoft em solicitações HTTP.

Em breve, o ponto de extremidade v2.0 apresentará no seu aplicativo a capacidade de receber tokens de acesso de outros clientes. Nesse momento, as informações neste tópico de referência serão atualizadas com as informações de que você precisa para que o aplicativo execute a validação de token de acesso e outras tarefas semelhantes.

Quando você solicita um token de acesso do ponto de extremidade v2.0, ele também retorna metadados sobre o token de acesso para uso pelo aplicativo. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. O aplicativo usa os metadados para executar o armazenamento em cache inteligente de tokens de acesso sem ter que analisar abertamente o token de acesso.

### <a name="refresh-tokens"></a>Tokens de atualização
Tokens de atualização são tokens de segurança que o aplicativo pode usar para obter novos tokens de acesso em um fluxo OAuth 2.0. O aplicativo pode usar tokens de atualização para obter acesso de longo prazo a recursos em nome de um usuário sem a necessidade de interação com o usuário.

Os tokens de atualização têm vários recursos. Um token de atualização recebido durante uma solicitação de token para um recurso pode ser resgatado para tokens de acesso para um recurso totalmente diferente.

Para receber uma atualização em uma resposta de token, o aplicativo deve solicitar e receber o escopo `offline_acesss`. Para saber mais sobre o escopo `offline_access`, confira o artigo [Consentimento e escopos](active-directory-v2-scopes.md).

Os tokens de atualização são e sempre serão completamente opacos para o aplicativo. Eles são emitidos pelo ponto de extremidade v2.0 do Azure AD e podem ser inspecionados e interpretados apenas pelo ponto de extremidade v2.0. Eles têm longa duração, mas o aplicativo não deve ser escrito para esperar que um token de atualização dure por qualquer período de tempo. Tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao ponto de extremidade v2.0.

Ao resgatar um token de atualização para um novo token de acesso (e se o aplicativo tiver concedido o escopo `offline_access` ), você recebe um novo token de atualização na resposta de token. Salve o token de atualização emitido mais recentemente, para substituir o que foi usado na solicitação. Isso garante que os tokens de atualização permaneçam válidos pelo tempo máximo possível.

## <a name="validating-tokens"></a>Validando tokens
Atualmente, a única validação de token que os aplicativos devem precisar executar é validar os tokens de ID. Para validar um token de ID, o aplicativo deve validar a assinatura do token de ID e as declarações contidas nele.

<!-- TODO: Link -->
A Microsoft fornece exemplos de código e bibliotecas que mostram como lidar facilmente com a validação de token. Nas próximas seções, descrevemos o processo subjacente. Várias bibliotecas de software livre de terceiros também estão disponíveis para validação de JWT. Há pelo menos uma opção de biblioteca para quase todas as plataformas e idiomas.

### <a name="validate-the-signature"></a>validar a assinatura
Um JWT contém três segmentos, que são separados pelo caractere `.` . O primeiro segmento é conhecido como o *cabeçalho*, o segundo segmento é o *corpo* e o terceiro segmento é a *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token de ID, de modo que o aplicativo possa confiar nele.

Os tokens de ID são assinados usando algoritmos de criptografia assimétrica padrão do setor, como RSA 256. O cabeçalho do token de ID tem informações sobre o método de criptografia e a chave usados para assinar o token. Por exemplo:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token. A declaração `kid` indica a chave pública que foi usada para assinar o token.

A qualquer momento, o ponto de extremidade v 2.0 pode assinar um token de ID, usando qualquer conjunto específico de pares de chaves públicas-privadas. O ponto de extremidade v2.0 gira periodicamente o possível conjunto de chaves. Assim, o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo ponto de extremidade v2.0 é a cada 24 horas.

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o documento de metadados do OpenID Connect localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente a URL em um navegador!
>
>

Esse documento de metadados é um objeto JSON com várias informações úteis, como o local dos vários pontos de extremidade exigidos para a autenticação do OpenID Connect.  O documento também inclui um *jwks_uri*, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. O documento JSON localizado em jwks_uri tem todas as informações de chaves públicas que estão em uso atualmente. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar qual chave pública neste documento foi usada para assinar um token. Assim, ele executa a validação da assinatura usando a chave pública correta e o algoritmo indicado.

Executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de software livre estão disponíveis para ajudá-lo.

### <a name="validate-the-claims"></a>Validar as declarações
Quando o aplicativo recebe um token de ID na conexão do usuário, ele também deve fazer algumas verificações nas declarações no token de ID. Elas incluem, mas sem limitação:

* A declaração **público-alvo**, para verificar se o token de ID foi destinado a ser dado ao aplicativo
* As declarações **não antes de** e **data de expiração**, para verificar se o token de ID não expirou
* A declaração **emissor**, para verificar se o token foi de fato emitido para o aplicativo pelo ponto de extremidade v2.0
* **nonce**, como uma redução do ataque de reprodução do token

Para obter uma lista completa das validações de declaração que o aplicativo deve executar, confira a [especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Os detalhes dos valores esperados para essas declarações estão incluídos acima na [seção tokens de ID](# ID tokens).

## <a name="token-lifetimes"></a>Tempos de vida do token
Fornecemos os tempos de vida de token a seguir para fins informativos. As informações podem ajudá-lo a desenvolver e depurar aplicativos. Os aplicativos não devem ser escritos com a expectativa de que esses tempos de vida permaneçam constantes. Os tempos de vida de token podem mudar e mudarão a qualquer momento.

| A criptografia do token | Tempo de vida | Descrição |
| --- | --- | --- |
| Tokens de ID (contas corporativos ou de estudante) |1 hora |Tokens de ID normalmente são válidos por uma hora. O aplicativo Web pode usar esse mesmo tempo de vida para manter sua própria sessão com o usuário (recomendável) ou você pode escolher um tempo de vida de sessão totalmente diferente. Se o aplicativo precisar obter um novo token de ID, precisará fazer uma nova solicitação de entrada para o ponto de extremidade de autorização v2.0. Se o usuário tiver uma sessão de navegador válida com o ponto de extremidade v2.0, talvez não precise inserir suas credenciais novamente. |
| Tokens de ID (contas pessoais) |24 horas |Tokens de ID para contas pessoais geralmente são válidos por 24 horas. O aplicativo Web pode usar esse mesmo tempo de vida para manter sua própria sessão com o usuário (recomendável) ou você pode escolher um tempo de vida de sessão totalmente diferente. Se o aplicativo precisar obter um novo token de ID, precisará fazer uma nova solicitação de entrada para o ponto de extremidade de autorização v2.0. Se o usuário tiver uma sessão de navegador válida com o ponto de extremidade v2.0, talvez não precise inserir suas credenciais novamente. |
| Tokens de acesso (contas corporativas ou de estudante) |1 hora |Indicado nas respostas de token como parte dos metadados do token. |
| Tokens de acesso (contas pessoais) |1 hora |Indicado nas respostas de token como parte dos metadados do token. Tokens de acesso que são emitidos em nome de contas pessoais podem ser configurados com um tempo de vida diferente, mas o valor típico é uma hora. |
| Tokens de atualização (conta corporativa ou de estudante) |Até 14 dias |Um único token de atualização é válido para um máximo de 14 dias. No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos. Portanto, o aplicativo deve continuar tentando usar um token de atualização até que ele falhe ou até que o aplicativo o substitua por um novo token de atualização. Um token de atualização também se torna inválido caso se passem 90 dias desde que o usuário inseriu suas credenciais. |
| Tokens de atualização (contas pessoais) |Até 1 ano |Um único token de atualização é válido para um máximo de 1 ano. No entanto, o token de atualização pode se tornar inválido a qualquer momento por vários motivos. Portanto, o aplicativo deve continuar a tentar usar um token de atualização até falhar. |
| Códigos de autorização (contas corporativas ou de estudante) |10 minutos |Os códigos de autorização são propositadamente de curta duração e devem ser resgatados imediatamente para tokens de acesso e tokens de atualização quando os tokens são recebidos. |
| Códigos de autorização (contas pessoais) |5 minutos |Os códigos de autorização são propositadamente de curta duração e devem ser resgatados imediatamente para tokens de acesso e tokens de atualização quando os tokens são recebidos. Códigos de autorização que são emitidos em nome de contas pessoais são para uso ocasional. |
