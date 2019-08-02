---
title: Expressões de política no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre expressões de política no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 9a42028891f0a8677127221c8ed4cff73a22103a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498546"
---
# <a name="api-management-policy-expressions"></a>Expressões de política de Gerenciamento de API
Este artigo descreve a sintaxe das expressões de política C# 7. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) fornecida implicitamente e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos do .NET Framework.

Para mais informações:

- Veja como fornecer informações de contexto ao serviço de back-end. Use as políticas [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) para fornecer essas informações.
- Como usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações baseadas em declarações de token.
- Como usar um rastreamento de [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados das avaliações.
- Como usar expressões com as políticas [Obter do cache](api-management-caching-policies.md#GetFromCache) e [Armazenar em cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de Gerenciamento de API. Defina uma duração que coincida com o cache de resposta do serviço de back-end conforme especificado pela diretiva `Cache-Control` do serviço de backup.
- Veja como realizar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida do back-end usando as políticas [Controlar fluxo](api-management-advanced-policies.md#choose) e [Definir corpo](api-management-transformation-policies.md#SetBody).
- Para baixar as instruções de política, confira o repositório GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).


## <a name="Syntax"></a> Sintaxe
Expressões de instrução única são colocadas em `@(expression)`, em que `expression` é uma instrução de expressão C# bem formada.

Expressões de várias instruções são colocadas em `@{expression}`. Todos os caminhos de código dentro de expressões de várias instruções devem terminar com uma instrução `return`.

## <a name="PolicyExpressionsExamples"></a> Exemplos

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Uso
Expressões podem ser usadas como valores de atributo ou de texto em qualquer [política](api-management-policies.md) de Gerenciamento de API (a menos que a referência de política especifique o contrário).

> [!IMPORTANT]
> Quando você usa expressões de política, há apenas uma verificação limitada de expressões de política quando a política é definida. Expressões são executadas pelo gateway em tempo de execução e todas as exceções são geradas pelo resultado de expressões de política em um erro em tempo de execução.

## <a name="CLRTypes"></a> Tipos do .NET framework permitidos em expressões de política
A tabela a seguir lista os tipos do .NET Framework e seus membros permitidos em expressões de política.

|Tipo|Membros com suporte|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Todas|
|Newtonsoft.Json.JsonConvert|SerializeObject, desserializaobject|
|Newtonsoft.Json.Linq.Extensions|Todas|
|Newtonsoft.Json.Linq.JArray|Todas|
|Newtonsoft.Json.Linq.JConstructor|Todas|
|Newtonsoft.Json.Linq.JContainer|Todas|
|Newtonsoft.Json.Linq.JObject|Todas|
|Newtonsoft.Json.Linq.JProperty|Todas|
|Newtonsoft.Json.Linq.JRaw|Todas|
|Newtonsoft.Json.Linq.JToken|Todas|
|Newtonsoft.Json.Linq.JTokenType|Todas|
|Newtonsoft.Json.Linq.JValue|Todas|
|System.Array|Todas|
|System.BitConverter|Todas|
|System.Boolean|Todas|
|System.Byte|Todas|
|System.Char|Todas|
|System. Collections. Generic. Dictionary < TKey, TValue >|Todas|
|System.Collections.Generic.HashSet\<T>|Todas|
|System.Collections.Generic.ICollection\<T>|Todas|
|System. Collections. Generic. IDictionary < TKey, TValue >|Todas|
|System.Collections.Generic.IEnumerable\<T>|Todas|
|System.Collections.Generic.IEnumerator\<T>|Todas|
|System.Collections.Generic.IList\<T>|Todas|
|System.Collections.Generic.IReadOnlyCollection\<T>|Todas|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Todas|
|System.Collections.Generic.ISet\<T>|Todas|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Todas|
|System.Collections.Generic.List\<T>|Todas|
|System.Collections.Generic.Queue\<T>|Todas|
|System.Collections.Generic.Stack\<T>|Todas|
|System.Convert|Todas|
|System.DateTime|(Construtor), adicionar, subdias, addhora, addmilésimos de segundo, addminutos, addmeses, adicionar, submarcações, AddYears, data, Day, DayOfWeek, DayOfYear, DaysInMonth, hora, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minuto, MinValue, mês, agora , Análise, segundo, subtrair, tiques, TimeOfDay, hoje, ToString, UtcNow, year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Todas|
|System.Decimal|Todas|
|System.Double|Todas|
|System.Exception|Todas|
|System.Guid|Todas|
|System.Int16|Todas|
|System.Int32|Todas|
|System.Int64|Todas|
|System.IO.StringReader|Todas|
|System.IO.StringWriter|Todas|
|System.Linq.Enumerable|Todas|
|System.Math|Todas|
|System.MidpointRounding|Todas|
|System.Net.WebUtility|Todas|
|System.Nullable|Todas|
|System.Random|Todas|
|System.SByte|Todas|
|System.Security.Cryptography.AsymmetricAlgorithm|Todas|
|System.Security.Cryptography.CipherMode|Todas|
|System.Security.Cryptography.HashAlgorithm|Todas|
|System.Security.Cryptography.HashAlgorithmName|Todas|
|System.Security.Cryptography.HMAC|Todas|
|System.Security.Cryptography.HMACMD5|Todas|
|System.Security.Cryptography.HMACSHA1|Todas|
|System.Security.Cryptography.HMACSHA256|Todas|
|System.Security.Cryptography.HMACSHA384|Todas|
|System.Security.Cryptography.HMACSHA512|Todas|
|System.Security.Cryptography.KeyedHashAlgorithm|Todas|
|System.Security.Cryptography.MD5|Todas|
|System.Security.Cryptography.Oid|Todas|
|System.Security.Cryptography.PaddingMode|Todas|
|System.Security.Cryptography.RNGCryptoServiceProvider|Todas|
|System.Security.Cryptography.RSA|Todas|
|System.Security.Cryptography.RSAEncryptionPadding|Todas|
|System.Security.Cryptography.RSASignaturePadding|Todas|
|System.Security.Cryptography.SHA1|Todas|
|System.Security.Cryptography.SHA1Managed|Todas|
|System.Security.Cryptography.SHA256|Todas|
|System.Security.Cryptography.SHA256Managed|Todas|
|System.Security.Cryptography.SHA384|Todas|
|System.Security.Cryptography.SHA384Managed|Todas|
|System.Security.Cryptography.SHA512|Todas|
|System.Security.Cryptography.SHA512Managed|Todas|
|System.Security.Cryptography.SymmetricAlgorithm|Todas|
|System.Security.Cryptography.X509Certificates.PublicKey|Todas|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todas|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Nome|
|System.Security.Cryptography.X509Certificates.X509Certificate|Todas|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todas|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todas|
|System.Security.Cryptography.X509Certificates.X509NameType|Todas|
|System.Single|Todas|
|System.String|Todas|
|System.StringComparer|Todas|
|System.StringComparison|Todas|
|System.StringSplitOptions|Todas|
|System.Text.Encoding|Todas|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, corresponder, corresponder, substituir, cancelar escape, dividir|
|System.Text.RegularExpressions.RegexOptions|Todas|
|System.Text.StringBuilder|Todas|
|System.TimeSpan|Todas|
|System.TimeZone|Todas|
|System.TimeZoneInfo.AdjustmentRule|Todas|
|System.TimeZoneInfo.TransitionTime|Todas|
|System.TimeZoneInfo|Todas|
|System.Tuple|Todas|
|System.UInt16|Todas|
|System.UInt32|Todas|
|System.UInt64|Todas|
|System.Uri|Todas|
|System.UriPartial|Todas|
|System.Xml.Linq.Extensions|Todas|
|System.Xml.Linq.XAttribute|Todas|
|System.Xml.Linq.XCData|Todas|
|System.Xml.Linq.XComment|Todas|
|System.Xml.Linq.XContainer|Todas|
|System.Xml.Linq.XDeclaration|Todas|
|System.Xml.Linq.XDocument|Todos, exceto de: Carregamento|
|System.Xml.Linq.XDocumentType|Todas|
|System.Xml.Linq.XElement|Todas|
|System.Xml.Linq.XName|Todas|
|System.Xml.Linq.XNamespace|Todas|
|System.Xml.Linq.XNode|Todas|
|System.Xml.Linq.XNodeDocumentOrderComparer|Todas|
|System.Xml.Linq.XNodeEqualityComparer|Todas|
|System.Xml.Linq.XObject|Todas|
|System.Xml.Linq.XProcessingInstruction|Todas|
|System.Xml.Linq.XText|Todas|
|System.Xml.XmlNodeType|Todas|

## <a name="ContextVariables"></a> Variável de contexto
Uma variável chamada `context` está implicitamente disponível em toda [expressão](api-management-policy-expressions.md#Syntax) de política. Seus membros fornecem informações pertinentes para `\request`. Todos os membros de `context` são somente leitura.

|Variável de contexto|Valores de métodos, propriedades e parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|context|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implantação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan – intervalo de tempo entre o valor de carimbo de data/hora e a hora atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Produto](#ref-context-product)<br /><br /> [Solicitação](#ref-context-request)<br /><br /> RequestId: Guid – identificador exclusivo de solicitação<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Assinatura](#ref-context-subscription)<br /><br /> Carimbo de data/hora: DateTime – ponto no tempo em que a solicitação foi recebida<br /><br /> Rastreamento: bool - indica que se o rastreamento está ativado ou desativado <br /><br /> [Usuário](#ref-context-user)<br /><br /> [Variáveis](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Para obter mais informações sobre context.LastError, consulte [Error handling](api-management-error-handling-policies.md) (Tratamento de erro).|
|<a id="ref-context-operation"></a>noticioso. Operacional|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|APIs: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>noticioso. Quest|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se a solicitação não tiver um corpo.<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de solicitação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>noticioso. Responde|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>noticioso. Scriçõe|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>noticioso. Usuário|Email: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identidades: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocolos: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false): Em que T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são usados para a leitura dos corpos de mensagens de solicitação e resposta em um tipo `T` especificado. Por padrão, o método usa o fluxo do corpo de mensagem original e o torna indisponível após seu retorno. Para evitar que o método opere em uma cópia do fluxo do corpo, defina o parâmetro `preserveContent` como `true`. Clique [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de parâmetro de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Retorna o valor da variável convertida para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método gerará uma exceção se o tipo especificado não corresponder ao tipo real da variável retornada.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de Autenticação Básica HTTP, o método retornará um objeto do tipo `BasicAuthCredentials`, caso contrário, o método retornará nulo.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de Autenticação Básica HTTP válido no cabeçalho de solicitação, o método retornará `true` e o parâmetro do resultado conterá um valor do tipo `BasicAuthCredentials`, caso contrário, o método retornará `false`.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará um objeto do tipo `Jwt`, caso contrário, o método retornará `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `Jwt`, caso contrário, o método retornará `false`.|
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de declaração separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – cyphertext a ser descriptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|
|bool VerifyNoRevocation (entrada: este System. Security. Cryptography. X509Certificates. X509Certificate2)|Executa uma validação de cadeia X. 509 sem verificar o status de revogação do certificado.<br /><br />objeto de entrada-certificado<br /><br />Retorna `true` se a validação for realizada com sucesso; `false` se a validação falhar.|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
