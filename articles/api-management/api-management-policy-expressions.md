---
title: "Expressões de política no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba mais sobre expressões de política no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 33bcc51466fa0918bf4484c58fac813d07ae14da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-policy-expressions"></a>Expressões de política de Gerenciamento de API
A sintaxe de expressões de política é C# 6.0. Cada expressão tem acesso à variável de [contexto](api-management-policy-expressions.md#ContextVariables) fornecida implicitamente e a um [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos do .NET Framework.  
  
> [!NOTE]
>  Para obter mais informações sobre expressões de política, consulte o vídeo [Policy Expressions](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/) (Expressões de política).  
>   
>  Para demonstrações da configuração de políticas usando expressões de política, consulte [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Abordagem da Nuvem Episódio 177: Mais Recursos de Gerenciamento de API com Vlad Vinogradsky). Este vídeo contém as seguintes demonstrações de expressão de política.  
>   
>  -   10:30 – veja como aplicar a política no nível da API para fornecer informações de contexto ao serviço de back-end usando as políticas [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) e [Set HTTP header](api-management-transformation-policies.md#SetHTTPheader) (Definir cabeçalho HTTP). Em 12:10, há uma demonstração de chamada de uma operação no portal do desenvolvedor na qual você pode ver essas políticas em ação.  
> -   13:50 – veja como usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar o acesso a operações baseadas em declarações de token. Avance para 15:00 para ver as diretivas configuradas no editor de diretiva e, em seguida, 18:50 para uma demonstração de como chamar uma operação do portal do desenvolvedor com e sem o token de autorização necessário.  
> -   21:00 – veja como usar um rastreamento de [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver como as políticas são avaliadas e os resultados das avaliações.  
> -   25:25 – veja como usar expressões de política com as políticas [Obter do cache](api-management-caching-policies.md#GetFromCache) e [Store to cache](api-management-caching-policies.md#StoreToCache) (Armazenar no cache) para configurar a duração do cache de resposta do Gerenciamento de API que corresponde ao cache de resposta do serviço de back-end, conforme especificado pela diretiva `Cache-Control` do serviço de backup.  
> -   34:30 – veja como executar a filtragem de conteúdo removendo elementos de dados da resposta recebida do serviço de back-end ao usar as políticas [Controlar fluxo](api-management-advanced-policies.md#choose) e [definir corpo](api-management-transformation-policies.md#SetBody). Inicie em 31:50 para uma visão geral da [API da Previsão de Céu Escuro](https://developer.forecast.io/) usada para esta demonstração.  
> -   Para baixar as instruções de política usadas neste vídeo, consulte o repositório GitHub [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).  
  
  
##  <a name="Syntax"></a> Sintaxe  
 Expressões de instrução única são colocadas em `@(expression)`, em que `expression` é uma instrução de expressão C# bem formada.  
  
 Expressões de várias instruções são colocadas em `@{expression}`. Todos os caminhos de código dentro de expressões de várias instruções devem terminar com uma instrução `return`.  
  
##  <a name="PolicyExpressionsExamples"></a> Exemplos  
  
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
  
##  <a name="PolicyExpressionsUsage"></a> Uso  
 Expressões podem ser usadas como valores de atributo ou texto em qualquer uma das [políticas](api-management-policies.md) de Gerenciamento de API, a menos que a referência de política especifique o contrário.  
  
> [!IMPORTANT]
>  Observe que quando você usa expressões de política, há apenas uma verificação limitada de expressões de política quando a política é definida. Como as expressões são executadas em tempo de execução no pipeline de entrada ou saída pelo gateway, quaisquer exceções de tempo de execução geradas por expressões de política resultarão em um erro em tempo de execução na chamada à API.  
  
##  <a name="CLRTypes"></a> Tipos do .NET framework permitidos em expressões de política  
 A tabela a seguir lista os tipos do .NET Framework e seus membros permitidos em expressões de política.  
  
|Tipo CLR|Métodos com suporte|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JArray|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JConstructor|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JContainer|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JObject|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JProperty|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JRaw|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JToken|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JTokenType|Há suporte para todos os métodos|  
|Newtonsoft.Json.Linq.JValue|Há suporte para todos os métodos|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Todos|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Todos|  
|System.Collections.Generic.ISet<TKey, TValue>|Todos|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value|  
|System.Collections.Generic.List<TKey, TValue>|Todos|  
|System.Collections.Generic.Queue<TKey, TValue>|Todos|  
|System.Collections.Generic.Stack<TKey, TValue>|Todos|  
|System.Convert|Todos|  
|System.DateTime|Todos|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|Todos|  
|System.Decimal|Todos|  
|System.Double|Todos|  
|System.Guid|Todos|  
|System.IEnumerable<T\>|Todos|  
|System.IEnumerator<T\>|Todos|  
|System.Int16|Todos|  
|System.Int32|Todos|  
|System.Int64|Todos|  
|System.Linq.Enumerable<T\>|Há suporte para todos os métodos|  
|System.Math|Todos|  
|System.MidpointRounding|Todos|  
|System.Nullable<T\>|Todos|  
|System.Random|Todos|  
|System.SByte|Todos|  
|System.Security.Cryptography. HMACSHA384|Todos|  
|System.Security.Cryptography. HMACSHA512|Todos|  
|System.Security.Cryptography.HashAlgorithm|Todos|  
|System.Security.Cryptography.HMAC|Todos|  
|System.Security.Cryptography.HMACMD5|Todos|  
|System.Security.Cryptography.HMACSHA1|Todos|  
|System.Security.Cryptography.HMACSHA256|Todos|  
|System.Security.Cryptography.KeyedHashAlgorithm|Todos|  
|System.Security.Cryptography.MD5|Todos|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Todos|  
|System.Security.Cryptography.SHA1|Todos|  
|System.Security.Cryptography.SHA1Managed|Todos|  
|System.Security.Cryptography.SHA256|Todos|  
|System.Security.Cryptography.SHA256Managed|Todos|  
|System.Security.Cryptography.SHA384|Todos|  
|System.Security.Cryptography.SHA384Managed|Todos|  
|System.Security.Cryptography.SHA512|Todos|  
|System.Security.Cryptography.SHA512Managed|Todos|  
|System.Single|Todos|  
|System.String|Todos|  
|System.StringSplitOptions|Todos|  
|System.Text.Encoding|Todos|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Todos|  
|System.Tuple|Todos|  
|System.UInt16|Todos|  
|System.UInt32|Todos|  
|System.UInt64|Todos|  
|System.Uri|Todos|  
|System.Xml.Linq.Extensions|Há suporte para todos os métodos|  
|System.Xml.Linq.XAttribute|Há suporte para todos os métodos|  
|System.Xml.Linq.XCData|Há suporte para todos os métodos|  
|System.Xml.Linq.XComment|Há suporte para todos os métodos|  
|System.Xml.Linq.XContainer|Há suporte para todos os métodos|  
|System.Xml.Linq.XDeclaration|Há suporte para todos os métodos|  
|System.Xml.Linq.XDocument|Há suporte para todos os métodos|  
|System.Xml.Linq.XDocumentType|Há suporte para todos os métodos|  
|System.Xml.Linq.XElement|Há suporte para todos os métodos|  
|System.Xml.Linq.XName|Há suporte para todos os métodos|  
|System.Xml.Linq.XNamespace|Há suporte para todos os métodos|  
|System.Xml.Linq.XNode|Há suporte para todos os métodos|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Há suporte para todos os métodos|  
|System.Xml.Linq.XNodeEqualityComparer|Há suporte para todos os métodos|  
|System.Xml.Linq.XObject|Há suporte para todos os métodos|  
|System.Xml.Linq.XProcessingInstruction|Há suporte para todos os métodos|  
|System.Xml.Linq.XText|Há suporte para todos os métodos|  
|System.Xml.XmlNodeType|Todos|  
  
##  <a name="ContextVariables"></a> Variável de contexto  
 Uma variável chamada `context` está implicitamente disponível em toda [expressão](api-management-policy-expressions.md#Syntax) de política. Seus membros fornecem informações pertinentes para `\request`. Todos os membros de `context` são somente leitura.  
  
|Variável de contexto|Valores de métodos, propriedades e parâmetros permitidos|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Implantação<br /><br /> LastError<br /><br /> Operação<br /><br /> Produto<br /><br /> Solicitação<br /><br /> RequestId: Guid<br /><br /> Resposta<br /><br /> Assinatura<br /><br /> Tracing: bool<br /><br /> Usuário<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: string<br /><br /> ServiceName: string|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Para obter mais informações sobre context.LastError, consulte [Error handling](api-management-error-handling-policies.md) (Tratamento de erro).|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de solicitação separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de cabeçalho de resposta separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): em que T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Os métodos `context.Request.Body.As<T>` e `context.Response.Body.As<T>` são usados para a leitura dos corpos de mensagens de solicitação e resposta em um tipo `T` especificado. Por padrão, o método usa o fluxo do corpo de mensagem original e o torna indisponível após seu retorno. Para evitar que o método opere em uma cópia do fluxo do corpo, defina o parâmetro `preserveContent` como `true`. Clique [aqui](api-management-transformation-policies.md#SetBody) para ver um exemplo.|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de parâmetro de consulta separados por vírgula ou `defaultValue` se o parâmetro não for encontrado.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Retorna o valor da variável convertida para o tipo `T` ou `defaultValue` se a variável não for encontrada.<br /><br /> Esse método gerará uma exceção se o tipo especificado não corresponder ao tipo real da variável retornada.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de Autenticação Básica HTTP, o método retornará um objeto do tipo `BasicAuthCredentials`, caso contrário, o método retornará nulo.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Se o parâmetro de entrada contiver um valor válido de cabeçalho de solicitação de autorização de Autenticação Básica HTTP, o método retornará `true` e o parâmetro do resultado conterá um valor do tipo `BasicAuthCredentials`, caso contrário, o método retornará `false`.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará um objeto do tipo `Jwt`, caso contrário, o método retornará `null`.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Se o parâmetro de entrada contiver um valor válido de token JWT, o método retornará `true` e o parâmetro de resultado conterá um valor do tipo `Jwt`, caso contrário, o método retornará `false`.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Retorna valores de declaração separados por vírgula ou `defaultValue` se o cabeçalho não for encontrado.|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com políticas, veja [Políticas em Gerenciamento de API](api-management-howto-policies.md).  
