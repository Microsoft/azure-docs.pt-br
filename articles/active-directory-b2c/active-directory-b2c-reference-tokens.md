---
title: "Referência de token - Azure AD B2C | Microsoft Docs"
description: Os tipos de tokens emitidos no Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 92087e4553580a5fe14e647d014e493bc7e47b67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Referência de token

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada [fluxo de autenticação](active-directory-b2c-apps.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de tokens
O Azure AD B2C dá suporte ao [protocolo de autorização do OAuth 2.0](active-directory-b2c-reference-protocols.md), que usa tokens de acesso e tokens de atualização. Ele também oferece suporte à autenticação e conexão por meio do [OpenID Connect](active-directory-b2c-reference-protocols.md), que introduz um terceiro tipo de token, o id_token. Cada um desses tokens é representado como um token de portador.

Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. O portador é qualquer parte que possa apresentar o token. Primeiro, o Azure AD B2Cdeve autenticar uma parte para que ela possa receber um token de portador. Porém, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele poderá ser interceptado e usado por uma parte não planejada. Alguns tokens de segurança têm um mecanismo interno para impedir que partes não autorizadas os usem, mas os tokens de portador não têm esse mecanismo. Eles devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS).

Se um token de portador for transmitido fora de um canal seguro, uma parte mal-intencionada poderá usar um ataque por parte de intermediários para adquirir o token e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança são aplicáveis ao se armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura.

