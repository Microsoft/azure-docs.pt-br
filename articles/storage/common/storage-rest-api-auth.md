---
title: Chamar operações da API REST com autorização de chave compartilhada
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Use a API REST do armazenamento do Azure para fazer uma solicitação para o armazenamento de BLOBs usando a autorização de chave compartilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010895"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Chamar operações da API REST com autorização de chave compartilhada

Este artigo mostra como chamar as APIs REST do armazenamento do Azure, incluindo como formar o cabeçalho de autorização. Ele é escrito a partir do ponto de vista de um desenvolvedor que não conhece nada sobre REST e sem ideia de como fazer uma chamada REST. Depois de aprender a chamar uma operação REST, você pode aproveitar esse conhecimento para usar qualquer outra operação REST do armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

O aplicativo de exemplo lista os contêineres de BLOB para uma conta de armazenamento. Para testar o código neste artigo, você precisa dos seguintes itens:

- Instale o [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho de **Desenvolvimento do Azure**.

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento de uso geral. Se você ainda não tiver uma conta de armazenamento, consulte o artigo [Criar uma conta de armazenamento](storage-account-create.md).

- O exemplo neste artigo mostra como listar os contêineres em uma conta de armazenamento. Para ver a saída, adicione alguns contêineres de armazenamento de blobs na conta de armazenamento antes de iniciar.

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O aplicativo de exemplo é um aplicativo de console escrito em C#.

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a solução do Visual Studio, procure a pasta storage-dotnet-rest-api-with-auth, abra-a e clique duas vezes em StorageRestApiAuth.sln.

## <a name="about-rest"></a>Sobre o REST

REST significa *transferência de estado de reapresentação*. Para obter uma definição específica, confira a[Wikipédia](https://en.wikipedia.org/wiki/Representational_state_transfer).

O REST é uma arquitetura que permite interagir com um serviço por meio de um protocolo de Internet, como HTTP/HTTPS. O REST é independente do software em execução no servidor ou no cliente. A API REST pode ser chamada de qualquer plataforma que ofereça suporte a HTTP/HTTPS. Você pode escrever um aplicativo que é executado em Mac, Windows, Linux, telefone Android ou tablet, iPhone, iPod ou site da Web, e usar a mesma API REST para todas as plataformas.

Uma chamada para a API REST consiste em uma solicitação, que é feita pelo cliente e uma resposta, que é retornada pelo serviço. Na solicitação, você envia uma URL com informações sobre qual operação você deseja chamar, o recurso para agir, quaisquer parâmetros de consulta e cabeçalhos, e dependendo da operação que foi chamada, uma carga de dados. A resposta do serviço inclui um código de status, um conjunto de cabeçalhos de resposta e, dependendo da operação que foi chamada, uma carga de dados.

## <a name="about-the-sample-application"></a>Sobre o aplicativo de exemplo

O aplicativo de exemplo lista os contêineres em uma conta de armazenamento. Depois de compreender como as informações na documentação da API REST se correlacionam com o código real, outras chamadas REST serão mais fáceis de entender.

Se você observar a [API REST do serviço Blob](/rest/api/storageservices/Blob-Service-REST-API), verá todas as operações que pode executar no armazenamento de blobs. As bibliotecas de cliente de armazenamento são wrappers em torno das APIs REST: elas facilitam o acesso ao armazenamento sem usar as APIs REST diretamente. Mas, conforme observado acima, às vezes você deseja usar a API REST em vez de uma biblioteca de cliente de armazenamento.

## <a name="list-containers-operation"></a>Operação de listar contêineres

Examine a referência para a operação [ListContainers](/rest/api/storageservices/List-Containers2) . Essas informações ajudarão você a entender onde vêm alguns dos campos na solicitação e na resposta.

**Método de solicitação**: Get. Esse verbo é o método HTTP que você especifica como uma propriedade do objeto de solicitação. Outros valores para esse verbo incluem HEAD, PUT e DELETE, dependendo da API chamada.

**URI da solicitação**: `https://myaccount.blob.core.windows.net/?comp=list` .O URI de solicitação é criado do ponto de extremidade da conta de armazenamento de BLOBs `https://myaccount.blob.core.windows.net` e da cadeia de caracteres do recurso `/?comp=list` .

[Parâmetros de URI](/rest/api/storageservices/List-Containers2#uri-parameters): há parâmetros de consulta adicionais que você pode usar ao chamar ListContainers. Alguns desses parâmetros são *timeout* para a chamada (em segundos) e *prefix*, que é usado para filtragem.

Outro parâmetro útil é *maxresults:* se houver mais contêineres que esse valor, o corpo da resposta conterá um *NextMarker*, que indica o próximo contêiner a ser retornado na próxima solicitação. Para usar esse recurso, você deve fornecer o valor *NextMarker* como o parâmetro *marker* no URI ao fazer a próxima solicitação. Ao usar esse recurso, ele é análogo à paginação de resultados.

Para usar outros parâmetros, acrescente-os à cadeia de caracteres do recurso com o valor, como neste exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Cabeçalhos de solicitação](/rest/api/storageservices/List-Containers2#request-headers)**:** essa seção lista os cabeçalhos de solicitação obrigatórios e opcionais. Três dos cabeçalhos são obrigatórios: um cabeçalho de *Autorização*, *x-ms-date* (contém a hora de UTC para a solicitação) e *x-ms-version* (especifica a versão da API REST a ser usada). A inclusão de *x-ms-client-request-id* nos cabeçalhos é opcional; você pode definir o valor para esse campo como qualquer coisa; ele é gravado nos logs de análise de armazenamento quando o registro em log está habilitado.

[Corpo da solicitação](/rest/api/storageservices/List-Containers2#request-body)**:** não há nenhum corpo de solicitação para ListContainers. O corpo da solicitação é usado em todas as operações PUT ao carregar blobs, bem como SetContainerAccessPolicy, que permite que você envie em uma lista XML de políticas de acesso armazenadas para aplicar. As políticas de acesso armazenadas são discutidas no artigo [Usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).

[Código de status da resposta](/rest/api/storageservices/List-Containers2#status-code)**:** indica os códigos de status que você precisa saber. Neste exemplo, um código de status HTTP 200 é OK. Para obter uma lista completa de códigos de status HTTP, confira [Definições de código de status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Para ver os códigos de erro específicos das APIs REST do Armazenamento, confira [Códigos de erro da API REST comuns](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/List-Containers2#response-headers)**:** eles incluem *tipo de conteúdo*; *x-MS-Request-ID*, que é a ID da solicitação que você passou; *x-MS-Version*, que indica a versão do serviço blob usado; e a *Data*, que está em UTC e informa a hora em que a solicitação foi feita.

[Corpo da resposta](/rest/api/storageservices/List-Containers2#response-body): esse campo é uma estrutura XML que fornece os dados solicitados. Neste exemplo, a resposta é uma lista de contêineres e suas propriedades.

## <a name="creating-the-rest-request"></a>Criando a solicitação REST

Para segurança ao executar em produção, sempre use HTTPS em vez de HTTP. Para os fins deste exercício, você deve usar HTTP para poder exibir os dados de solicitação e resposta. Para exibir as informações de solicitação e resposta nas chamadas REST reais, você pode baixar o [Fiddler](https://www.telerik.com/fiddler) ou um aplicativo semelhante. Na solução do Visual Studio, o nome da conta de armazenamento e a chave são codificados na classe. O método ListContainersAsyncREST passa o nome da conta de armazenamento e a chave da conta de armazenamento para os métodos usados para criar os vários componentes da solicitação REST. Em um aplicativo do mundo real, o nome da conta de armazenamento e a chave devem residir em um arquivo de configuração, em variáveis de ambiente ou ser recuperados de um Azure Key Vault.

Em nosso projeto de exemplo, o código para criar o cabeçalho de autorização está em uma classe separada. A ideia é que você poderia pegar a classe inteira e adicioná-la à sua própria solução e usá-la "como está". O código de cabeçalho de autorização funciona para a maioria das chamadas da API REST ao Armazenamento do Azure.

Para criar a solicitação, que é um objeto HttpRequestMessage, vá para ListContainersAsyncREST em Program.cs. As etapas para criar a solicitação são:

- Crie o URI a ser usado para chamar o serviço.
- Crie o objeto HttpRequestMessage e defina a carga. A carga é nula para ListContainersAsyncREST porque não estamos transmitindo nada.
- Adicione os cabeçalhos de solicitação para x-ms-date e x-ms-version.
- Obtenha o cabeçalho de autorização e adicione-o.

Algumas informações básicas de que você precisa:

- Para ListContainers, o **método** é `GET`. Esse valor é definido ao instanciar a solicitação.
- O **recurso** é a parte de consulta do URI que indica qual API está sendo chamada e, portanto, o valor é `/?comp=list`. Conforme observado anteriormente, o recurso está na página de documentação de referência que mostra as informações sobre a [API ListContainers](/rest/api/storageservices/List-Containers2).
- O URI é construído com a criação do ponto de extremidade do serviço Blob para essa conta de armazenamento e a concatenação do recurso. O valor da **URI de solicitação** acaba sendo `http://contosorest.blob.core.windows.net/?comp=list`.
- Para ListContainers, **requestBody** é nulo e não há **cabeçalhos** extra.

Outras APIs podem ter outros parâmetros para passar, como *ifMatch*. Um exemplo em que você pode usar ifMatch é ao chamar PutBlob. Nesse caso, você define ifMatch para uma eTag e ela atualiza o blob somente se a eTag fornecida corresponder à eTag atual no blob. Se alguém tiver atualizado o blob desde a recuperação da eTag, suas alterações não serão substituídas.

Primeiro, defina o `uri` e a `payload`.

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, crie uma instância a solicitação, defina o método como `GET` e forneça o URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de solicitação para `x-ms-date` e `x-ms-version` . Esse local no código também é onde você pode adicionar outros cabeçalhos de solicitação obrigatórios para a chamada. Neste exemplo, não há nenhum cabeçalho adicional. Um exemplo de uma API que passa em cabeçalhos extras é a operação definir ACL de contêiner. Essa chamada à API adiciona um cabeçalho chamado "x-ms-blob-Public-Access" e o valor para o nível de acesso.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Chame o método que cria o cabeçalho de autorização e adicione-o aos cabeçalhos de solicitação. Você verá como criar o cabeçalho de autorização mais adiante neste artigo. O nome do método é GetAuthorizationHeader, que você pode ver neste snippet de código:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Aqui, `httpRequestMessage` contém a solicitação REST com os cabeçalhos de autorização.

## <a name="send-the-request"></a>Enviar a solicitação

Agora que você construiu a solicitação, você pode chamar o método SendAsync para enviá-la ao armazenamento do Azure. Verifique se o valor do código de status de resposta é 200, o que significa que a operação foi bem-sucedida. Em seguida, analise a resposta. Nesse caso, você pode obter uma lista XML de contêineres. Vamos examinar o código para chamar o método GetRESTRequest a fim de criar a solicitação, executar a solicitação e examinar a resposta da lista de contêineres.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Se você executar um rastreador de rede, como o [Fiddler](https://www.telerik.com/fiddler), ao fazer a chamada a SendAsync, poderá ver as informações de solicitação e resposta. Vamos conferir isso. O nome da conta de armazenamento é *contosorest*.

**Solicitação:**

```
GET /?comp=list HTTP/1.1
```

**Cabeçalhos de solicitação:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Cabeçalhos de resposta e código de status retornado após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corpo da resposta (XML):** Para a operação listar contêineres, isso mostra a lista de contêineres e suas propriedades.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Agora que você entende como criar a solicitação, chame o serviço e analise os resultados, vamos ver como criar o cabeçalho de autorização. A criação desse cabeçalho é complicada, mas a boa notícia é que, quando o código estiver funcionando, ele funcionará para todas as APIs REST do Serviço de Armazenamento.

## <a name="creating-the-authorization-header"></a>Criando o cabeçalho de autorização

> [!TIP]
> O armazenamento do Azure agora dá suporte à integração do Azure Active Directory (Azure AD) para BLOBs e filas. O Azure AD oferece uma experiência mais simples para autorizar uma solicitação para o Armazenamento do Azure. Para obter mais informações sobre como usar o Azure AD para autorizar operações REST, consulte [autorizar com Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). Para obter uma visão geral da integração do Azure AD com o armazenamento do Azure, consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](storage-auth-aad.md).

Há um artigo que explica conceitualmente (sem código) como [autorizar solicitações para o armazenamento do Azure](/rest/api/storageservices/authorize-requests-to-azure-storage).

Vamos ver o artigo até exatamente o for é necessário e mostrar o código.

Primeiro, use a autorização de chave compartilhada. O formato de cabeçalho de autorização tem esta aparência:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

O campo de assinatura é um HMAC (Hash-based Message Authentication Code) criado a partir da solicitação, calculado usando o algoritmo SHA256 e codificado usando a codificação de Base64. Entendeu? (Calma, você ainda nem ouviu a palavra *canonizado*.)

Este snippet de código mostra o formato da cadeia de caracteres de assinatura de chave compartilhada:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

A maioria desses campos é usada raramente. Para o armazenamento de Blobs, você pode especificar VERBO, md5, tamanho do conteúdo, cabeçalhos canonizados e recurso canonizado. Você pode deixar os outros em branco (mas colocar `\n` para que ele saiba que estão em branco).

O que são CanonicalizedHeaders e CanonicalizedResource? Boa pergunta. Na verdade, o que canonizado significa? Não é uma palavra comum nesse contexto. Aqui está o que a [Wikipédia diz (em inglês) sobre a canonização](https://en.wikipedia.org/wiki/Canonicalization): * Na ciência da computação, a canonização (às vezes, padronização ou normalização) é um processo de conversão de dados que tem mais de uma possível representação em forma canônica, "normal" ou "padrão".* Em linguagem normal, isso significa fazer a lista de itens (como cabeçalhos no caso de cabeçalhos canonizados) e padronizá-los em um formato obrigatório. Basicamente, a Microsoft decidiu-se por um formato e você precisa corresponder a ele.

Vamos começar com esses dois campos canonizados, pois eles são necessários para criar o cabeçalho de autorização.

### <a name="canonicalized-headers"></a>Cabeçalhos canônicos

Para criar esse valor, recupere os cabeçalhos que começam com "x-ms-", classifique-os e formate-os em uma cadeia de caracteres de instâncias `[key:value\n]` concatenadas em uma cadeia de caracteres. Neste exemplo, os cabeçalhos canonizados têm esta aparência:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Aqui está o código usado para criar essa saída:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Recurso canônico

Esta parte da cadeia de caracteres de assinatura representa a conta de armazenamento que é o destino da solicitação. Lembre-se de que o URI da solicitação é `<http://contosorest.blob.core.windows.net/?comp=list>`, com o nome da conta real (`contosorest` nesse caso). Neste exemplo, isto é retornado:

```
/contosorest/\ncomp:list
```

Se você tiver parâmetros de consulta, este exemplo também incluirá esses parâmetros. Aqui está o código, que também lida com parâmetros de consulta adicionais e parâmetros de consulta com vários valores. Lembre-se de que você está criando esse código para funcionar para todas as APIs REST. Você deseja incluir todas as possibilidades, mesmo que o método ListContainers não precise de todas elas.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Agora que as cadeias de caracteres canonizadas foram definidas, vamos dar uma olhada em como criar o cabeçalho de autorização. Comece criando uma cadeia de caracteres da assinatura de mensagem no formato StringToSign exibido anteriormente neste artigo. Esse conceito é mais fácil de explicar usando os comentários no código e, portanto, este é o método final que retorna o cabeçalho de autorização:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

Quando você executa esse código, o MessageSignature resultante é semelhante a este exemplo:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Aqui está o valor final para AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O AuthorizationHeader é o último cabeçalho colocado nos cabeçalhos de solicitação antes de enviar a resposta.

Isso abrange tudo o que você precisa saber para reunir uma classe com a qual você pode criar uma solicitação para chamar as APIs REST dos serviços de armazenamento.

## <a name="example-list-blobs"></a>Exemplo: listar BLOBs

Vejamos como alterar o código para chamar a operação listar BLOBs para contêiner de contêiner *-1*. Esse código é quase idêntico ao código para listar contêineres, as únicas diferenças são o URI e como você analisa a resposta.

Se você examinar a documentação de referência de [ListBlobs](/rest/api/storageservices/List-Blobs), verá que o método é *obter* e o RequestURI é:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Em ListContainersAsyncREST, altere o código que define o URI para a API de ListBlobs. O nome do contêiner é **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Onde você manipula a resposta, altere o código para procurar os blobs em vez dos contêineres.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Quando você executar este exemplo, poderá obter resultados semelhantes ao seguinte:

**Cabeçalhos canônicos:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Recurso canônico:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Assinatura da mensagem:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Cabeçalho de autorização:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Os valores a seguir são do [Fiddler](https://www.telerik.com/fiddler):

**Solicitação:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Cabeçalhos de solicitação:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Cabeçalhos de resposta e código de status retornado após a execução:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corpo da resposta (XML):** a resposta XML isso mostra a lista de blobs e suas propriedades.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu como fazer uma solicitação para a API REST do armazenamento de BLOBs. Com a solicitação, você pode recuperar uma lista de contêineres ou uma lista de BLOBs em um contêiner. Você aprendeu como criar a assinatura de autorização para a chamada à API REST e como usá-la na solicitação REST. Por fim, você aprendeu a examinar a resposta.

## <a name="next-steps"></a>Próximas etapas

- [API REST do serviço Blob](/rest/api/storageservices/blob-service-rest-api)
- [API REST do serviço Arquivo](/rest/api/storageservices/file-service-rest-api)
- [API REST do serviço Fila](/rest/api/storageservices/queue-service-rest-api)
- [API REST de Serviço Tabela](/rest/api/storageservices/table-service-rest-api)
