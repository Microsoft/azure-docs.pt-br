---
title: Políticas de restrição de acesso do Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de restrição de acesso disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 11/23/2020
ms.author: apimpm
ms.openlocfilehash: e38dcf1e12629405ae5f28a987ba20557037ee67
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683453"
---
# <a name="api-management-access-restriction-policies"></a>Políticas de restrição de acesso do Gerenciamento de API

Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](./api-management-policies.md).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Políticas de restrição de acesso

-   [Verificar cabeçalho HTTP](#CheckHTTPHeader) - Impõe a existência e/ou valor de um cabeçalho HTTP.
-   [Limitar a taxa de chamada por assinatura](#LimitCallRate) - Previne picos de uso da API limitando a taxa de chamada, baseado em assinatura.
-   [Limitar a taxa de chamada por chave](#LimitCallRateByKey) - Previne picos de uso da API limitando a taxa de chamada, baseado em chave.
-   [Restringir IP do autor da chamada](#RestrictCallerIPs) - Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.
-   [Definir a cota de uso por assinatura](#SetUsageQuota) - Permite que você aplique uma cota renovável ou permanente de volume de chamada e/ou largura de banda, baseado em assinatura.
-   [Definir a cota de uso por chave](#SetUsageQuotaByKey) - Permite que você aplique uma cota renovável ou permanente de volume de chamada e/ou largura de banda, baseado em chave.
-   [Validar JWT](#ValidateJWT) - Impõe a existência e a validade de JWT extraída de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.

> [!TIP]
> Você pode usar políticas de restrição de acesso em escopos diferentes para finalidades diferentes. Por exemplo, você pode proteger toda a API com a autenticação do AAD aplicando a `validate-jwt` política no nível de API ou pode aplicá-la no nível de operação da API e usá-la `claims` para um controle mais granular.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> Verificar cabeçalho HTTP

Use a política `check-header` para impor que uma solicitação tem um cabeçalho HTTP especificado. Você pode, opcionalmente, verificar se o cabeçalho tem um valor específico ou procurar um intervalo de valores permitidos. Se a verificação falhar, a política encerrará o processamento da solicitação e retornará a mensagem de erro e código de status HTTP especificada pela política.

### <a name="policy-statement"></a>Declaração de política

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Exemplo

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Elemento raiz.                                                                                                                                 | Sim      |
| value        | Valor do cabeçalho HTTP permitido. Quando vários elementos de valor são especificados, a verificação é considerada um sucesso se qualquer um dos valores é uma correspondência. | Não       |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Padrão |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | A mensagem de erro para retornar no corpo da resposta HTTP se o cabeçalho não existe ou tem um valor inválido. Esta mensagem deve conter quaisquer caracteres especiais adequadamente seguidos por caracteres de escape. | Sim      | N/D     |
| failed-check-httpcode      | O código de status HTTP para retornar se o cabeçalho não existir ou tiver um valor inválido.                                                                                        | Sim      | N/D     |
| header-name                | O nome do cabeçalho HTTP para verificar.                                                                                                                                  | Sim      | N/D     |
| ignore-case                | Pode ser definido como True ou False. Se definido como True, maiúsculas e minúsculas são ignoradas quando o valor do cabeçalho é comparado com o conjunto de valores aceitáveis.                                    | Sim      | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída

-   **Escopos da política:** todos os escopos

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Limitar a taxa de chamada por assinatura

A política `rate-limit` impede picos de uso da API para cada assinatura, limitando a taxa de chamadas para um número especificado por um período de tempo especificado. Quando essa política é disparada, o chamador recebe um código de status de resposta `429 Too Many Requests`.

> [!IMPORTANT]
> Essa política pode ser usada apenas uma vez por cada documento de política.
>
> As [Expressões de política](api-management-policy-expressions.md) não podem ser usadas em nenhum dos atributos de política para esta política.

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de limitação, a limitação de taxa nunca é completamente precisa. A diferença entre a configuração e o número real de solicitações permitidas varia de acordo com o volume e a taxa de solicitação, a latência de back-end e outros fatores.

> [!NOTE]
> Para entender a diferença entre limites de taxa e cotas, [consulte limites e cotas de taxa.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Declaração de política

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Nome       | Descrição                                                                                                                                                                                                                                                                                              | Obrigatório |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | Elemento raiz.                                                                                                                                                                                                                                                                                            | Sim      |
| api        | Adicione um ou mais desses elementos para impor um limite de taxa de chamada em APIs dentro do produto. Limites de taxa de chamadas à API e ao produto são aplicados de forma independente. A API pode ser referenciada através de `name` ou `id`. Se ambos os atributos são fornecidos, `id` será usado e `name` será ignorado.                    | Não       |
| operation  | Adicione um ou mais desses elementos para impor um limite de taxa de chamada em operações em uma API. Limites de taxa de chamadas à API, operação e produto são aplicados de forma independente. A operação pode ser referenciada através de `name` ou `id`. Se ambos os atributos são fornecidos, `id` será usado e `name` será ignorado. | Não       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                           | Obrigatório | Padrão |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | O nome da API para a qual aplicar o limite de taxa.                                                | Sim      | N/D     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`. | Sim      | N/D     |
| renewal-period | O período de tempo, em segundos, durante o qual uma cota reinicia.                                              | Sim      | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos de política:** produto, API, operação

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Limitar a taxa de chamadas por chave

> [!IMPORTANT]
> Esse recurso não está disponível na camada **Consumo** do Gerenciamento de API.

A política `rate-limit-by-key` impede picos de uso da API para cada chave, limitando a taxa de chamadas para um número especificado por um período de tempo especificado. A chave pode ter um valor de cadeia de caracteres arbitrária e geralmente é fornecida usando uma expressão de política. A condição de incremento opcional pode ser adicionada para especificar quais solicitações devem ser contadas para obtenção do limite. Quando essa política é disparada, o chamador recebe um código de status de resposta `429 Too Many Requests`.

Para obter mais informações e exemplos dessa política, consulte [Limitação de solicitação avançada com o Gerenciamento de API do Azure](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de limitação, a limitação de taxa nunca é completamente precisa. A diferença entre a configuração e o número real de solicitações permitidas varia de acordo com o volume e a taxa de solicitação, a latência de back-end e outros fatores.

> [!NOTE]
> Para entender a diferença entre limites de taxa e cotas, [consulte limites e cotas de taxa.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Declaração de política

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo a seguir, o limite de taxa é codificado pelo endereço IP do chamador.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Nome              | Descrição   | Obrigatório |
| ----------------- | ------------- | -------- |
| taxa-limite por chave | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                           | Obrigatório | Padrão |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`. | Sim      | N/D     |
| counter-key         | A chave a ser usada para a política de limite de taxa.                                                             | Sim      | N/D     |
| increment-condition | A expressão booliana que especifica se a solicitação deve ser contabilizada para a cota (`true`).        | Não       | N/D     |
| renewal-period      | O período de tempo, em segundos, durante o qual uma cota reinicia.                                              | Sim      | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos da política:** todos os escopos

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Restringir IPs do chamador

A política `ip-filter` filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.

### <a name="policy-statement"></a>Declaração de política

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exemplo

No exemplo a seguir, a política só permite solicitações provenientes do endereço IP único ou do intervalo de endereços IP especificados

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementos

| Nome                                      | Descrição                                         | Obrigatório                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Elemento raiz.                                       | Sim                                                            |
| address                                   | Especifica um único endereço IP no qual filtrar.   | Pelo menos um elemento `address` ou `address-range` é necessário. |
| address-range from="address" to="address" | Especifica um intervalo de endereços IP nos quais filtrar. | Pelo menos um elemento `address` ou `address-range` é necessário. |

### <a name="attributes"></a>Atributos

| Nome                                      | Descrição                                                                                 | Obrigatório                                           | Padrão |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | Um intervalo de endereços IP aos quais o acesso será permitido ou negado.                                        | Necessário quando o elemento `address-range` é usado. | N/D     |
| ip-filter action="allow &#124; forbid"    | Especifica se chamadas para os endereços IP e intervalos de endereços IP especificados devem ou não ser permitidas. | Sim                                                | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada
-   **Escopos da política:** todos os escopos

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Definir cota de uso por assinatura

A política `quota` impõe uma cota renovável ou de tempo de vida de volume de chamadas e/ou largura de banda, para cada assinatura.

> [!IMPORTANT]
> Essa política pode ser usada apenas uma vez por cada documento de política.
>
> As [Expressões de política](api-management-policy-expressions.md) não podem ser usadas em nenhum dos atributos de política para esta política.

> [!NOTE]
> Para entender a diferença entre limites de taxa e cotas, [consulte limites e cotas de taxa.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Declaração de política

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Nome      | Descrição                                                                                                                                                                                                                                                                                  | Obrigatório |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Elemento raiz.                                                                                                                                                                                                                                                                                | Sim      |
| api       | Adicione um ou mais desses elementos para impor a cota de chamada em APIs dentro do produto. Cotas de API e produto são aplicadas de forma independente. A API pode ser referenciada através de `name` ou `id`. Se ambos os atributos são fornecidos, `id` será usado e `name` será ignorado.                    | Não       |
| operation | Adicione um ou mais desses elementos para impor a cota de chamada em operações em uma API. Cotas de operações, APIs e produtos são aplicadas de forma independente. A operação pode ser referenciada através de `name` ou `id`. Se ambos os atributos são fornecidos, `id` será usado e `name` será ignorado. | Não       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                               | Obrigatório                                                         | Padrão |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | O nome da API ou operação à qual a cota se aplica.                                             | Sim                                                              | N/D     |
| largura de banda      | O número total máximo de kilobytes permitidos durante o intervalo de tempo especificado no `renewal-period`. | `calls` ou `bandwidth` ou ainda ambos juntos devem ser especificados. | N/D     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`.     | `calls` ou `bandwidth` ou ainda ambos juntos devem ser especificados. | N/D     |
| renewal-period | O período de tempo, em segundos, durante o qual uma cota reinicia.                                                  | Sim                                                              | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada
-   **Escopos de política:** produto

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Definir uma cota de uso por chave

> [!IMPORTANT]
> Esse recurso não está disponível na camada **Consumo** do Gerenciamento de API.

A política `quota-by-key` impõe uma cota renovável ou de tempo de vida de volume de chamadas e/ou largura de banda, para cada chave. A chave pode ter um valor de cadeia de caracteres arbitrária e geralmente é fornecida usando uma expressão de política. A condição de incremento opcional pode ser adicionada para especificar quais solicitações devem ser contadas para obtenção da cota. Se várias políticas incrementassem o mesmo valor de chave, ele seria incrementado apenas uma vez por solicitação. Quando essa política é disparada, o chamador recebe um código de status de resposta `403 Forbidden`.

Para obter mais informações e exemplos dessa política, consulte [Limitação de solicitação avançada com o Gerenciamento de API do Azure](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Para entender a diferença entre limites de taxa e cotas, [consulte limites e cotas de taxa.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Declaração de política

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo a seguir, a cota é codificada pelo endereço IP do chamador.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Nome  | Descrição   | Obrigatório |
| ----- | ------------- | -------- |
| quota | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                               | Obrigatório                                                         | Padrão |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| largura de banda           | O número total máximo de kilobytes permitidos durante o intervalo de tempo especificado no `renewal-period`. | `calls` ou `bandwidth` ou ainda ambos juntos devem ser especificados. | N/D     |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`.     | `calls` ou `bandwidth` ou ainda ambos juntos devem ser especificados. | N/D     |
| counter-key         | A chave a ser usada para a política de cota.                                                                      | Sim                                                              | N/D     |
| increment-condition | A expressão booliana que especifica se a solicitação deve ser contabilizada para a cota (`true`)             | Não                                                               | N/D     |
| renewal-period      | O período de tempo, em segundos, durante o qual uma cota reinicia.                                                  | Sim                                                              | N/D     |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada
-   **Escopos da política:** todos os escopos

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> Validar JWT

A `validate-jwt` política impõe a existência e a validade de um JWT (token Web JSON) extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.

> [!IMPORTANT]
> A política `validate-jwt` requer que a declaração registrada `exp` seja incluída no token JWT, a menos que o atributo `require-expiration-time` seja especificado e definido como `false`.
> A política `validate-jwt` dá suporte aos algoritmos de assinatura HS256 e RS256. Para HS256, a chave deve ser fornecida embutida na política no formato codificado em base64. Para RS256, a chave pode ser fornecida por meio de um ponto de extremidade de configuração de Open ID ou fornecendo a ID de um certificado carregado que contém a chave pública ou o par de módulo-expoente da chave pública.
> A `validate-jwt` política dá suporte a tokens criptografados com chaves simétricas usando os seguintes algoritmos de criptografia: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Declaração de política

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Exemplos

#### <a name="simple-token-validation"></a>Validação de token simples

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>Validação de token com certificado RSA

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Validação de token do Azure Active Directory

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Validação de token do Azure Active Directory B2C

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizar o acesso para operações baseadas em declarações de token

Este exemplo mostra como usar a política [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base no valor de declarações de token.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição                                                                                                                                                                                                                                                                                                                                           | Obrigatório |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Elemento raiz.                                                                                                                                                                                                                                                                                                                                         | Sim      |
| públicos-alvo           | Contém uma lista de declarações de público-alvo aceitáveis que podem estar presentes no token. Se vários valores de público-alvo estiverem presentes, cada valor será tentado até que todos sejam esgotados (nesse caso, a validação falhará) ou até obter êxito. Pelo menos um público-alvo deve ser especificado.                                                                     | Não       |
| issuer-signing-keys | Uma lista de chaves de segurança codificadas em Base64 usadas para validar tokens assinados. Se várias chaves de segurança estiverem presentes, cada chave será tentada até que todas estejam esgotadas (nesse caso, a validação falha) ou uma com êxito (útil para substituição de token). Elementos-chave têm um atributo `id` opcional usado para correspondência com a declaração `kid`. <br/><br/>Como alternativa, forneça uma chave de assinatura do emissor usando:<br/><br/> - `certificate-id` em formato `<key certificate-id="mycertificate" />` para especificar o identificador de uma entidade de certificado [carregada](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) para o gerenciamento de API<br/>-Par RSA de módulo `n` e expoente `e` no formato `<key n="<modulus>" e="<exponent>" />` para especificar os parâmetros RSA no formato codificado em base64url               | Não       |
| decryption-keys     | Uma lista de chaves codificadas em Base64 para descriptografar os tokens. Se várias chaves de segurança estiverem presentes, cada chave será tentada até que todas as chaves sejam esgotadas (nesse caso, a validação falha) ou uma chave seja bem-sucedida. Elementos-chave têm um atributo `id` opcional usado para correspondência com a declaração `kid`.<br/><br/>Como alternativa, forneça uma chave de descriptografia usando:<br/><br/> - `certificate-id` em formato `<key certificate-id="mycertificate" />` para especificar o identificador de uma entidade de certificado [carregada](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) para o gerenciamento de API                                                 | Não       |
| emissores             | Uma lista de entidades aceitáveis que emitiram o token. Se vários valores de emissor estiverem presentes, cada valor será tentado até que todos sejam esgotados (nesse caso, a validação falhará) ou até obter êxito.                                                                                                                                         | Não       |
| openid-config       | O elemento usado para especificar um ponto de extremidade de configuração de Open ID em conformidade do qual chaves de assinatura e emissor podem ser obtidos.                                                                                                                                                                                                                        | Não       |
| required-claims     | Contém uma lista de declarações cuja presença é esperada no token para que ele possa ser considerado válido. Quando o atributo `match` é definido como `all`, cada valor de declaração na política deve estar presente no token para que a validação seja bem-sucedida. Quando o atributo `match` é definido como `any`, pelo menos uma declaração deve estar presente no token para que a validação seja bem-sucedida. | Não       |

### <a name="attributes"></a>Atributos

| Nome                            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                            | Obrigatório                                                                         | Padrão                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Período de tempo. Use para especificar a diferença de tempo máxima esperada entre os relógios do sistema do emissor do token e a instância do Gerenciamento de API.                                                                                                                                                                                                                                                                                                               | Não                                                                               | 0 segundos                                                                         |
| failed-validation-error-message | Mensagem de erro para retornar no corpo da resposta HTTP se o JWT não passar na validação. Esta mensagem deve conter quaisquer caracteres especiais adequadamente seguidos por caracteres de escape.                                                                                                                                                                                                                                                                                                 | Não                                                                               | A mensagem de erro padrão depende do problema de validação, por exemplo, "O JWT não está presente." |
| failed-validation-httpcode      | O código de status HTTP para retornar se o JWT não passar na validação.                                                                                                                                                                                                                                                                                                                                                                                         | Não                                                                               | 401                                                                               |
| header-name                     | O nome do cabeçalho HTTP contendo o token.                                                                                                                                                                                                                                                                                                                                                                                                         | Um `header-name` `query-parameter-name` ou `token-value` deve ser especificado. | N/D                                                                               |
| nome do parâmetro de consulta            | O nome do parâmetro de consulta que contém o token.                                                                                                                                                                                                                                                                                                                                                                                                     | Um `header-name` `query-parameter-name` ou `token-value` deve ser especificado. | N/D                                                                               |
| valor do token                     | Expressão que retorna uma cadeia de caracteres que contém o token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Um `header-name` `query-parameter-name` ou `token-value` deve ser especificado. | N/D                                                                               |
| id                              | O atributo `id` no elemento `key` permite que você especifique a cadeia de caracteres cuja correspondência será verificada em relação à declaração `kid` no token (se presente) para descobrir a chave apropriada a ser usada para validação de assinatura.                                                                                                                                                                                                                                           | Não                                                                               | N/D                                                                               |
| match                           | O atributo `match` no elemento `claim` especifica se todos os valores de declaração na política devem estar presentes no token para que a validação seja bem-sucedida. Os valores possíveis são:<br /><br /> - `all` – todos os valores de declaração na política devem estar presentes no token para que a validação seja bem-sucedida.<br /><br /> - `any` – pelo menos um valor de declaração na política deve estar presente no token para que a validação seja bem-sucedida.                                                       | Não                                                                               | all                                                                               |
| require-expiration-time         | Booliano. Especifica se uma declaração de expiração é necessária no token.                                                                                                                                                                                                                                                                                                                                                                               | Não                                                                               | true                                                                              |
| require-scheme                  | O nome do esquema de token, por exemplo, "portador". Quando esse atributo for definido, a política garantirá que o esquema especificado esteja presente no valor do cabeçalho de Autorização.                                                                                                                                                                                                                                                                                    | Não                                                                               | N/D                                                                               |
| require-signed-tokens           | Booliano. Especifica se é necessário que um determinado token seja assinado.                                                                                                                                                                                                                                                                                                                                                                                           | Não                                                                               | true                                                                              |
| separator                       | Cadeia de caracteres. Especifica um separador (por exemplo, ",") a ser usado para extrair um conjunto de valores de uma declaração com valores múltiplos.                                                                                                                                                                                                                                                                                                                                          | Não                                                                               | N/D                                                                               |
| url                             | URL ponto de extremidade de configuração de Open ID da qual é possível obter os metadados de configuração de Open ID. A resposta deve ser de acordo com as especificações definidas na URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Para o Azure Active Directory, use a seguinte URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituindo o seu nome de locatário do diretório, por exemplo, `contoso.onmicrosoft.com`. | Sim                                                                              | N/D                                                                               |
| saída-token-variável-nome      | Cadeia de caracteres. Nome da variável de contexto que receberá o valor de token como um objeto do tipo [`Jwt`](api-management-policy-expressions.md) após a validação de token bem-sucedida                                                                                                                                                                                                                                                                                     | Não                                                                               | N/D                                                                               |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada
-   **Escopos da política:** todos os escopos

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

-   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
-   [Transformar APIs](transform-api.md)
-   [Referência de Política](./api-management-policies.md) para uma lista completa das instruções de política e suas configurações
-   [Exemplos de política](./policy-reference.md)
