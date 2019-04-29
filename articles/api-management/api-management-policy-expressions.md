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
ms.openlocfilehash: 90b2dfdbec0d6dc81a05b845832fda92fe36d98c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656584"
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

|Type|Membros com suporte|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Todos|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Todos|
|Newtonsoft.Json.Linq.JArray|Todos|
|Newtonsoft.Json.Linq.JConstructor|Todos|
|Newtonsoft.Json.Linq.JContainer|Todos|
|Newtonsoft.Json.Linq.JObject|Todos|
|Newtonsoft.Json.Linq.JProperty|Todos|
|Newtonsoft.Json.Linq.JRaw|Todos|
|Newtonsoft.Json.Linq.JToken|Todos|
|Newtonsoft.Json.Linq.JTokenType|Todos|
|Newtonsoft.Json.Linq.JValue|Todos|
|System.Array|Todos|
|System.BitConverter|Todos|
|System.Boolean|Todos|
|System.Byte|Todos|
|System.Char|Todos|
|System.Collections.Generic.Dictionary<TKey, TValue>|Todos|
|System.Collections.Generic.HashSet<T>|Todos|
|System.Collections.Generic.ICollection<T>|Todos|
|System.Collections.Generic.IDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.IEnumerable<T>|Todos|
|System.Collections.Generic.IEnumerator<T>|Todos|
|System.Collections.Generic.IList<T>|Todos|
|System.Collections.Generic.IReadOnlyCollection<T>|Todos|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Todos|
|System.Collections.Generic.ISet<T>|Todos|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Todos|
|System.Collections.Generic.List<T>|Todos|
|System.Collections.Generic.Queue<T>|Todos|
|System.Collections.Generic.Stack<T>|Todos|
|System.Convert|Todos|
|System.DateTime|(Construtor), adicionar, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, data, dia, DayOfWeek, DayOfYear, DaysInMonth, hora, IsDaylightSavingTime, IsLeapYear, MaxValue, milissegundo, minuto, MinValue, mês, agora , Análise, em segundo lugar, subtrair, tiques, TimeOfDay, hoje, ToString, UtcNow, ano|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Todos|
|System.Decimal|Todos|
|System.Double|Todos|
|System.Exception|Todos|
|System.Guid|Todos|
|System.Int16|Todos|
|System.Int32|Todos|
|System.Int64|Todos|
|System.IO.StringReader|Todos|
|System.IO.StringWriter|Todos|
|System.Linq.Enumerable|Todos|
|System.Math|Todos|
|System.MidpointRounding|Todos|
|System.Net.WebUtility|Todos|
|System.Nullable|Todos|
|System.Random|Todos|
|System.SByte|Todos|
|System.Security.Cryptography.AsymmetricAlgorithm|Todos|
|System.Security.Cryptography.CipherMode|Todos|
|System.Security.Cryptography.HashAlgorithm|Todos|
|System.Security.Cryptography.HashAlgorithmName|Todos|
|System.Security.Cryptography.HMAC|Todos|
|System.Security.Cryptography.HMACMD5|Todos|
|System.Security.Cryptography.HMACSHA1|Todos|
|System.Security.Cryptography.HMACSHA256|Todos|
|System.Security.Cryptography.HMACSHA384|Todos|
|System.Security.Cryptography.HMACSHA512|Todos|
|System.Security.Cryptography.KeyedHashAlgorithm|Todos|
|System.Security.Cryptography.MD5|Todos|
|System.Security.Cryptography.Oid|Todos|
|System.Security.Cryptography.PaddingMode|Todos|
|System.Security.Cryptography.RNGCryptoServiceProvider|Todos|
|System.Security.Cryptography.RSA|Todos|
|System.Security.Cryptography.RSAEncryptionPadding|Todos|
|System.Security.Cryptography.RSASignaturePadding|Todos|
|System.Security.Cryptography.SHA1|Todos|
|System.Security.Cryptography.SHA1Managed|Todos|
|System.Security.Cryptography.SHA256|Todos|
|System.Security.Cryptography.SHA256Managed|Todos|
|System.Security.Cryptography.SHA384|Todos|
|System.Security.Cryptography.SHA384Managed|Todos|
|System.Security.Cryptography.SHA512|Todos|
|System.Security.Cryptography.SHA512Managed|Todos|
|System.Security.Cryptography.SymmetricAlgorithm|Todos|
|System.Security.Cryptography.X509Certificates.PublicKey|Todos|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Todos|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|NOME|
|System.Security.Cryptography.X509Certificates.X509Certificate|Todos|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Todos|
|System.Security.Cryptography.X509Certificates.X509ContentType|Todos|
|System.Security.Cryptography.X509Certificates.X509NameType|Todos|
|System.Single|Todos|
|System.String|Todos|
|System.StringComparer|Todos|
|System.StringComparison|Todos|
|System.StringSplitOptions|Todos|
|System.Text.Encoding|Todos|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Construtor) IsMatch, correspondência, correspondências, substituir, Unescape, dividir|
|System.Text.RegularExpressions.RegexOptions|Todos|
|System.Text.StringBuilder|Todos|
|System.TimeSpan|Todos|
|System.TimeZone|Todos|
|System.TimeZoneInfo.AdjustmentRule|Todos|
|System.TimeZoneInfo.TransitionTime|Todos|
|System.TimeZoneInfo|Todos|
|System.Tuple|Todos|
|System.UInt16|Todos|
|System.UInt32|Todos|
|System.UInt64|Todos|
|System.Uri|Todos|
|System.UriPartial|Todos|
|System.Xml.Linq.Extensions|Todos|
|System.Xml.Linq.XAttribute|Todos|
|System.Xml.Linq.XCData|Todos|
|System.Xml.Linq.XComment|Todos|
|System.Xml.Linq.XContainer|Todos|
|System.Xml.Linq.XDeclaration|Todos|
|System.Xml.Linq.XDocument|Todos, exceto de: Carregar|
|System.Xml.Linq.XDocumentType|Todos|
|System.Xml.Linq.XElement|Todos|
|System.Xml.Linq.XName|Todos|
|System.Xml.Linq.XNamespace|Todos|
|System.Xml.Linq.XNode|Todos|
|System.Xml.Linq.XNodeDocumentOrderComparer|Todos|
|System.Xml.Linq.XNodeEqualityComparer|Todos|
|System.Xml.Linq.XObject|Todos|
|System.Xml.Linq.XProcessingInstruction|Todos|
|System.Xml.Linq.XText|Todos|
|System.Xml.XmlNodeType|Todos|

