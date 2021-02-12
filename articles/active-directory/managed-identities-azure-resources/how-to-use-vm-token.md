---
title: Usar identidades gerenciadas em uma máquina virtual para adquirir o token de acesso-Azure AD
description: Instruções passo a passo e exemplos para usar identidades gerenciadas para recursos do Azure em máquinas virtuais para adquirir um token de acesso OAuth.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541f76ad825f492679530902c571096ca4b01902
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726224"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Este artigo fornece vários exemplos de código e de script para aquisição de token, assim como diretrizes sobre tópicos importantes como a manipulação de expiração de token e erros HTTP. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do Azure PowerShell neste artigo, instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Todos os scripts/exemplos de código neste artigo assumem que o cliente está executando em uma máquina virtual com identidades gerenciadas para recursos do Azure. Use o recurso "Conectar" da máquina virtual no portal do Azure para conectar remotamente a VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) ou um dos artigos variantes (usando PowerShell, CLI, um modelo ou um SDK do Azure). 

> [!IMPORTANT]
> - O limite de segurança de identidades gerenciadas para recursos do Azure é o recurso em que está sendo usado. Todos os códigos/scripts em execução em uma máquina virtual podem solicitar e recuperar tokens para quaisquer identidades gerenciadas disponíveis neles. 

## <a name="overview"></a>Visão geral

