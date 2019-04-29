---
title: Cliente de autoteste para validar previamente uma máquina virtual – Azure Marketplace | Microsoft Docs
description: Como criar um cliente de autoteste para validar previamente uma imagem de máquina virtual para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: ae01b0fb088035240e670c16d4d457d8abda1bfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846875"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Criar um cliente de autoteste para validar previamente uma imagem de máquina virtual do Azure

Use este artigo como guia para criar um serviço de cliente que consome a API de autoteste. Use a API de autoteste para pré-validar uma VM (máquina virtual) a fim de garantir que ela atende aos últimos requisitos de publicação do Azure Marketplace. Esse serviço de cliente permite que você teste uma VM antes de enviar sua oferta para certificação da Microsoft.

## <a name="development-and-testing-overview"></a>Visão geral de desenvolvimento e teste

Como parte do processo de autoteste, você criará um cliente local que se conecta ao Azure Marketplace para validar uma VM executada em sua assinatura do Azure. A VM pode executar o sistema operacional Windows ou Linux.

O cliente local executa um script que se autentica com a API de autoteste, envia informações de conexão e recebe os resultados do teste.

As etapas de alto nível para a criação de um cliente de autoteste são:

1. Escolher o locatário do Azure AD (Active Directory) para o aplicativo.
2. Registrar o aplicativo cliente.
3. Criar um token para o aplicativo cliente do Azure AD.
4. Passar o token para a API de autoteste.

Depois de criar o cliente, você poderá testá-lo em sua VM.

### <a name="self-test-client-authorization"></a>Autorização do cliente de autoteste

O diagrama a seguir mostra como funciona a autorização para chamadas de serviço a serviço usando as credenciais do cliente (certificado ou segredo compartilhado).

