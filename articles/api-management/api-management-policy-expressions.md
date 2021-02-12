---
title: Expressões de política no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre expressões de política no Gerenciamento de API do Azure. Veja exemplos e exiba recursos adicionais disponíveis.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219308"
---
# <a name="api-management-policy-expressions"></a>Expressões de política de Gerenciamento de API
Este artigo discute a sintaxe de expressões de política no C# 7. Toda expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) fornecida implicitamente e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos do .NET Framework.

Para mais informações:

- Veja como fornecer informações de contexto ao serviço de back-end. Use as políticas [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) para fornecer essas informações.
- Como usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações baseadas em declarações de token.
- Como usar um rastreamento de [Inspetor de API](./api-management-howto-api-inspector.md) para ver como as políticas são avaliadas e os resultados das avaliações.
- Como usar expressões com as políticas [Obter do cache](api-management-caching-policies.md#GetFromCache) e [Armazenar em cache](api-management-caching-policies.md#StoreToCache) para configurar o cache de resposta de Gerenciamento de API. Defina uma duração que coincida com o cache de resposta do serviço de back-end conforme especificado pela diretiva `Cache-Control` do serviço de backup.
- Veja como realizar a filtragem de conteúdo. Remova os elementos de dados da resposta recebida do back-end usando as políticas [Controlar fluxo](api-management-advanced-policies.md#choose) e [Definir corpo](api-management-transformation-policies.md#SetBody).
- Para baixar as instruções de política, consulte o repositório GitHub [API-Management-Samples/Policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="syntax"></a><a name="Syntax"></a> Sintaxe
Expressões de instrução única são colocadas em `@(expression)`, em que `expression` é uma instrução de expressão C# bem formada.

Expressões de várias instruções são colocadas em `@{expression}`. Todos os caminhos de código dentro de expressões de várias instruções devem terminar com uma instrução `return`.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Exemplos

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Uso
Expressões podem ser usadas como valores de atributo ou de texto em qualquer [política](api-management-policies.md) de Gerenciamento de API (a menos que a referência de política especifique o contrário).

> [!IMPORTANT]
> Quando você usa expressões de política, há apenas uma verificação limitada de expressões de política quando a política é definida. Expressões são executadas pelo gateway em runtime e todas as exceções são geradas pelo resultado de expressões de política em um erro de runtime.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> Tipos do .NET framework permitidos em expressões de política
A tabela a seguir lista os tipos do .NET Framework e seus membros permitidos em expressões de política.

|Tipo|Membros com suporte|
|--------------|-----------------------|
|Newtonsoft.Jsem. Formatação|Tudo|
|Newtonsoft.Json.Jsonconvert|SerializeObject, desserializaobject|
|Newtonsoft.Json.Linq.Extensions|Tudo|
|Newtonsoft.Json.Linq.JArray|Tudo|
|Newtonsoft.Json.Linq.JConstructor|Tudo|
|Newtonsoft.Json.Linq.JContainer|Tudo|
|Newtonsoft.Json.Linq.JObject|Tudo|
|Newtonsoft.Json.Linq.JProperty|Tudo|
|Newtonsoft.Json.Linq.JRaw|Tudo|
|Newtonsoft.Json.Linq.JToken|Tudo|
|Newtonsoft.Json.Linq.JTokenType|Tudo|
|Newtonsoft.Json.Linq.JValue|Tudo|
|System.Array|Tudo|
|System.BitConverter|Tudo|
|System.Boolean|Tudo|
|System.Byte|Tudo|
|System.Char|Tudo|
|System.Collections.Generic.Dictionary<TKey, TValue>|Tudo|
|System. Collections. Generic. HashSet\<T>|Tudo|
|System. Collections. Generic. ICollection\<T>|Tudo|
|System. Collections. Generic. IDictionary<TKey, TValue>|Tudo|
|System.Collections.Generic.IEnumerable\<T>|Tudo|
|System. Collections. Generic. IEnumerator\<T>|Tudo|
|System.Collections.Generic.IList\<T>|Tudo|
|System. Collections. Generic. IReadOnlyCollection\<T>|Tudo|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Tudo|
|System. Collections. Generic. ISet\<T>|Tudo|
|System. Collections. Generic. KeyValuePair<TKey, TValue>|Tudo|
|System. coleções. Generic. List\<T>|Tudo|
|System. coleções. Generic. Queue\<T>|Tudo|
|System. coleções. Generic. Stack\<T>|Tudo|
|System.Convert|Tudo|
|System.DateTime|(Construtor), adicionar, AddDays, addhora, addmilésimos de segundo, addminutos, addmeses, addsegundos, addtiques, subyears, data, Day, DayOfWeek, DayOfYear, DaysInMonth, hour, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minute, MinValue, month, Now, Parse, Second, subtrair, Ticks, TimeOfDay, hoje, ToString, UtcNow|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Tudo|
|System.Decimal|Tudo|
|System.Double|Tudo|
|System.Exception|Tudo|
|System.Guid|Tudo|
|System.Int16|Tudo|
|System.Int32|Tudo|
|System.Int64|Tudo|
|System. IO. StringReader|Tudo|
|System. IO. StringWriter|Tudo|
|System. Linq. Enumerable|Tudo|
|System.Math|Tudo|
|System.MidpointRounding|Tudo|
|System.Net.WebUtility|Tudo|
|System.Nullable|Tudo|
|System.Random|Tudo|
|System.SByte|Tudo|
|System.Security.Cryptography.AsymmetricAlgorithm|Tudo|
|System. Security. Cryptography. CipherMode|Tudo|
|System.Security.Cryptography.HashAlgorithm|Tudo|
|System. Security. Cryptography. HashAlgorithmname|Tudo|
|System.Security.Cryptography.HMAC|Tudo|
|System.Security.Cryptography.HMACMD5|Tudo|
|System.Security.Cryptography.HMACSHA1|Tudo|
|System.Security.Cryptography.HMACSHA256|Tudo|
|System.Security.Cryptography.HMACSHA384|Tudo|
|System.Security.Cryptography.HMACSHA512|Tudo|
|System.Security.Cryptography.KeyedHashAlgorithm|Tudo|
|System.Security.Cryptography.MD5|Tudo|
|System. Security. Cryptography. Oid|Tudo|
|System. Security. Cryptography. PaddingMode|Tudo|
|System.Security.Cryptography.RNGCryptoServiceProvider|Tudo|
|System.Security.Cryptography.RSA|Tudo|
|System. Security. Cryptography. RSAEncryptionPadding|Tudo|
|System. Security. Cryptography. RSASignaturePadding|Tudo|
|System.Security.Cryptography.SHA1|Tudo|
|System.Security.Cryptography.SHA1Managed|Tudo|
|System.Security.Cryptography.SHA256|Tudo|
|System.Security.Cryptography.SHA256Managed|Tudo|
|System.Security.Cryptography.SHA384|Tudo|
|System.Security.Cryptography.SHA384Managed|Tudo|
|System.Security.Cryptography.SHA512|Tudo|
|System.Security.Cryptography.SHA512Managed|Tudo|
|System. Security. Cryptography. SymmetricAlgorithm|Tudo|
|System. Security. Cryptography. X509Certificates. PublicKey|Tudo|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Tudo|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Nome|
|System. Security. Cryptography. X509Certificates. X509Certificate|Tudo|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Tudo|
|System. Security. Cryptography. X509Certificates. X509ContentType|Tudo|
|System. Security. Cryptography. X509Certificates. X509NameType|Tudo|
|System.Single|Tudo|
|System.String|Tudo|
|System.StringComparer|Tudo|
|System.StringComparison|Tudo|
|System.StringSplitOptions|Tudo|
|System.Text.Encoding|Tudo|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Construtor), IsMatch, corresponder, corresponder, substituir, cancelar escape, dividir|
|System.Text.RegularExpressions.RegexOptions|Tudo|
|System.Text.StringBuilder|Tudo|
|System.TimeSpan|Tudo|
|System.TimeZone|Tudo|
|System. TimeZoneInfo. AdjustmentRule|Tudo|
|System. TimeZoneInfo. Transicionatime|Tudo|
|System.TimeZoneInfo|Tudo|
|System.Tuple|Tudo|
|System.UInt16|Tudo|
|System.UInt32|Tudo|
|System.UInt64|Tudo|
|System.Uri|Tudo|
|System.UriPartial|Tudo|
|System.Xml.Linq.Extensions|Tudo|
|System.Xml.Linq.XAttribute|Tudo|
|System.Xml.Linq.XCData|Tudo|
|System.Xml.Linq.XComment|Tudo|
|System.Xml.Linq.XContainer|Tudo|
|System.Xml.Linq.XDeclaration|Tudo|
|System.Xml.Linq.XDocument|Todos, exceto de: Load|
|System.Xml.Linq.XDocumentType|Tudo|
|System.Xml.Linq.XElement|Tudo|
|System.Xml.Linq.XName|Tudo|
|System.Xml.Linq.XNamespace|Tudo|
|System.Xml.Linq.XNode|Tudo|
|System.Xml.Linq.XNodeDocumentOrderComparer|Tudo|
|System.Xml.Linq.XNodeEqualityComparer|Tudo|
|System.Xml.Linq.XObject|Tudo|
|System.Xml.Linq.XProcessingInstruction|Tudo|
|System.Xml.Linq.XText|Tudo|
|System.Xml.XmlNodeType|Tudo|

## <a name="context-variable"></a><a name="ContextVariables"></a> Variável de contexto
Uma variável chamada `context` está implicitamente disponível em cada [expressão](api-management-policy-expressions.md#Syntax) de política. Seus membros fornecem informações pertinentes para `\request`. Todos os membros de `context` são somente leitura.

|Variável de contexto|Valores de métodos, propriedades e parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|contexto|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implantação](#ref-context-deployment)<br /><br /> Decorrido: TimeSpan - intervalo de tempo entre o valor de carimbo de data/hora e a hora atual<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operação](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Solicitação](#ref-context-request)<br /><br /> RequestId: Guid - identificador exclusivo de solicitação<br /><br /> [Resposta](#ref-context-response)<br /><br /> [Assinatura](#ref-context-subscription)<br /><br /> Carimbo de data/hora: DataHora - ponto no tempo em que a solicitação foi recebida<br /><br /> Rastreamento: bool - indica que se o rastreamento está ativado ou desativado <br /><br /> [Usuário](#ref-context-user)<br /><br /> [Variables](#ref-context-variables): IReadOnlyDictionary<string,> de objeto<br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Para obter mais informações sobre context.LastError, consulte [Error handling](api-management-error-handling-policies.md) (Tratamento de erro).|
|<a id="ref-context-operation"></a>context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|APIs: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|Corpo: [IMessageBody](#ref-imessagebody) ou `null` se a solicitação não tiver um corpo.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Cabeçalhos](#ref-context-request-headers): IReadOnlyDictionary<cadeia de caracteres, Cadeia de caracteres [] ><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de solicitação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-response"></a>context.Response|Corpo: [IMessageBody](#ref-imessagebody)<br /><br /> [Cabeçalhos](#ref-context-response-headers): IReadOnlyDictionary<cadeia de caracteres, Cadeia de caracteres [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedDate: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identidades: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T \> (preserveContent: bool = false): em que t: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são usados para a leitura dos corpos de mensagens de solicitação e resposta em um tipo `T` especificado. Por padrão, o método usa o fluxo do corpo de mensagem original e o torna indisponível após seu retorno. Para evitar que o método opere em uma cópia do fluxo do corpo, defina o parâmetro `preserveContent` como `true`. Clique [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [Consulta](#ref-iurl-query): IReadOnlyDictionary<String, String [] ><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de parâmetro de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|
|<a id="ref-context-variables"></a>Contexto T. Variables. GetValueOrDefault<T \> (VariableName: String, DefaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Retorna o valor da variável convertida para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método gerará uma exceção se o tipo especificado não corresponder ao tipo real da variável retornada.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de Autenticação Básica HTTP, o método retornará um objeto do tipo `BasicAuthCredentials`, caso contrário, o método retornará nulo.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de Autenticação Básica HTTP válido no cabeçalho de solicitação, o método retornará `true` e o parâmetro do resultado conterá um valor do tipo `BasicAuthCredentials`, caso contrário, o método retornará `false`.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará um objeto do tipo `Jwt`, caso contrário, o método retornará `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `Jwt`, caso contrário, o método retornará `false`.|
|Jwt|Algorithm: string<br /><br /> Audiência: IEnumerable<cadeia de caracteres\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de declaração separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – cyphertext a ser descriptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|
|bool VerifyNoRevocation (entrada: este System. Security. Cryptography. X509Certificates. X509Certificate2)|Executa uma validação de cadeia X. 509 sem verificar o status de revogação do certificado.<br /><br />objeto de entrada-certificado<br /><br />Retorna `true` se a validação for bem-sucedida; `false` se a validação falhar.|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](./api-management-policies.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](./policy-reference.md)