## <a name="ContextVariables"></a> Variável de contexto
Uma variável chamada `context` está implicitamente disponível em toda [expressão](api-management-policy-expressions.md#Syntax) de política. Seus membros fornecem informações pertinentes para `\request`. Todos os membros de `context` são somente leitura.

|Variável de contexto|Valores de métodos, propriedades e parâmetros permitidos|
|----------------------|-------------------------------------------------------|
|context|API: IApi<br /><br /> Implantação<br /><br /> Decorrido: TimeSpan – intervalo de tempo entre o valor de carimbo de data/hora e a hora atual<br /><br /> lastError<br /><br /> Operação<br /><br /> Produto<br /><br /> Solicitação<br /><br /> RequestId: Guid – identificador exclusivo de solicitação<br /><br /> Response<br /><br /> Assinatura<br /><br /> Carimbo de data/hora: DateTime – ponto no tempo em que a solicitação foi recebida<br /><br /> Rastreamento: bool - indica que se o rastreamento está ativado ou desativado <br /><br /> Usuário<br /><br /> Variáveis: IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: IUrl<br /><br /> Version: string |
|context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificados: IReadOnlyDictionary<string, X509Certificate2>|
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Para obter mais informações sobre context.LastError, consulte [Error handling](api-management-error-handling-policies.md) (Tratamento de erro).|
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|context.Product|APIs: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Grupos: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|context.Request|Corpo: IMessageBody<br /><br /> Certificado: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Cabeçalhos: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> URL: IUrl|
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de solicitação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|context.Response|Corpo: IMessageBody<br /><br /> Cabeçalhos: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Grupos: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identidades: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocolos: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|Id: string<br /><br /> Name: string|
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são usados para a leitura dos corpos de mensagens de solicitação e resposta em um tipo `T` especificado. Por padrão, o método usa o fluxo do corpo de mensagem original e o torna indisponível após seu retorno. Para evitar que o método opere em uma cópia do fluxo do corpo, defina o parâmetro `preserveContent` como `true`. Clique [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Consulta: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|IUserIdentity|Id: string<br /><br /> Provider: string|
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de parâmetro de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Retorna o valor da variável convertida para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método gerará uma exceção se o tipo especificado não corresponder ao tipo real da variável retornada.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de Autenticação Básica HTTP, o método retornará um objeto do tipo `BasicAuthCredentials`, caso contrário, o método retornará nulo.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor de autorização de Autenticação Básica HTTP válido no cabeçalho de solicitação, o método retornará `true` e o parâmetro do resultado conterá um valor do tipo `BasicAuthCredentials`, caso contrário, o método retornará `false`.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará um objeto do tipo `Jwt`, caso contrário, o método retornará `null`.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `Jwt`, caso contrário, o método retornará `false`.|
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de declaração separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – texto sem formatação a ser criptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna texto sem formatação criptografado.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input – cyphertext a ser descriptografado<br /><br />alg – nome de um algoritmo de criptografia simétrico<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />Retorna o texto sem formatação.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input – input – cyphertext a ser descriptografado<br /><br />alg – algoritmo de criptografia<br /><br />key – chave de criptografia<br /><br />iv – vetor de inicialização<br /><br />Retorna o texto sem formatação.|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