Um aplicativo cliente pode solicitar identidades gerenciadas para o [token de acesso somente de aplicativo](../develop/developer-glossary.md#access-token) dos recursos do Azure para acessar um determinado recurso. O token é [com base nas identidades gerenciadas para a entidade de serviço dos recursos do Azure](overview.md#managed-identity-types). Sendo assim, o cliente não precisa se registrar para obter um token de acesso em sua própria entidade de serviço. O token é adequado para uso como um token de portador em [chamadas de serviço a serviço que exigem credenciais de cliente](../develop/v2-oauth2-client-creds-grant-flow.md).

| Link | Descrição |
| -------------- | -------------------- |
| [Obter um token usando HTTP](#get-a-token-using-http) | Detalhes do protocolo para identidades gerenciadas do ponto de extremidade do token de recursos do Azure |
| [Obtenha um token usando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exemplo de como usar a biblioteca Microsoft.Azure.Services.AppAuthentication de um cliente .NET
| [Obter um token usando C#](#get-a-token-using-c) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade de REST de recursos do Azure de um cliente C# |
| [Obter um token usando o Java](#get-a-token-using-java) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade de REST de recursos do Azure de um cliente Java |
| [Obter um token usando Go](#get-a-token-using-go) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade de REST de recursos do Azure de um cliente Go |
| [Obter um token usando o Azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade de REST de recursos do Azure de um cliente PowerShell |
| [Obter um token usando CURL](#get-a-token-using-curl) | Exemplo de uso de identidades gerenciadas para o ponto de extremidade de REST de recursos do Azure de um cliente Bash/CURL |
| Tratamento de cache de token | Diretrizes para manipular tokens de acesso expirados |
| [Tratamento de erros](#error-handling) | Diretrizes para tratar erros HTTP retornados das identidades gerenciadas do ponto de extremidade de token de recursos do Azure |
| [IDs de recurso para serviços do Azure](#resource-ids-for-azure-services) | Onde obter IDs de recurso para os serviços do Azure compatíveis |

## <a name="get-a-token-using-http"></a>Obter um token usando HTTP 

A interface fundamental para adquirir um token de acesso é baseada em REST, tornando-a acessível para qualquer aplicativo cliente em execução na VM que pode fazer chamadas REST HTTP. Isso é semelhante ao modelo de programação do Azure AD, exceto que o cliente usa um ponto de extremidade na máquina virtual (em vez de um ponto de extremidade do Azure AD).

Exemplo de solicitação usando o ponto de extremidade do serviço de metadados na instância do Azure (IMDS) *(recomendado)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | As identidades gerenciadas do ponto de extremidade de recursos do Azure para o Serviço de Metadados de Instância. |
| `api-version`  | Um parâmetro de cadeia de caracteres, que indica a versão de API para o ponto de extremidade IMDS. Use a versão de API `2018-02-01` ou superior. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de `https://management.azure.com/`. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma atenuação contra ataque SSRF (Server Side Request Forgery). Esse valor deve ser definido como "true", com todas as letras minúsculas. |
| `object_id` | (Opcional) Um parâmetro de string de consulta, indicando o object_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando o client_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|
| `mi_res_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a mi_res_id (ID de recurso do Azure) da identidade gerenciada para a qual você gostaria de obter o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário. |

Solicitação de exemplo usando as identidades gerenciadas do Ponto de Extremidade de Extensão de VM dos recursos do Azure *(previsão de reprovação em janeiro de 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades gerenciadas do ponto de extremidade de recursos do Azure, em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de `https://management.azure.com/`. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma atenuação contra ataque SSRF (Server Side Request Forgery). Esse valor deve ser definido como "true", com todas as letras minúsculas.|
| `object_id` | (Opcional) Um parâmetro de string de consulta, indicando o object_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando o client_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|

Exemplo de resposta:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não usado por identidades gerenciadas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Obtenha um token usando a biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com identidades gerenciadas para recursos do Azure é por meio do pacote Microsoft.Azure.Services.AppAuthentication. Essa biblioteca também permitirá que você teste seu código localmente em sua máquina de desenvolvimento, usando sua conta de usuário do Visual Studio, a [Azure CLI](/cli/azure) ou a Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte a [Referência Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication). Esta seção mostra a você como começar a usar a biblioteca no seu código.

1. Adicione uma referência aos pacotes NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) no aplicativo.

2.  Adicione o código a seguir à sua página:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Para saber mais sobre o Microsoft.Azure.Services.AppAuthentication e as operações que ele expõe, consulte a [Referência do Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) e o [Serviço de Aplicativo e KeyVault com identidades gerenciadas para exemplo .NET de recursos do Azure ](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Obter um token usando C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Obter um token usando o Java

Use esta [biblioteca JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) para recuperar um token usando o Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Obter um token usando Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Obter um token usando o Azure PowerShell

O exemplo a seguir demonstra como usar as identidades gerenciadas do ponto de extremidade de REST de recursos do Azure de um cliente do PowerShell para:

1. Adquirir um token de acesso.
2. Use o token de acesso para chamar uma API REST do Azure Resource Manager e obter informações sobre a VM. Substitua sua ID de assinatura, o nome do grupo de recursos e o nome da máquina virtual para `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` e `<VM-NAME>`, respectivamente.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exemplo sobre como analisar o token de acesso da resposta:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Obter um token usando CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exemplo sobre como analisar o token de acesso da resposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Armazenamento de token em cache

Embora as identidades gerenciadas do subsistema de recursos do Azure que está sendo usado (IMDS/identidades gerenciadas para Extensão de VM dos recursos do Azure) tenham tokens de cache, também é recomendável implementar o cache de token no código. Como resultado, você deve se preparar para cenários em que o recurso indica que o token expirou. 

Chamadas durante a transmissão para o Azure AD resultam apenas quando:
- a perda no cache ocorre porque não há tokens nas identidades gerenciadas do cache do subsistema de recursos do Azure
- o token está expirado

## <a name="error-handling"></a>Tratamento de erros

As identidades gerenciadas do ponto de extremidade de recursos do Azure sinalizam erros por meio do campo de código de status do cabeçalho da mensagem de resposta HTTP, como erros 4xx ou 5xx:

| Código de status | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| 404 Não Encontrado: | Ponto de extremidade IMDS está atualizando. | Tentar novamente com Retirada Exponencial. Consulte as diretrizes abaixo. |
| 429 Número excessivo de solicitações. |  Atingido o limite de restrição IMDS. | Tentar novamente com Retirada Exponencial. Consulte as diretrizes abaixo. |
| o erro 4xx na solicitação. | Um ou mais parâmetros de solicitação estava incorreto. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  Os erros 4xx são erros de tempo de design.|
| Erro 5xx transitório do serviço. | As identidades gerenciadas do subsistema de recursos do Azure ou do Azure Active Directory retornaram um erro transitório. | É seguro tentar novamente após aguardar pelo menos 1 segundo.  Se tentar novamente muito rápido ou com muita frequência, o IMDS e/ou o Microsoft Azure Active Directory poderá retornar um erro de limite de taxa (429).|
| tempo limite | Ponto de extremidade IMDS está atualizando. | Tentar novamente com Retirada Exponencial. Consulte as diretrizes abaixo. |

Se ocorrer um erro, o corpo da resposta HTTP correspondente conterá o JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| error   | Identificador do erro. |
| error_description | Descrição detalhada do erro. **As descrições de erro podem ser alteradas a qualquer momento. Não grave o código que ramifica com base nos valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta seção documenta as possíveis respostas de erro. Um status "200 OK" é uma resposta bem-sucedida, e o token de acesso está contido no JSON do corpo de resposta, no elemento access_token.

| Código de status | Erro | Descrição do erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 Solicitação Inválida | invalid_resource | AADSTS50001: o aplicativo nomeado *\<URI\>* não foi encontrado no locatário denominado *\<TENANT-ID\>* . Isso poderá acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou aceito por qualquer usuário no locatário. Talvez você tenha enviado a solicitação de autenticação ao locatário errado.\ | (Apenas Linux) |
| 400 Solicitação Inválida | bad_request_102 | Cabeçalho de metadados necessário não especificado | O campo de cabeçalho de solicitação `Metadata` está ausente da solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, com todas as letras minúsculas. Consulte a "Solicitação de exemplo" na seção REST anterior para obter um exemplo.|
| 401 Não Autorizado | unknown_source | Origem desconhecida *\<URI\>* | Verifique se o URI da solicitação HTTP GET está formatado corretamente. A parte `scheme:host/resource-path` deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte a "Solicitação de exemplo" na seção REST anterior para obter um exemplo.|
|           | invalid_request | A solicitação não tem um parâmetro obrigatório, inclui um valor de parâmetro inválido, inclui um parâmetro mais de uma vez ou está malformada. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um token de acesso usando este método. | Causado por uma solicitação que não usou o loopback local para chamar a extensão ou em uma VM que não tem identidades gerenciadas para recursos do Azure configurados corretamente. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md), se precisar de ajuda com a configuração da VM. |
|           | access_denied | O proprietário do recurso ou o servidor de autorização negou a solicitação. |  |
|           | unsupported_response_type | O servidor de autorização não dá suporte à obtenção de um token de acesso usando este método. |  |
|           | invalid_scope | O escopo solicitado é inválido, desconhecido ou malformado. |  |
| Erro interno do servidor 500 | unknown | Falha ao recuperar o token do Active Directory. Para obter detalhes, consulte logs em *\<file path\>* | Verifique se as identidades gerenciadas dos recursos do Azure foram habilitadas na VM. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md), se precisar de ajuda com a configuração da VM.<br><br>Verifique também se seu URI de solicitação GET HTTP foi formatado corretamente, principalmente o URI do recurso especificado na cadeia de caracteres de consulta. Consulte a "Solicitação de amostra" na seção REST anterior para obter um exemplo ou [Serviços do Azure que dão suporte para autenticação do Azure AD](./services-support-managed-identities.md) para obter uma lista de serviços e seus respectivos IDs de recurso.

## <a name="retry-guidance"></a>Repita a orientação 

É recomendado tentar novamente se você receber o código de erro 404, 429 ou 5xx (confira [Tratamento de erro](#error-handling) acima).

Limitação limites se aplicam ao número de chamadas feitas para o ponto de extremidade IMDS. Quando o limite de limitação é excedido, o ponto de extremidade IMDS limita qualquer solicitação adicional enquanto a limitação está em vigor. Durante esse período, o ponto de extremidade de IMDS da MSI retornará o código de status HTTP 429 ("Muitas solicitações") e as solicitações falharão. 

Para tentar novamente, é recomendável a estratégia a seguir: 

| **Estratégia de repetição** | **Configurações** | **Valores** | **Como ele funciona** |
| --- | --- | --- | --- |
|ExponentialBackoff |Contagem de repetição<br />Retirada mín.<br />Retirada máx.<br />Retirada delta<br />Primeira repetição rápida |5<br />0 s<br />60 s<br />2 s<br />false |1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de ~2 s<br />3ª tentativa — intervalo de ~6 s<br />4ª tentativa — intervalo de ~14 s<br />5ª tentativa — intervalo de ~30 s |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure que dão suporte à autenticação do Azure AD](./services-support-managed-identities.md) para obter uma lista de recursos que dão suporte ao Azure AS e foram testados com identidades gerenciadas para recursos do Azure e as respectivas IDs do recurso.


## <a name="next-steps"></a>Próximas etapas

- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure l](qs-configure-portal-windows-vm.md).