Para obter considerações de segurança adicionais sobre tokens de portador, consulte [RFC 6750 Seção 5](http://tools.ietf.org/html/rfc6750).

Muitos dos tokens emitidos pelo Azure AD B2C são implementados como tokens Web JSON (JWTs). Um JWT é um meio compacto e protegido por URL de transferir informações entre duas partes. Os JWTs contêm informações conhecidas como declarações. Trata-se de asserções de informações sobre o portador e o assunto do token. As declarações em JWTs são objetos JSON codificados e serializados para transmissão. Como os JWTs emitidos pelo Azure AD B2C são assinados, mas não criptografados, você pode inspecionar facilmente o conteúdo de um JWT para depurá-lo. Estão disponíveis várias ferramentas que podem fazer isso, incluindo [jwt.ms](https://jwt.ms). Para saber mais sobre JWTs, confira [Especificações de JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de ID

Um token de ID é uma forma de token de segurança que seu aplicativo recebe dos pontos de extremidade `/authorize` e `/token` do Azure AD B2C. Os tokens de ID são representados como [JWTs](#types-of-tokens)e contêm declarações que você pode usar para identificar usuários em seu aplicativo. Quando os tokens de ID são adquiridos do ponto de extremidade `/authorize`, isso é feito usando o [fluxo implícito](active-directory-b2c-reference-spa.md), que é geralmente usado para os usuários entrarem em aplicativos Web baseados em javascript. Quando os tokens de ID são adquiridos do ponto de extremidade `/token`, isso é feito usando o [fluxo de código confidencial](active-directory-b2c-reference-oidc.md), que mantém o token escondido do navegador. Isso permite que o token seja enviado com segurança em solicitações HTTP para a comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID como desejar. Elas são comumente usadas para exibir informações de conta ou para tomar decisões de controle de acesso em um aplicativo.  

Os tokens de ID são assinados, mas não são criptografados atualmente. Quando seu aplicativo ou a API recebe um token de ID, deve [validar a assinatura](#token-validation) para provar que o token é autêntico. Seu aplicativo ou a API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas [validações de declaração comuns](#token-validation) em cada cenário.

#### <a name="sample-id-token"></a>Token de ID de exemplo
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Tokens de acesso

Um token de acesso é uma forma de token de segurança que seu aplicativo recebe dos pontos de extremidade `/authorize` e `/token` do Azure AD B2C. Os tokens de acesso também são representados como [JWTs](#types-of-tokens) e contêm declarações que você pode usar para identificar as permissões concedidas às suas APIs. Os tokens de acesso são assinados, mas não são criptografados atualmente. Os tokens de acesso devem ser usados para fornecer acesso aos servidores de APIs e recursos. Saiba mais sobre como [usar tokens de acesso](active-directory-b2c-access-tokens.md). 

Quando sua API recebe um token de acesso, deve [validar a assinatura](#token-validation) para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas [validações de declaração comuns](#token-validation) em cada cenário.

### <a name="claims-in-id-and-access-tokens"></a>Declarações em tokens de ID e de acesso

Ao usar o Azure AD B2C, você tem um controle refinado sobre o conteúdo de seus tokens. Você pode configurar [políticas](active-directory-b2c-reference-policies.md) para enviar determinados conjuntos de dados do usuário em declarações que o aplicativo requer para suas operações. Essas declarações podem incluir propriedades padrão, como o `displayName` e o `emailAddress` do usuário. Também podem incluir [atributos de usuário personalizados](active-directory-b2c-reference-custom-attr.md) que você pode definir no diretório do B2C. Cada token de ID e de acesso que você recebe contém um determinado conjunto de declarações relacionadas à segurança. Seus aplicativos podem usar essas declarações para autenticar usuários e solicitações com segurança.

Observe que as declarações em tokens de ID não são retornadas em uma ordem específica. Além disso, novas declarações podem ser introduzidas em tokens de ID a qualquer momento. Seu aplicativo não deve ser interrompido à medida que novas declarações são introduzidas. Aqui estão as declarações que você espera que existam em tokens de ID e de acesso emitidos pelo Azure AD B2C. As declarações adicionais são determinadas por políticas. Para praticar, tente inspecionar as declarações no token de ID de exemplo colando-o em [jwt.ms](https://jwt.ms). Mais detalhes podem ser encontrados na [especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Declaração | Valor de exemplo | Descrição |
| --- | --- | --- | --- |
| Público-alvo |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Uma declaração de audiência identifica o destinatário pretendido do token. Para o Azure AD B2C, o público-alvo é a ID de aplicativo de seu aplicativo, conforme atribuída a ele no portal de registro do aplicativo. O aplicativo deve validar esse valor e rejeitar o token, caso ele não corresponda. |
| Emissor |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Essa declaração identifica o STS (serviço de token de segurança) que constrói e retorna o token. Também identifica o diretório do Azure AD no qual o usuário foi autenticado. O aplicativo deve validar a declaração do emissor para garantir que o token venha do ponto de extremidade Azure Active Directory v2.0. |
| Emitido em |`iat` |`1438535543` |Essa declaração é a hora em que o token foi emitido, representada na época. |
| Hora de expiração |`exp` |`1438539443` |A declaração de hora de expiração é a hora em que o token se torna inválido, representada na época. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Não antes de |`nbf` |`1438535543` |Essa declaração é a hora na qual o token se torna válido, representada na época. Essa geralmente é a mesma hora em que o token foi emitido. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Versão |`ver` |`1.0` |Essa é a versão do token de ID, conforme definido pelo Azure AD. |
| Hash de código |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Um hash de código é incluído em um token de ID apenas quando o token é emitido junto com um código de autorização OAuth 2.0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais detalhes sobre como realizar essa validação, veja a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash de token de acesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Um hash de token de acesso é incluído em um token de ID apenas quando o token é emitido junto com um token de acesso OAuth 2.0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais detalhes sobre como realizar essa validação, veja a [Especificação do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce |`nonce` |`12345` |Um nonce é uma estratégia usada para reduzir ataques de reprodução de token. O aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce` . O valor que você fornecer na solicitação será emitido sem modificações na declaração `nonce` de um token de ID somente. Isso permite ao aplicativo verificar o valor em relação ao valor que ele especificou na solicitação, que associa a sessão do aplicativo a determinado token de ID. O aplicativo deve executar essa validação durante o processo de validação do token de ID. |
| Subject |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Essa é a entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório. Para obter mais informações, consulte [Azure Active Directory B2C: configuração de token, de sessão e de logon único](active-directory-b2c-token-session-sso.md). |
| Referência de classe de contexto de autenticação |`acr` |Não aplicável |Não usado atualmente, exceto no caso de políticas mais antigas. Para obter mais informações, consulte [Azure Active Directory B2C: configuração de token, de sessão e de logon único](active-directory-b2c-token-session-sso.md). |
| Política de estrutura confiável |`tfp` |`b2c_1_sign_in` |Esse é o nome da política que foi usada para adquirir o token de ID. |
| Hora da autenticação |`auth_time` |`1438535543` |Essa declaração é a hora em que um usuário inseriu credenciais pela última vez, representada na época. |

### <a name="refresh-tokens"></a>Tokens de atualização
Os tokens de atualização são tokens de segurança que o aplicativo pode usar para adquirir novos tokens de ID e tokens de acesso em um fluxo do OAuth 2.0. Eles fornecem ao aplicativo acesso de longo prazo aos recursos em nome dos usuários sem a necessidade de interação com os usuários.

Para receber um token de atualização em uma resposta de token, o aplicativo deve solicitar o escopo `offline_acesss` . Para saber mais sobre o escopo `offline_access` , consulte a [referência de protocolo do AD B2C do Azure](active-directory-b2c-reference-protocols.md).

Os tokens de atualização são, e sempre serão, completamente opacos para seu aplicativo. Eles são emitidos pelo Azure AD e só podem ser inspecionados e interpretados por ele. Eles têm longa duração, mas o aplicativo não deve ser escrito com a expectativa de que um token de atualização durará por um período de tempo específico. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao AD do Azure.

Ao resgatar um token de atualização para um novo token (e se o escopo `offline_access` o tiver sido concedido ao aplicativo), você receberá um novo token de atualização na resposta de token. Você deve salvar o token de atualização recém-emitido. Ele deve substituir o token de atualização que você usou anteriormente na solicitação. Isso ajuda a garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível.

## <a name="token-validation"></a>Validação de token
Para validar um token, o aplicativo deve verificar a assinatura e as declarações do token.

Muitas bibliotecas de software livre estão disponíveis para validação de JWTs, dependendo da linguagem preferencial. É recomendável explorar essas opções em vez de implementar sua própria lógica de validação. As informações neste guia podem ajudá-lo a aprender a usar essas bibliotecas adequadamente.

### <a name="validate-the-signature"></a>validar a assinatura
Um JWT contém três segmentos, separados pelo caractere `.` . O primeiro segmento é o *cabeçalho*, o segundo é o *corpo* e o terceiro é a *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token, de modo que seu aplicativo possa confiar nele.

Os tokens do Azure AD B2C são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256. O cabeçalho do token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token. A declaração `kid` indica a chave pública específica que foi usada para assinar o token.

A qualquer momento, o Azure AD pode assinar um token usando qualquer opção de determinado conjunto de pares de chaves públicas-privadas. O Azure AD gira o possível conjunto de chaves periodicamente. Portanto, o aplicativo deve ser escrito para lidar com essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Isso permite que os aplicativos busquem informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. O diretório B2C contém um documento de metadados JSON para cada política. Por exemplo, o documento de metadados para a política `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` é o diretório B2C usado para autenticar o usuário e `b2c_1_sign_in` é a política usada para adquirir o token. Para determinar qual política foi usada para assinar um token (e onde buscar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no token. Você pode analisar as declarações fora do corpo do JWT decodificando em base 64 o corpo e desserializando a cadeia de caracteres JSON resultante. A declaração `acr` será o nome da política que foi usada para emitir o token.  A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Ambos os métodos são válidos.

O documento de metadados é um objeto JSON que contém várias informações úteis. Isso inclui o local dos pontos de extremidade necessários para realizar a autenticação OpenID Connect. Também inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. Esse local é fornecido aqui, mas é melhor buscar o local dinamicamente usando o documento de metadados e analisando `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

O documento JSON localizado nessa URL contém todas as informações de chave pública em uso em um momento específico. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de software livre estão disponíveis para ajudá-lo, se necessário.

### <a name="validate-the-claims"></a>Validar as declarações
Quando o aplicativo ou a API recebe um token de ID, também deve fazer diversas verificações em relação às declarações no token de ID. Elas incluem, mas sem limitação:

* A declaração **audiência** : verifica se o token de ID se destinava a ser fornecido a seu aplicativo.
* As declarações **não antes** de e **hora de expiração**: verificam se o token de ID não expirou.
* A declaração **emissor** : verifica se o token foi de fato emitido para o aplicativo pelo Azure AD.
* O **nonce**: essa é uma estratégia para redução de ataques de reprodução de token.

Para obter uma lista completa de validações que seu aplicativo deve realizar, consulte a [especificação do OpenID Connect](https://openid.net). Os detalhes dos valores esperados para essas declarações estão incluídos na [seção sobre tokens](#types-of-tokens)anterior.  

## <a name="token-lifetimes"></a>Tempos de vida do token
Os tempos de vida de token a seguir são fornecidos para expandir seus conhecimentos. Eles poderão ajudá-lo quando você desenvolver e depurar aplicativos. Observe que seus aplicativos não devem ser escritos com a esxpectativa de que esses tempos de vida permaneçam constantes. Eles podem e vão ser alterados. Leia mais sobre a [personalização de tempos de vida de token](active-directory-b2c-token-session-sso.md) no Azure AD B2C.

| A criptografia do token | Tempo de vida | Descrição |
| --- | --- | --- |
| Tokens de ID |Uma hora |Tokens de ID normalmente são válidos por uma hora. O aplicativo Web pode usar esse tempo de vida para manter suas próprias sessões com usuários (recomendado). Também é possível escolher um tempo de vida de sessão diferente. Se o aplicativo precisar obter um novo token de ID, ele simplesmente precisará fazer uma nova solicitação de conexão ao Azure AD. Se um usuário tiver uma sessão de navegador válida com o Azure AD, talvez ele não precise inserir suas credenciais novamente. |
| Tokens de atualização |Até 14 dias |Um único token de atualização é válido para um máximo de 14 dias. No entanto, um token de atualização pode se tornar inválido a qualquer momento por vários motivos. Seu aplicativo deve continuar a tentar usar um token de atualização até que a solicitação falhe ou até que o aplicativo substitua o token de atualização por um novo. Um token de atualização também pode se tornar inválido se 90 dias se passaram desde a última vez em que o usuário inseriu credenciais. |
| Códigos de autorização |Cinco minutos |Intencionalmente, os códigos de autorização são de curta duração. Eles devem ser resgatados imediatamente para tokens de acesso, tokens de ID ou tokens de atualização ao serem recebidos. |