![Processo de autorização do cliente](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>A API do cliente de autoteste

A API de autoteste contém um ponto de extremidade individual que dá suporte apenas ao método POST.  Ela tem a seguinte estrutura.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

A tabela a seguir descreve os campos da API.


|      Campo         |    DESCRIÇÃO    |
|  ---------------   |  ---------------  |
|  Autorização     |  A cadeia de caracteres “Portador xxxx-xxxx-xxxx-xxxxx” contém o token de cliente do Azure AD (Active Directory), que pode ser criado usando o PowerShell.          |
|  DNSName           |  Nome DNS da VM a ser testada    |
|  Usuário              |  Nome de usuário para entrar na VM         |
|  Senha          |  Senha para entrar na VM          |
|  SO                |  Sistema operacional da VM: `Linux` ou `Windows`          |
|  PortNo            |  Abra o número da porta para se conectar à VM. O número da porta é normalmente `22` para Linux e `5986` para Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Consumindo a API

Você pode consumir a API de autoteste usando o PowerShell ou o cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Usar o PowerShell para consumir a API no sistema operacional Linux

Para chamar a API no PowerShell, siga estas etapas:

1. Use o comando `Invoke-WebRequest` para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na captura de tela a seguir.
3. Especifique parâmetros de corpo no formato JSON.

O exemplo de código a seguir mostra uma chamada à API no PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
A captura de tela a seguir mostra um exemplo de chamada à API no PowerShell.

![Chamar a API com o PowerShell no sistema operacional Linux](./media/stclient-call-api-ps-linuxvm.png)

Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A tela de captura a seguir, que mostra `$res.Content`, fornece os detalhes dos resultados do teste no formato JSON.

![Resultados em JSON da chamada do PowerShell ao Linux](./media/stclient-pslinux-rescontent-json.png)

A captura de tela a seguir mostra um exemplo dos resultados do teste em JSON exibidos em um visualizador JSON online (por exemplo, [Code Beautify](https://codebeautify.org/jsonviewer) ou [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Resultados em JSON da chamada do PowerShell à VM do Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Usar o PowerShell para consumir a API no sistema operacional Windows

Para chamar a API no PowerShell, siga estas etapas:

1. Use o comando `Invoke-WebRequest` para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na captura de tela a seguir.
3. Crie os parâmetros de Corpo no formato JSON.

O exemplo de código a seguir mostra uma chamada à API no PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

A captura de tela a seguir mostra um exemplo de chamada à API no PowerShell.

![Chamar a API com o PowerShell na VM do Windows](./media/stclient-call-api-ps-windowsvm.png)

Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A tela de captura a seguir, que mostra `$res.Content`, fornece os detalhes dos resultados do teste no formato JSON.

![Resultados em JSON da chamada do PowerShell ao Windows](./media/stclient-pswindows-rescontent-json.png)

A captura de tela a seguir mostra os resultados do teste exibidos em um visualizador JSON online.
(por exemplo, [Code Beautify](https://codebeautify.org/jsonviewer), [Visualizador JSON](https://jsonformatter.org/json-viewer))

![Resultados em JSON da chamada do PowerShell à VM do Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Usar o cURL para consumir a API no sistema operacional Linux

Para chamar a API com cURL, siga estas etapas:

1. Use o comando curl para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no snippet de código a seguir.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

A tela a seguir mostra um exemplo de como usar o comando curl para chamar a API.

![Chamar a API usando o comando curl](./media/stclient-consume-api-curl.png)

A captura de tela a seguir mostra os resultados em JSON da chamada de curl.

![Resultados em JSON da chamada de curl](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Escolher o locatário do Azure AD para o aplicativo

Use as etapas a seguir para escolher o locatário do Azure AD no qual deseja criar seu aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Na barra de menus superior, selecione sua conta e, na lista Diretório, escolha o locatário do Active Directory no qual deseja registrar seu aplicativo. Se preferir, selecione o ícone **Diretório + Assinatura** para ver o filtro de assinatura Global. A captura de tela a seguir mostra um exemplo desse filtro.

   ![Selecionar o filtro de assinatura](./media/stclient-subscription-filter.png)

3. Na barra de navegação à esquerda, selecione **Todos os serviços** e, em seguida, **Azure Active Directory**.

   Nas etapas a seguir, talvez você precise do nome do locatário (ou do diretório) ou da ID do locatário (ou do diretório).

   **Para obter informações de locatário:**

   Em **Visão Geral do Azure Active Directory**, pesquise “Propriedades” e, em seguida, selecione **Propriedades**. Usando a seguinte captura de tela como exemplo:

   - **Nome** – o nome do locatário ou o nome do diretório
   - **ID de Diretório** – a ID de locatário ou a ID de diretório, ou use a barra de rolagem para localizar Propriedades.

   ![Página de propriedades do Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrar o aplicativo cliente

Use as etapas a seguir para registrar o aplicativo cliente.

1. Na barra de navegação à esquerda, selecione **Todos os serviços** e, em seguida, **Registros de aplicativo**.
2. Em **Registros de aplicativo**, selecione **+ Novo registro de aplicativo**.
3. Em **Criar**, forneça as informações necessárias para os seguintes campos:

   - **Nome** – insira um nome amigável para o aplicativo. Por exemplo, “SelfTestClient”.
   - **Tipo de aplicativo** – selecione **API/Aplicativo Web**
   - **URL de logon** – tipo de "https:\//isvapp.azurewebsites.net/selftest-vm"

4. Selecione **Criar**.
5. Em **Registros de aplicativo** ou em **Aplicativo registrado**, copie a **ID do Aplicativo**.

   ![Obtenha a ID do aplicativo](./media/stclient-app-id.png)

6. Na barra de ferramentas do aplicativo registrado, selecione **Configurações**.
7. Selecione **Permissões necessárias** para configurar permissões para o aplicativo.
8. Em **Permissões necessárias**, selecione **+ Adicionar**.
9. Em **Adicionar acesso à API**, escolha **Selecionar uma API**.
10. Em **Selecionar uma API**, digite “Modelo de implantação clássico do Microsoft Azure” para pesquisar a API.
11. Nos resultados da pesquisa, escolha **Modelo de implantação clássico do Microsoft Azure** e, em seguida, clique em **Selecionar**.

    ![Configurar o multilocatário para o aplicativo](./media/stclient-select-api.png)

12. Em **Adicionar acesso à API**, escolha **Selecionar permissões**.
13. Selecione **Acessar a “API de Gerenciamento de Serviços do Microsoft Azure”**.

    ![Habilitar o acesso à API para o aplicativo](./media/stclient-enable-api-access.png)

14. Clique em **Selecionar**.
15. Selecione **Concluído**.
16. Em **Configurações**, selecione **Propriedades**.
17. Em **Propriedades**, role a tela para baixo até **Multilocatário**. Selecione **Sim**.

    ![Configurar o multilocatário para o aplicativo](./media/stclient-yes-multitenant.png)

18. Clique em **Salvar**.
19. Em **Configurações**, selecione **Chaves**.
20. Crie uma chave secreta selecionando a caixa de texto **DESCRIÇÃO** da chave. Configure os seguintes campos:

    - Digite um nome de chave. Por exemplo, “selftestclient”
    - Na lista suspensa **EXPIRA**, selecione “Em 1 ano”.
    - Selecione **Salvar** para gerar a chave.
    - Em **VALOR**, copie a chave.

      >[!Important]
      >Você não poderá ver o valor de chave após sair do formulário **Chaves**.

    ![Formulário de valor da chave](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Criar o token para o aplicativo cliente

Use um dos seguintes programas para criar e obter um token usando a API REST OAuth:

- postman
- cURL no Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Para criar e obter um token usando o Postman

 Para solicitar tokens ao Auth0 para um dos aplicativos autorizados, execute uma operação POST no ponto de extremidade [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) com um conteúdo no seguinte formato:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Passe os seguintes parâmetros no corpo da Solicitação:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Passe os seguintes parâmetros no cabeçalho da Solicitação:

```
Content-Type: application/x-www-form-urlencoded
```

A captura de tela a seguir mostra um exemplo de como usar o Postman para obter um token.

![Obter um token com o Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Para criar e obter um token usando o cURL no Linux

Para solicitar tokens ao Auth0 para um dos aplicativos autorizados, execute uma operação POST no ponto de extremidade [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) com um conteúdo no seguinte formato:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

A captura de tela a seguir mostra um exemplo de como usar o comando curl para obter um token.

![Obter o token com o comando curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Para criar e obter um token usando C&#35;

Para solicitar tokens para qualquer um dos seus aplicativos autorizados Auth0, executar uma operação POST para o https:\//soamtenant.auth0.com/oauth/token de ponto de extremidade com uma carga no seguinte formato:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Para criar e obter um token usando o PowerShell

Para solicitar tokens para qualquer um dos seus aplicativos autorizados Auth0, executar uma operação POST para o https:\//soamtenant.auth0.com/oauth/token de ponto de extremidade com uma carga no seguinte formato:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Passar o token do aplicativo cliente para a API

Passe o token para a API de autoteste usando o seguinte código no cabeçalho de autorização:

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Testar o cliente de autoteste

Para testar o cliente, siga estas etapas:

1. Implante a VM que você deseja testar.
2. Chame a API de autoteste usando o token do aplicativo cliente para autorização.
3. Obtenha os resultados do teste no formato JSON.

### <a name="test-result-examples"></a>Exemplos de resultados do teste

Os snippets de código a seguir mostram os resultados do teste no formato JSON.

**Resultados do teste para uma VM do Windows:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Resultados do teste para uma VM do Linux:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Próximas etapas

Depois de testar sua máquina virtual do Azure com êxito, você poderá [Publicar a oferta](./cpp-publish-offer.md).
