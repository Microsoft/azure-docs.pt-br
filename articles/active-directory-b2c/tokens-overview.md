---
title: Visão geral de tokens - Azure Active Directory B2C
description: Saiba mais sobre os tokens usados no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516363"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada [fluxo de autenticação](application-types.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

O Azure AD B2C dá suporte aos protocolos [OAuth 2.0 e OpenID Connect](protocols-overview.md), que usam tokens para autenticação e acesso seguro aos recursos. Todos os tokens usados no Azure AD B2C são [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm declarações de informações sobre o portador e o assunto do token.

Os tokens a seguir são usados na comunicação com o Azure AD B2C:

- *Token de ID* - um JWT que contém declarações que você pode usar para identificar usuários em seu aplicativo. Esse token é enviado com segurança em solicitações HTTP para a comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID como desejar. Elas são comumente usadas para exibir informações de conta ou para tomar decisões de controle de acesso em um aplicativo. Os tokens de ID são assinados, mas não são criptografados. Quando seu aplicativo ou a API recebe um token de ID, deve validar a assinatura para provar que o token é autêntico. Seu aplicativo ou a API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Token de acesso* - um JWT que contém declarações que você pode usar para identificar as permissões concedidas para suas APIs. Os tokens de acesso são assinados, mas não são criptografados. Os tokens de acesso são usados para fornecer acesso aos servidores de APIs e recursos.  Quando sua API recebe um token de acesso, deve validar a assinatura para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as declarações validadas por um aplicativo podem variar, mas seu aplicativo deve executar algumas validações de declaração comuns em cada cenário.
- *Tokens de atualização* - os tokens de atualização são usados para adquirir novos tokens de ID e tokens de acesso em um fluxo OAuth 2.0. Eles fornecem ao aplicativo acesso de longo prazo aos recursos em nome dos usuários sem a necessidade de interação com os usuários. Os tokens de atualização são opacos para seu aplicativo. Eles são emitidos pelo Azure AD B2C e só podem ser inspecionados e interpretados por ele. Eles têm longa duração, mas o aplicativo não deve ser escrito com a expectativa de que um token de atualização durará por um período de tempo específico. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de o aplicativo saber se um token de atualização é válido, é tentando resgatá-lo fazendo uma solicitação de token ao Azure AD B2C. Ao resgatar um token de atualização para um novo token, você receberá um novo token de atualização na resposta de token. Salve o novo token de atualização. Ele substitui o token de atualização que você usou anteriormente na solicitação. Isso ajuda a garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível. Observe que os aplicativos de página única que usam o fluxo de código de autorização com PKCE sempre têm um tempo de vida de token de atualização de 24 horas. [Saiba mais sobre as implicações de segurança de tokens de atualização no navegador](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Pontos de extremidade

Um [aplicativo registrado](tutorial-register-applications.md) recebe tokens e se comunica com o Azure AD B2C enviando solicitações para estes pontos de extremidade:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Os tokens de segurança que seu aplicativo recebe do Azure AD B2C podem vir dos pontos de extremidade `/authorize` ou `/token`. Quando os tokens de ID são adquiridos do ponto de extremidade `/authorize`, isso é feito usando o [fluxo implícito](implicit-flow-single-page-application.md), que é geralmente usado para os usuários entrarem em aplicativos Web baseados em JavaScript. Quando os tokens de ID são adquiridos do ponto de extremidade `/token`, isso é feito usando o [fluxo de código de autorização](openid-connect.md#get-a-token), que mantém o token escondido do navegador.

## <a name="claims"></a>Declarações

Ao usar o Azure AD B2C, você tem um controle refinado sobre o conteúdo de seus tokens. É possível configurar os [fluxos dos usuários](user-flow-overview.md) e [políticas personalizadas](custom-policy-overview.md) para enviar determinados conjuntos de dados do usuário em declarações exigidas pelo aplicativo. Essas declarações podem incluir propriedades padrão, como **displayName** e **emailAddress**. Seus aplicativos podem usar essas declarações para autenticar usuários e solicitações com segurança.

As declarações em tokens de ID não são retornadas em uma ordem específica. Novas declarações podem ser introduzidas em tokens de ID a qualquer momento. Seu aplicativo não deve ser interrompido à medida que novas declarações são introduzidas. Você também pode incluir [atributos de usuário personalizados](user-flow-custom-attributes.md) em suas declarações.

A tabela a seguir lista as declarações que você pode esperar em tokens de ID e tokens de acesso emitidos pelo Azure AD B2C.

| Nome | Declaração | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Público | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Para o Azure AD B2C, o público-alvo é a ID do aplicativo. O aplicativo deve validar esse valor e rejeitar o token, caso ele não corresponda. Público-alvo é sinônimo de recurso. |
| Emissor | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Também identifica o diretório no qual o usuário foi autenticado. O aplicativo deve validar a declaração do emissor para garantir que o token venha do ponto de extremidade apropriado. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Hora de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Não antes de | `nbf` | `1438535543` | O horário em que o token se torna inválido, representado no horário da época. Esse horário geralmente é o mesmo no qual o token foi emitido. O aplicativo deve usar essa declaração para verificar a validade do tempo de vida do token. |
| Versão | `ver` | `1.0` | A versão do token de ID, conforme definida pelo Azure AD B2C. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído em um token de ID apenas quando o token é emitido junto com um código de autorização OAuth 2.0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Para saber mais sobre como realizar essa validação, veja a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de token de acesso incluído em um token de ID apenas quando o token é emitido junto com um token de acesso OAuth 2.0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Para saber mais sobre como realizar essa validação, veja a [Especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para reduzir ataques de reprodução de token. O aplicativo pode especificar um nonce em uma solicitação de autorização usando o parâmetro de consulta `nonce`. O valor que você fornecer na solicitação é emitido sem modificações na declaração `nonce` de um token de ID somente. Essa declaração permite que seu aplicativo verifique o valor em relação ao valor especificado na solicitação. O aplicativo deve executar essa validação durante o processo de validação do token de ID. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado somente com políticas mais antigas. |
| Política de estrutura confiável | `tfp` | `b2c_1_signupsignin1` | O nome da política que foi usada para adquirir o token da ID. |
| Hora da autenticação | `auth_time` | `1438535543` | A hora em que um usuário inseriu credenciais pela última vez, representada na época. Não há nenhuma discriminação entre essa autenticação ser uma entrada nova, uma sessão de logon único (SSO) ou outro tipo de entrada. A `auth_time` é a última vez em que o aplicativo (ou usuário) iniciou uma tentativa de autenticação em relação ao Azure AD B2C. O método usado para autenticar não é diferenciado. |
| Escopo | `scp` | `Read`| As permissões concedidas ao recurso para um token de acesso. Várias permissões concedidas são separadas por espaço. |
| Entidade Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **ID do aplicativo** do aplicativo cliente que iniciou a solicitação. |

## <a name="configuration"></a>Configuração

As seguintes propriedades são usadas para [gerenciar tempos de vida de tokens de segurança](configure-tokens.md) emitidos pelo Azure AD B2C:

- **Duração dos tokens de acesso e ID (minutos)** : o tempo de vida do token portador do OAuth 2.0 usado para obter acesso a um recurso protegido. O padrão é de 60 minutos. O mínimo (inclusive) é de 5 minutos. O máximo (inclusive) é de 1440 minutos.

- **Tempo de vida do token de atualização (dias)** - o período de tempo máximo antes que um token de atualização possa ser usado para adquirir um novo token de acesso ou de ID. O período de tempo também abrange a aquisição de um novo token de atualização se o seu aplicativo tiver recebido o escopo de `offline_access`. O padrão é 14 dias. O mínimo (inclusive) é de um dia. O máximo (inclusive) é 90 dias.

- **Atualizar vida útil da janela deslizante do token (dias)** - após esse período de tempo, o usuário é forçado a autenticar novamente, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico. O padrão é 90 dias. O mínimo (inclusive) é de um dia. O máximo (inclusive) é de 365 dias.

Os casos de uso a seguir são ativados usando estas propriedades:

- Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, contanto que o usuário esteja continuamente ativo no aplicativo. Você pode definir **Atualizar duração da janela deslizante do token (dias)** para **Não consolidado** em seu fluxo de usuário.
- Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

## <a name="compatibility"></a>Compatibilidade

As propriedades a seguir são usadas para [gerenciar a compatibilidade do token](configure-tokens.md):

- **Emissor (iss) reivindicação** - essa propriedade identifica o locatário do Azure AD B2C que emitiu o token. O valor padrão é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor de `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclui IDs do locatário do Azure AD B2C e do fluxo de usuário usado na solicitação de token. Se seu aplicativo ou biblioteca precisar do Azure AD B2C para ser compatível com as [especificações do OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), use esse valor.

- **Subject (sub) declaração** - Esta propriedade identifica a entidade para a qual o token afirma informações. O valor padrão é **ObjectID**, que preenche a declaração `sub` no token com a ID de objeto do usuário. O valor de **Sem suporte** é fornecido somente para compatibilidade com versões anteriores. É recomendável que você troque para **ObjectID** assim que for possível.

- **Reivindicação representando o ID da política**: essa propriedade identifica o tipo de reivindicação no qual o nome da política usado na solicitação de token é preenchido. O valor padrão é `tfp`. O valor de `acr` é fornecido somente para compatibilidade com versões anteriores.

## <a name="pass-through"></a>Passagem

Quando a jornada de um usuário é iniciada, o Azure AD B2C recebe um token de acesso de um provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Você habilita uma declaração em seu fluxo de usuário para [passar o token](idp-pass-through-user-flow.md) para os aplicativos que você registra no Azure ad B2C. Seu aplicativo deve estar usando um [fluxo de usuário recomendado](user-flow-versions.md) para aproveitar a passagem do token como uma declaração.

Atualmente, o Azure AD B2C é compatível somente com a passagem do token de acesso dos provedores de identidade do OAuth 2.0, que incluem o Facebook e o Google. Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="validation"></a>Validação

Para validar um token, o aplicativo deve verificar a assinatura e as declarações do token. Muitas bibliotecas de software livre estão disponíveis para validação de JWTs, dependendo da linguagem preferencial. É recomendável explorar essas opções em vez de implementar sua própria lógica de validação.

### <a name="validate-signature"></a>Validar a assinatura

Um JWT contém três segmentos: um *cabeçalho*, um *corpo* e uma *assinatura*. O segmento de assinatura pode ser usado para validar a autenticidade do token, de modo que seu aplicativo possa confiar nele. Os tokens do Azure AD B2C são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256.

O cabeçalho do token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor da declaração **alg** é o algoritmo que foi usado para assinar o token. O valor da declaração **kid** é a chave pública que foi usada para assinar o token. A qualquer momento, o Azure AD B2C pode assinar um token usando qualquer opção de um conjunto de pares de chaves públicas-privadas. O Azure AD B2C alterna o possível conjunto de chaves periodicamente. Seu aplicativo deve ser gravado para tratar dessas alterações de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD B2C é a cada 24 horas. Para lidar com alterações de chave inesperadas, seu aplicativo deve ser gravado para recuperar as chaves públicas novamente caso receba um valor de **criança** inesperado.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Usando esse ponto de extremidade, os aplicativos podem solicitar informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Seu locatário do Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias informações úteis. Os metadados contêm **jwks_uri**, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. Esse local é fornecido aqui, mas é melhor buscar o local dinamicamente usando o documento de metadados e analisando **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
O documento JSON localizado nessa URL contém todas as informações de chave pública em uso em um momento específico. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

O documento de metadados para a política `B2C_1_signupsignin1` no locatário `contoso.onmicrosoft.com` está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Para determinar qual política foi usada para assinar um token (e onde ir para solicitar os metadados), você tem duas opções. Primeiro, o nome da política é incluído no `tfp` (padrão) ou na `acr` declaração (conforme configurado) no token. Você pode analisar as declarações fora do corpo do JWT decodificando em base 64 o corpo e desserializando a cadeia de caracteres JSON resultante. A `tfp` `acr` declaração ou é o nome da política que foi usada para emitir o token. A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Ambos os métodos são válidos.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de software livre estão disponíveis para ajudar você a validar um token.

### <a name="validate-claims"></a>Validar declarações

Quando seus aplicativos ou a API recebe um token de ID, também deve fazer diversas verificações em relação às declarações no token de ID. As declarações a seguir devem ser verificadas:

- **audience** - verifica se o token de ID foi destinado ao seu aplicativo.
- **not before** e **expiration time** - verifica se o token de ID não expirou.
- **issuer** - verifica se o token foi emitido para o aplicativo pelo Azure AD B2C.
- **nonce** - uma estratégia para redução de ataque de reprodução de token.

Para obter uma lista completa de validações que seu aplicativo deve realizar, confira a [especificação do OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](access-tokens.md).

