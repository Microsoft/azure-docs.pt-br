---
title: "Como usar uma Identidade de Serviço Gerenciado da VM do Azure para entrada e aquisição de token"
description: "Instruções passo a passo sobre como usar uma entidade de serviço da MSI da VM do Azure para entrar e adquirir um token de acesso."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.openlocfilehash: 2f6cf4709c77ca1bb051b7d5c9e7d1d5d125c343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Como usar uma MSI (Identidade de Serviço Gerenciado) da VM do Azure para entrada e aquisição de token 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] Depois de habilitar a MSI em uma VM do Azure, você pode usar a MSI para entrar e solicitar um token de acesso. Este artigo mostra várias maneiras de usar uma [entidade de serviço](develop/active-directory-dev-glossary.md#service-principal-object) da MSI para entrar e adquirir um [token de acesso somente de aplicativo](develop/active-directory-dev-glossary.md#access-token) para acessar outros recursos, incluindo:

- Entrada no modo silencioso/autônomo do PowerShell ou da CLI do Azure
- Aquisição de token para vários clientes, incluindo .NET, PowerShell, Bash/CURL, REST
- Entrar usando um SDK do Azure, incluindo .NET, Java, Node.js, Python, Ruby

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do PowerShell neste artigo, instale o [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM) ou superior. Se você planeja usar os exemplos de CLI do Azure neste artigo, tem três opções:
- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito dos blocos de código da CLI do Azure.
- [Instalar a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar a CLI em um prompt de comando local. 


> [!IMPORTANT]
> - Todo código/script de exemplo neste artigo supõe que o cliente esteja sendo executado em uma Máquina Virtual habilitada para MSI. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) da VM usando o portal do Azure](msi-qs-configure-portal-windows-vm.md) ou um dos artigos variantes (o PowerShell, CLI, modelo ou SDK do Azure). 
> - Para evitar erros de autorização (403/AuthorizationFailed) nos exemplos de código/script, a identidade da VM deve receber acesso de "Leitor" no escopo de VM para permitir operações do Azure Resource Manager na VM. Consulte [Atribuir um acesso de MSI (Identidade de Serviço Gerenciado) a um recurso usando o portal do Azure](msi-howto-assign-access-portal.md) para obter detalhes.
> - Antes de prosseguir para uma das seções a seguir, use o recurso "Conectar" da VM no portal do Azure para conectar-se remotamente à sua VM habilitada para MSI.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Como entrar do PowerShell ou da CLI usando MSI

Anteriormente, executar um script sob sua própria identidade significava:
- registrá-lo o como um aplicativo cliente confidencial/Web com o Azure AD
- entrar usando as credenciais de ID/segredo do cliente do aplicativo

Com a MSI, seu cliente de script não precisa mais registrar-se com o Azure AD nem fornecer credenciais. Nos exemplos a seguir, mostramos como usar a entidade de serviço da MSI da VM para entrar.

### <a name="azure-powershell"></a>Azure PowerShell

O script a seguir demonstra como:

- adquirir um token de acesso da MSI para uma VM do Azure
- usar o token de acesso para entrar no Azure AD sob entidade de serviço MSI correspondente
- usar a entidade de serviço do MSI para fazer uma chamada do Azure Resource Manager para obter a ID da entidade de serviço

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

- entrar no Azure AD sob a entidade de serviço MSI da VM
- usar a entidade de serviço do MSI para fazer uma chamada do Azure Resource Manager para obter a ID da entidade de serviço

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Como adquirir um token de acesso da MSI

Usando a MSI, o script/aplicativo cliente não precisa mais se registrar com o Azure AD, nem apresentar sua ID e segredo de cliente para obter um token de acesso. O cliente pode pedir simplesmente o ponto de extremidade local da MSI para um token de acesso, sem apresentar credenciais de aplicativo. O token de acesso somente de aplicativo é emitido para a entidade de serviço da MSI adequada para uso como um token de portador em [chamadas serviço a serviço que exigem credenciais de cliente](active-directory-protocols-oauth-service-to-service.md).

