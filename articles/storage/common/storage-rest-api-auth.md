---
title: Chamar operações de API REST do serviço Armazenamento do Azure, incluindo autenticação | Microsoft Docs
description: Chamar operações de API REST do serviço Armazenamento do Azure, incluindo autenticação
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9f6698eebf184d1df80920b7779512e2fda83a0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729334"
---
# <a name="using-the-azure-storage-rest-api"></a>Usando a API REST de Armazenamento do Azure

Este artigo mostra como usar as APIs REST do serviço Armazenamento de Blobs e como autenticar a chamada ao serviço. Ele foi escrito do ponto de vista de alguém que não sabe nada sobre REST e não sabe como fazer uma chamada REST, mas é um desenvolvedor. Vamos examinar a documentação de referência para uma chamada REST e ver como transformá-la em uma chamada REST real; quais campos vão onde? Depois de aprender a configurar uma chamada REST, você poderá aproveitar esse conhecimento para usar outras APIs REST do serviço de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos 

O aplicativo lista os contêineres no armazenamento de blobs de uma conta de armazenamento. Para testar o código neste artigo, você precisa dos seguintes itens: 

* Instale o [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho a seguir:
    - Desenvolvimento do Azure

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Uma conta de armazenamento de uso geral. Se você ainda não tiver uma conta de armazenamento, consulte o artigo [Criar uma conta de armazenamento](storage-quickstart-create-account.md).

* O exemplo neste artigo mostra como listar os contêineres em uma conta de armazenamento. Para ver a saída, adicione alguns contêineres de armazenamento de blobs na conta de armazenamento antes de iniciar.

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O aplicativo de exemplo é um aplicativo de console escrito em C#.

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a solução do Visual Studio, procure a pasta storage-dotnet-rest-api-with-auth, abra-a e clique duas vezes em StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>O que é REST?

REST significa *Transferência de Estado Representacional*. Para obter uma definição específica, confira a[Wikipédia](https://en.wikipedia.org/wiki/Representational_state_transfer).

Basicamente, REST é uma arquitetura que pode ser usada a fim de chamar APIs ou disponibilizar APIs para chamadas. Ela independe do que está acontecendo em ambos os lados e de qual outro software está sendo usado ao enviar ou receber chamadas REST. Você pode escrever um aplicativo que é executado em Mac, Windows, Linux, telefone Android ou tablet, iPhone, iPod ou site da Web, e usar a mesma API REST para todas as plataformas. Dados podem ser passados de e/ou para quando a API REST é chamada. A API REST não se importa com o nome da plataforma chamada; o importante é a informação passada na solicitação e os dados fornecidos na resposta.

Saber como usar REST é uma técnica útil. A equipe de produto do Azure lança de novos recursos frequentemente. Muitas vezes, os novos recursos podem ser acessados por meio da interface REST, mas ainda não foram expostos a **todas** as bibliotecas de cliente de armazenamento ou à interface do usuário (como o portal do Azure). Se você quiser sempre usar o melhor e mais recente, aprender REST é um requisito. Além disso, se você quiser codificar sua própria biblioteca para interagir com o Armazenamento do Azure ou para acessar o Armazenamento do Azure com uma linguagem de programação que não tem uma biblioteca de cliente de armazenamento ou SDK, poderá usar a API REST.

## <a name="about-the-sample-application"></a>Sobre o aplicativo de exemplo

O aplicativo de exemplo lista os contêineres em uma conta de armazenamento. Depois de compreender como as informações na documentação da API REST se correlacionam com o código real, outras chamadas REST serão mais fáceis de entender. 

Se você observar a [API REST do serviço Blob](/rest/api/storageservices/Blob-Service-REST-API), verá todas as operações que pode executar no armazenamento de blobs. As bibliotecas de cliente de armazenamento são wrappers em torno das APIs REST: elas facilitam o acesso ao armazenamento sem usar as APIs REST diretamente. Mas, conforme observado acima, às vezes você deseja usar a API REST em vez de uma biblioteca de cliente de armazenamento.

## <a name="rest-api-reference-list-containers-api"></a>Referência da API REST: API de lista de contêineres

Vamos examinar a página de referência da API REST para a operação [ListContainers](/rest/api/storageservices/List-Containers2) a fim de compreender de onde alguns dos campos vêm na solicitação e na resposta na próxima seção com o código.

**Método de solicitação**: GET. Esse verbo é o método HTTP que você especifica como uma propriedade do objeto de solicitação. Outros valores para esse verbo incluem HEAD, PUT e DELETE, dependendo da API chamada.

**URI de solicitação**: https://myaccount.blob.core.windows.net/?comp=list Isso é criado no ponto de extremidade da conta de Armazenamento de Blobs `http://myaccount.blob.core.windows.net` e da cadeia de caracteres do recurso `/?comp=list`.

[Parâmetros de URI](/rest/api/storageservices/List-Containers2#uri-parameters): Há parâmetros de consulta adicionais que você pode usar ao chamar ListContainers. Alguns desses parâmetros são *timeout* para a chamada (em segundos) e *prefix*, que é usado para filtragem.

Outro parâmetro útil é *maxresults:* se houver mais contêineres que esse valor, o corpo da resposta conterá um *NextMarker*, que indica o próximo contêiner a ser retornado na próxima solicitação. Para usar esse recurso, você deve fornecer o valor *NextMarker* como o parâmetro *marker* no URI ao fazer a próxima solicitação. Ao usar esse recurso, ele é análogo à paginação de resultados. 

Para usar outros parâmetros, acrescente-os à cadeia de caracteres do recurso com o valor, como neste exemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Cabeçalhos de solicitação](/rest/api/storageservices/List-Containers2#request-headers)**:** Essa seção lista os cabeçalhos de solicitação obrigatórios e opcionais. Três dos cabeçalhos são obrigatórios: um cabeçalho de *Autorização*, *x-ms-date* (contém a hora de UTC para a solicitação) e *x-ms-version* (especifica a versão da API REST a ser usada). A inclusão de *x-ms-client-request-id* nos cabeçalhos é opcional; você pode definir o valor para esse campo como qualquer coisa; ele é gravado nos logs de análise de armazenamento quando o registro em log está habilitado.

[Corpo da solicitação](/rest/api/storageservices/List-Containers2#request-body)**:** Não há corpo da solicitação para ListContainers. O corpo da solicitação é usado em todas as operações PUT ao carregar blobs, bem como SetContainerAccessPolicy, que permite que você envie em uma lista XML de políticas de acesso armazenadas para aplicar. As políticas de acesso armazenadas são discutidas no artigo [Usando SAS (assinaturas de acesso compartilhado)](storage-dotnet-shared-access-signature-part-1.md).

[Código de status de resposta](/rest/api/storageservices/List-Containers2#status-code)**:** Indica os códigos de status que você precisa saber. Neste exemplo, um código de status HTTP 200 é OK. Para obter uma lista completa de códigos de status HTTP, confira [Definições de código de status](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Para ver os códigos de erro específicos das APIs REST do Armazenamento, confira [Códigos de erro da API REST comuns](/rest/api/storageservices/common-rest-api-error-codes)

[Cabeçalhos de resposta](/rest/api/storageservices/List-Containers2#response-headers)**:** Eles incluem *Tipo de Conteúdo*; *x-ms-request-id* (a ID da solicitação transmitida, se aplicável); *x-ms-version* (indica a versão do serviço Blob usado) e a *Data* (UTC, indica a hora em que a solicitação foi feita).

[Corpo da resposta](/rest/api/storageservices/List-Containers2#response-body): Esse campo é uma estrutura XML que fornece os dados solicitados. Neste exemplo, a resposta é uma lista de contêineres e suas propriedades.

## <a name="creating-the-rest-request"></a>Criando a solicitação REST

Algumas observações antes de começar: por segurança, ao executar em produção, sempre use HTTPS em vez de HTTP. Para os fins deste exercício, você deve usar HTTP para poder exibir os dados de solicitação e resposta. Para exibir as informações de solicitação e resposta nas chamadas REST reais, você pode baixar o [Fiddler](https://www.telerik.com/fiddler) ou um aplicativo semelhante. Na solução Visual Studio, o nome da conta de armazenamento e a chave estão embutidos em código na classe e o método ListContainersAsyncREST transmite o nome e a chave da conta de armazenamento para os métodos que são usados para criar os vários componentes da solicitação REST. Em um aplicativo do mundo real, o nome da conta de armazenamento e a chave devem residir em um arquivo de configuração, em variáveis de ambiente ou ser recuperados de um Azure Key Vault.

Em nosso projeto de exemplo, o código para criar o cabeçalho de autorização é uma classe separada, com a ideia de que você pode usar a classe inteira e adicioná-la à sua própria solução e usá-la "no modo em que se encontra”. O código de cabeçalho de autorização funciona para a maioria das chamadas da API REST ao Armazenamento do Azure.

Para criar a solicitação, que é um objeto HttpRequestMessage, vá para ListContainersAsyncREST em Program.cs. As etapas para criar a solicitação são: 

* Crie o URI a ser usado para chamar o serviço. 
* Crie o objeto HttpRequestMessage e defina a carga. A carga é nula para ListContainersAsyncREST porque não estamos transmitindo nada.
* Adicione os cabeçalhos de solicitação para x-ms-date e x-ms-version.
* Obtenha o cabeçalho de autorização e adicione-o.

Algumas informações básicas de que você precisa: 

*  Para ListContainers, o **método** é `GET`. Esse valor é definido ao instanciar a solicitação. 
*  O **recurso** é a parte de consulta do URI que indica qual API está sendo chamada e, portanto, o valor é `/?comp=list`. Conforme observado anteriormente, o recurso está na página de documentação de referência que mostra as informações sobre a [API ListContainers](/rest/api/storageservices/List-Containers2).
*  O URI é construído com a criação do ponto de extremidade do serviço Blob para essa conta de armazenamento e a concatenação do recurso. O valor da **URI de solicitação** acaba sendo `http://contosorest.blob.core.windows.net/?comp=list`.
*  Para ListContainers, **requestBody** é nulo e não há **cabeçalhos** extra.

Outras APIs podem ter outros parâmetros para passar, como *ifMatch*. Um exemplo em que você pode usar ifMatch é ao chamar PutBlob. Nesse caso, você define ifMatch para uma eTag e ela atualiza o blob somente se a eTag fornecida corresponder à eTag atual no blob. Se alguém tiver atualizado o blob desde a recuperação da eTag, suas alterações não serão substituídas. 

Primeiro, defina o `uri` e a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Em seguida, crie uma instância a solicitação, defina o método como `GET` e forneça o URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adicione os cabeçalhos de solicitação para x-ms-date e x-ms-version. Esse local no código também é onde você pode adicionar outros cabeçalhos de solicitação obrigatórios para a chamada. Neste exemplo, não há nenhum cabeçalho adicional. Um exemplo de uma API que transmite cabeçalhos adicionais é SetContainerACL. Para o Armazenamento de Blobs, ele adiciona um cabeçalho chamado "x-ms-blob-public-access" e o valor para o nível de acesso.

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

## <a name="call-the-rest-api-with-the-request"></a>Chamar a API REST com a solicitação

Agora que você tem a solicitação, pode chamar SendAsync para enviar a solicitação REST. SendAsync chama a API e obtém a resposta de volta. Examine a resposta StatusCode (200 é OK) e analise a resposta. Nesse caso, você pode obter uma lista XML de contêineres. Vamos examinar o código para chamar o método GetRESTRequest a fim de criar a solicitação, executar a solicitação e examinar a resposta da lista de contêineres.

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

**Corpo da resposta (XML):** Para ListContainers, mostra a lista de contêineres e suas propriedades.

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
> O armazenamento do Azure agora dá suporte a integração do Active Directory do Azure (AD Azure) para blobs e filas. O Azure AD oferece uma experiência mais simples para autorizar uma solicitação para o Armazenamento do Azure. Para obter mais informações sobre como usar o Azure AD para autorizar operações REST, consulte [autenticar com o Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Para obter uma visão geral da integração do Azure AD com o armazenamento do Azure, consulte [autenticar o acesso ao armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).

Há um artigo que explica conceitualmente (sem código) como executar a [Autenticação para o serviço Armazenamento do Azure](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Vamos ver o artigo até exatamente o for é necessário e mostrar o código.

Primeiro, use uma autenticação de chave compartilhada. O formato de cabeçalho de autorização tem esta aparência:

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

O que são CanonicalizedHeaders e CanonicalizedResource? Boa pergunta. Na verdade, o que canonizado significa? Não é uma palavra comum nesse contexto. Aqui está o que a [Wikipédia diz (em inglês) sobre a canonicalização](https://en.wikipedia.org/wiki/Canonicalization): *Na ciência da computação, a canonicalização (às vezes, padronização ou normalização) é um processo de conversão de dados que tem mais de uma possível representação em forma canônica, "normal" ou "padrão".* Em linguagem normal, isso significa fazer a lista de itens (como cabeçalhos no caso de cabeçalhos canonizados) e padronizá-los em um formato obrigatório. Basicamente, a Microsoft decidiu-se por um formato e você precisa corresponder a ele.

Vamos começar com esses dois campos canonizados, pois eles são necessários para criar o cabeçalho de autorização.

**Cabeçalhos canonizados**

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

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Recurso canonizado**

Esta parte da cadeia de caracteres de assinatura representa a conta de armazenamento que é o destino da solicitação. Lembre-se de que o URI da solicitação é `<http://contosorest.blob.core.windows.net/?comp=list>`, com o nome da conta real (`contosorest` nesse caso). Neste exemplo, isto é retornado:

```
/contosorest/\ncomp:list
```

Se você tiver parâmetros de consulta, isso os incluirá também. Aqui está o código, que também lida com parâmetros de consulta adicionais e parâmetros de consulta com vários valores. Lembre-se de que você está criando esse código para funcionar em todas as APIs REST e, portanto, o ideal é incluir todas as possibilidades, mesmo se o método ListContainers não precisar delas.

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
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
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
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Quando você executa esse código, o MessageSignature resultante terá esta aparência:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Aqui está o valor final para AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

O AuthorizationHeader é o último cabeçalho colocado nos cabeçalhos de solicitação antes de enviar a resposta.

Isso abrange tudo o que você precisa saber, juntamente com o código para criar uma classe que pode ser usada para criar uma solicitação a fim de chamar as APIs REST do Serviço de Armazenamento.

## <a name="how-about-another-example"></a>Que tal outro exemplo? 

Vamos dar uma olhada em como alterar o código para chamar ListBlobs para o contêiner *container-1*. Isso é quase idêntico ao código para listar os contêineres; as únicas diferenças são o URI e como analisar a resposta. 

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

**Cabeçalhos canonizados:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Recurso canonizado:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

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

**Corpo da resposta (XML):** Essa resposta XML mostra a lista de blobs e suas propriedades. 

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

Neste artigo, você aprendeu a fazer uma solicitação para a API REST do armazenamento de blobs a fim de recuperar uma lista de contêineres ou de blobs em um contêiner. Você também aprendeu a criar a assinatura de autorização para a chamada à API REST, como usá-la na solicitação REST e como examinar a resposta.

## <a name="next-steps"></a>Próximas etapas

* [API REST do serviço Blob](/rest/api/storageservices/blob-service-rest-api)
* [API REST do serviço de arquivo](/rest/api/storageservices/file-service-rest-api)
* [API REST do serviço Fila](/rest/api/storageservices/queue-service-rest-api)