Nos exemplos a seguir, mostramos como usar MSI da VM para aquisição de token.

### <a name="net"></a>.NET

> [!IMPORTANT]
> A ADAL (Biblioteca de Autenticação do Azure AD) não está integrada à MSI. Para obter um token de acesso usando a MSI, faça uma solicitação ao ponto de extremidade da MSI local em uma VM. Para obter mais informações, consulte [Problemas conhecidos e perguntas frequentes sobre MSI](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
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

### <a name="azure-powershell-token"></a>PowerShell do Azure

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Solicitação de exemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | O ponto de extremidade da MSI em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Neste exemplo, solicitamos um token para acessar o Azure Resource Manager, que tem um URI de ID do Aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido pela MSI como uma mitigação contra ataques de SSRF (Falsificação de Solicitação no Lado de Servidor). Esse valor deve ser definido como "true", com todas as letras minúsculas.

Exemplo de resposta:

```
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
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não é usado pela MSI. |
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |

## <a name="how-to-sign-in-with-azure-sdk-libraries-using-msi"></a>Como entrar com bibliotecas do SDK do Azure usando a MSI

O Azure dá suporte a várias plataformas de programação por meio de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Vários deles foram atualizados para dar suporte à entrada usando uma MSI e apresentam exemplos correspondentes para demonstrar o uso. Esta lista é atualizada conforme suporte adicional é adicionado:

| . | Amostra |
| --- | ------ | 
| .NET   | [Gerenciar recursos de uma VM habilitada para MSI](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerenciar Armazenamento de uma VM habilitada para MSI](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Gerenciar recursos usando a Identidade de Serviço Gerenciado](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [Use o MSI para autenticar de modo simples de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Gerenciar recurso de uma VM habilitada para MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Informações adicionais

### <a name="token-expiration"></a>Expiração do token

O subsistema de MSI local armazena em cache os tokens. Portanto, você pode chamá-lo sempre que desejar e uma chamada durante a transmissão para resultados do Azure AD somente se:
- ocorrer um cache ignorado devido a não haver token no cache
- o token está expirado

Se você armazenar em cache o token em seu código, deverá estar preparado para lidar com cenários em que o recurso indique que o token expirou.

### <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure com suporte para autenticação do Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de serviços que dão suporte à MSI e às suas respectivas IDs de recurso.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="sign-in-or-token-acquisition-fails"></a>A entrada ou aquisição de token falha

Verifique se a MSI foi habilitada corretamente. Volte à VM do Azure no [portal do Azure](https://portal.azure.com) e:

- Examine a página "Configuração" e certifique-se de que o MSI esteja habilitado = "Sim".
- Examine a página "Extensões" e verifique se a extensão do MSI foi implantada com êxito.

Se um deles estiver incorreto, reimplante o MSI em seu recurso ou solucione a falha de implantação.

### <a name="http-request-error-responses"></a>Respostas de erro de solicitação HTTP

- *bad_request_102: cabeçalho de metadados necessário não especificado*

  O campo de cabeçalho de solicitação `Metadata` está ausente da solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, com todas as letras minúsculas. Consulte a "Amostra de solicitação" na [seção REST anterior](#rest) para obter um exemplo.

- *desconhecido: falha ao recuperar o token do Active Directory. Para obter detalhes, consulte os logs no \<caminho do arquivo\>*

  Verifique se o URI da solicitação HTTP GET está formatado corretamente, em especial o URI do recurso especificado na cadeia de consulta. Consulte a "Solicitação de amostra" na [seção REST anterior](#rest) para obter um exemplo ou [Serviços do Azure que dão suporte para autenticação do Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de serviços e seus respectivos IDs de recurso.

- *unknown_source: \<URI\> de fonte desconhecida*

  Verifique se o URI da solicitação HTTP GET está formatado corretamente. A parte `scheme:host/resource-path` deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte a "Amostra de solicitação" na [seção REST anterior](#rest) para obter um exemplo.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para habilitar o MSI em uma VM do Azure, confira [Configurar um MSI (identidade do serviço gerenciada) usando o PowerShell](msi-qs-configure-powershell-windows-vm.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

