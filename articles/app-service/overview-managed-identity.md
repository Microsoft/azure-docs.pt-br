---
title: Identidades gerenciadas
description: Saiba como as identidades gerenciadas funcionam no Serviço de Aplicativo do Azure e no Azure Functions, como configurar uma identidade gerenciada e gerar um token para um recurso de back-end.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 16cd4685f513eb628372802cc158195b81bce72a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736164"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions

Este tópico mostra como criar uma identidade gerenciada para aplicativos do Serviço de Aplicativo e do Azure Functions e como usá-la para acessar outros recursos. 

> [!Important] 
> As identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions não se comportarão conforme o esperado se seu aplicativo for migrado entre assinaturas/locatários. O aplicativo precisará obter uma nova identidade, que pode ser feito ao desabilitar e reabilitar o recurso. Consulte [removendo uma identidade](#remove) abaixo. Os recursos de downstream também precisarão ter políticas de acesso atualizadas para usar a nova identidade.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída pelo sistema

Criar um aplicativo com uma identidade designada pelo sistema requer uma propriedade adicional a ser definida no aplicativo.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para configurar uma identidade gerenciada no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.

2. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda.

3. Selecionar **Identidade**.

4. Na guia **Sistema atribuído**, alterne o **Status** para **Ligado**. Clique em **Save** (Salvar).

    ![Captura de tela que mostra onde alternar o status para ativado e, em seguida, selecione salvar.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Para localizar a identidade gerenciada para seu aplicativo Web ou de slot no portal do Azure, em **aplicativos empresariais**, procure na seção **configurações do usuário** . Normalmente, o nome do slot é semelhante a `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando a CLI do Azure, será preciso usar o comando `az webapp identity assign` em um aplicativo existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell inserido usando o botão “Experimentar”, localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente da CLI do Azure](/cli/azure/install-azure-cli) (2.0.31 ou mais recente) se você preferir usar um console da CLI local. 

As etapas a seguir o guiarão na criação de um aplicativo Web e na atribuição de uma identidade a ele usando a CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure na a qual você deseja implantar o aplicativo:

    ```azurecli-interactive
    az login
    ```

2. Crie um aplicativo Web usando a CLI. Para ver mais exemplos de como usar a CLI com o Serviço de Aplicativo, consulte [Exemplos de CLI do Serviço de Aplicativo](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Execute o comando `identity assign` para criar a identidade para este aplicativo:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir guiarão você na criação de um aplicativo e na atribuição de uma identidade usando o Azure PowerShell. As instruções para criar um aplicativo Web e um aplicativo de funções são diferentes.

#### <a name="using-azure-powershell-for-a-web-app"></a>Usando o Azure PowerShell para um aplicativo Web

1. Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Login-AzAccount` para criar uma conexão com o Azure.

2. Crie um aplicativo da Web usando o Azure PowerShell. Para obter mais exemplos de como usar o Azure PowerShell com o Serviço de Aplicativo, consulte [ Amostras do PowerShell do Serviço de Aplicativo ](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Execute o comando `Set-AzWebApp -AssignIdentity` para criar a identidade para este aplicativo:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Usando o Azure PowerShell para um aplicativo de funções

1. Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Login-AzAccount` para criar uma conexão com o Azure.

2. Crie um aplicativo de funções usando o Azure PowerShell. Para obter mais exemplos de como usar o Azure PowerShell com o Azure Functions, confira a [referência do Az.Functions](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Você também pode atualizar um aplicativo de funções existente usando `Update-AzFunctionApp` em vez disso.

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre a implantação do Serviço de Aplicativo e do Azure Functions, consulte [Automatizar a implantação de recursos no Serviço de Aplicativo](../app-service/deploy-complex-application-predictably.md) e [Automatizar a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a propriedade a seguir na definição de recurso:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a `type` propriedade seria `SystemAssigned,UserAssigned`

Adicionar o tipo atribuído pelo sistema diz ao Azure para criar e gerenciar a identidade do seu aplicativo.

Por exemplo, um aplicativo Web pode ser semelhante ao seguinte:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A propriedade tenantId identifica a qual locatário do Azure Active Directory a identidade pertence. O principalId é um identificador exclusivo para a nova identidade do aplicativo. No Azure Active Directory, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou do Azure Functions.

Se você precisar fazer referência a essas propriedades em um estágio posterior no modelo, poderá fazer isso por meio da [ `reference()` função de modelo](../azure-resource-manager/templates/template-functions-resource.md#reference) com o `'Full'` sinalizador, como neste exemplo:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída pelo usuário

Criar um aplicativo com uma identidade atribuída pelo usuário exige que você crie a identidade e, em seguida, adicione seu identificador de recursos à configuração do aplicativo.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Primeiro, você precisará criar um recurso de identidade atribuído pelo usuário.

1. Crie um recurso de identidade gerenciado atribuído pelo usuário de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.

3. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda.

4. Selecionar **Identidade**.

5. Na guia **Usuário atribuído**, clique em **Adicionar**.

6. Procure a identidade que você criou anteriormente e selecione-a. Clique em **Adicionar**.

    ![Identidade gerenciada no Serviço de Aplicativo](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir guiarão você na criação de um aplicativo e na atribuição de uma identidade usando o Azure PowerShell.

> [!NOTE]
> A versão atual dos commandlets do Azure PowerShell para o Serviço de Aplicativo do Azure não são compatíveis com identidades atribuídas pelo usuário. As instruções abaixo são para o Azure Functions.

1. Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Login-AzAccount` para criar uma conexão com o Azure.

2. Crie um aplicativo de funções usando o Azure PowerShell. Para obter mais exemplos de como usar o Azure PowerShell com o Azure Functions, confira a [referência do Az.Functions](/powershell/module/az.functions/#functions). O script abaixo também usa `New-AzUserAssignedIdentity` que deve ser instalado separadamente de acordo com [Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando o Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Você também pode atualizar um aplicativo de funções existente usando `Update-AzFunctionApp` em vez disso.

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre a implantação do Serviço de Aplicativo e do Azure Functions, consulte [Automatizar a implantação de recursos no Serviço de Aplicativo](../app-service/deploy-complex-application-predictably.md) e [Automatizar a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade incluindo o seguinte bloco na definição do recurso, substituindo `<RESOURCEID>` pelo ID do recurso da identidade desejada:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a `type` propriedade seria `SystemAssigned,UserAssigned`

Adicionar o tipo atribuído pelo usuário informa ao Azure para usar a identidade atribuída pelo usuário especificada para seu aplicativo.

Por exemplo, um aplicativo Web pode ser semelhante ao seguinte:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

O principalId é um identificador exclusivo para a identidade que é usada para a administração do Azure Active Directory. O clientId é um identificador exclusivo para a nova identidade do aplicativo que é usado para especificar qual identidade usar durante as chamadas de runtime.

## <a name="obtain-tokens-for-azure-resources"></a>Obter tokens para recursos do Azure

Um aplicativo pode usar sua identidade gerenciada para obter tokens que acessam outros recursos protegidos pelo Azure Active Directory, como o Azure Key Vault. Esses tokens representam o acesso do aplicativo ao recurso e não um usuário específico do aplicativo. 

Talvez seja necessário configurar o recurso de destino para permitir o acesso do aplicativo. Por exemplo, se você solicitar um token para acessar o Key Vault, será necessário se certificar de que uma política de acesso que inclui a identidade do aplicativo foi adicionada. Caso contrário, as chamadas para o Key Vault serão rejeitadas, mesmo se elas incluírem o token. Para saber mais sobre os recursos que oferecem suporte a tokens do Azure Active Directory, veja [Serviços do Azure que dão suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Os serviços de back-end para identidades gerenciadas mantêm um cache por URI de recurso por cerca de 24 horas. Se você atualizar a política de acesso de um recurso de destino específico e recuperar imediatamente um token para esse recurso, você poderá continuar a obter um token em cache com permissões desatualizadas até que esse token expire. Atualmente, não é possível forçar uma atualização de token.

Há um protocolo REST simples para obter um token no Serviço de Aplicativo e no Azure Functions. Isso pode ser usado para todos os aplicativos e linguagens. Para .NET e Java, o SDK do Azure fornece uma abstração sobre esse protocolo e facilita uma experiência de desenvolvimento local.

### <a name="using-the-rest-protocol"></a>Usar o protocolo REST

> [!NOTE]
> Uma versão mais antiga desse protocolo, usando a versão de “01-09-2017” da API, usou o cabeçalho `secret` em vez de `X-IDENTITY-HEADER` e aceitou apenas a propriedade `clientid` para atribuição pelo usuário. Ele também retornou `expires_on` em um formato de carimbo de data/hora. MSI_ENDPOINT pode ser usado como um alias para IDENTITY_ENDPOINT e MSI_SECRET pode ser usado como um alias para IDENTITY_HEADER. Atualmente, esta versão do protocolo é necessária para planos de Hospedagem de consumo do Linux.

Um aplicativo com uma identidade gerenciada tem duas variáveis de ambiente definidas:

- IDENTITY_ENDPOINT: a URL para o serviço de token local.
- IDENTITY_HEADER: um cabeçalho usado para ajudar a reduzir os ataques de falsificação da solicitação do lado do servidor (SSRF). O valor é trocado pela plataforma.

O **IDENTITY_ENDPOINT** é uma URL local a partir da qual o aplicativo pode solicitar tokens. Para obter um token para um recurso, solicite uma HTTP GET para esse ponto de extremidade, incluindo os seguintes parâmetros:

> | Nome do parâmetro    | No     | Descrição                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | recurso          | Consulta  | O URI do recurso do Azure Active Directory do recurso para o qual um token deve ser obtido. Pode ser um dos [serviços do Azure que dão suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou a qualquer outro URI de recurso.    |
> | api-version       | Consulta  | A versão da API do token a ser usada. Use "2019-08-01" ou posterior (a menos que use o consumo do Linux, que atualmente só oferece "2017-09-01"-consulte a observação acima).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Cabeçalho | O valor da variável de ambiente IDENTITY_HEADER. Esse cabeçalho é usado para ajudar a reduzir os ataques de falsificação da solicitação do lado do servidor (SSRF).                                                                                                                                                                                                    |
> | client_id         | Consulta  | (Opcional) A ID da identidade atribuída pelo usuário a ser usada. Não pode ser usada em uma solicitação que inclui `principal_id`, `mi_res_id`ou `object_id`. Se todos os parâmetros de ID (`client_id`, `principal_id`, `object_id`e `mi_res_id`) forem omitidos, será usada a identidade atribuída pelo sistema.                                             |
> | principal_id      | Consulta  | (Opcional) A ID principal da identidade atribuída pelo usuário a ser usada. `object_id` é um alias que pode ser usado no lugar dela. Não pode ser usada em uma solicitação que inclui client_id, mi_res_id ou object_id. Se todos os parâmetros de ID (`client_id`, `principal_id`, `object_id`e `mi_res_id`) forem omitidos, será usada a identidade atribuída pelo sistema. |
> | mi_res_id         | Consulta  | (Opcional) A ID do recurso do Azure da identidade atribuída pelo usuário a ser usada. Não pode ser usada em uma solicitação que inclui `principal_id`, `client_id`ou `object_id`. Se todos os parâmetros de ID (`client_id`, `principal_id`, `object_id`e `mi_res_id`) forem omitidos, será usada a identidade atribuída pelo sistema.                                      |

> [!IMPORTANT]
> Se você estiver tentando obter tokens para identidades atribuídas pelo usuário, deverá incluir uma das propriedades opcionais. Caso contrário, o serviço de token tentará obter um token para uma identidade atribuída pelo sistema, que pode ou não existir.

Uma resposta bem-sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> | Nome da propriedade | Descrição                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento.                                                                                                                               |
> | client_id     | A ID do cliente da identidade que foi usada.                                                                                                                                                                                                       |
> | expires_on    | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token).                                                                                |
> | not_before    | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token).                                                      |
> | recurso      | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação.                                                                                                                               |
> | token_type    | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador Para saber mais sobre os tokens de portador, consulte [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Essa resposta é igual à [resposta para a solicitação de token de acesso de serviço a serviço do Azure Active Directory](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Uma solicitação de exemplo pode ser semelhante ao seguinte:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Uma resposta de exemplo pode ser semelhante ao seguinte:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Exemplos de código

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Para as linguagens .NET, também é possível usar [Microsoft.Azure.Services.AppAuthentication](#asal) em vez de criar essa solicitação por conta própria.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Usar a biblioteca de Microsoft.Azure.Services.AppAuthentication do .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do pacote Microsoft.Azure.Services.AppAuthentication. Essa biblioteca também permitirá que você teste seu código localmente em sua máquina de desenvolvimento, usando sua conta de usuário do Visual Studio, a [Azure CLI](/cli/azure) ou a Autenticação Integrada do Active Directory. Quando hospedado na nuvem, ele usará como padrão uma identidade atribuída pelo sistema, mas você pode personalizar esse comportamento usando uma variável de ambiente de cadeia de conexão que faz referência à ID do cliente de uma identidade atribuída pelo usuário. Para obter mais informações sobre opções de desenvolvimento com essa biblioteca, consulte a [referência Microsoft. Azure. Services. AppAuthentication]. Esta seção mostra a você como começar a usar a biblioteca no seu código.

1. Adicione referências a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e a qualquer outro pacote NuGet necessário para seu aplicativo. O exemplo abaixo também usa [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Adicione o seguinte código ao seu aplicativo, modificando-o para ter como destino o recurso correto. Este exemplo mostra duas maneiras de trabalhar com o Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Se você quiser usar uma identidade gerenciada atribuída pelo usuário, poderá definir a `AzureServicesAuthConnectionString` configuração do aplicativo como `RunAs=App;AppId=<clientId-guid>` . Substitua `<clientId-guid>` pela ID do cliente da identidade que você deseja usar. Você pode definir várias dessas cadeias de conexão usando configurações de aplicativo personalizadas e passando seus valores para o Construtor O azureservicetokenprovider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Para saber mais sobre como configurar o O azureservicetokenprovider e as operações que ele expõe, consulte a [referência do Microsoft. Azure. Services. AppAuthentication] e o [serviço de aplicativo e o keyvault com o exemplo do MSI .net](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Usando o SDK do Azure para Java

Para aplicativos e funções Java, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java). Esta seção mostra a você como começar a usar a biblioteca no seu código.

1. Adicione uma referência à biblioteca [SDK do Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para projetos Maven, você pode adicionar esse trecho à seção `dependencies` do arquivo POM do projeto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Use o objeto `AppServiceMSICredentials` para autenticação. Este exemplo mostra como esse mecanismo pode ser usado para trabalhar com o Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Uma identidade atribuída pelo sistema pode ser removida desabilitando o recurso usando o portal, PowerShell ou CLI da mesma forma que foi criado. As identidades atribuídas pelo usuário podem ser removidas individualmente. Para remover todas as identidades, defina o tipo de identidade como "Nenhum".

Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do Azure Active Directory. As identidades atribuídas pelo sistema também são automaticamente removidas do Azure Active Directory quando o recurso do aplicativo é excluído.

Para remover todas as identidades em um [modelo do ARM](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Para remover todas as identidades no Azure PowerShell (somente Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Há também uma configuração de aplicativo que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desativa o serviço de token local. No entanto, ele deixa a identidade no local e ferramentas ainda mostrará a identidade gerenciada como "ligada" ou "habilitada". Como resultado, o uso dessa configuração não é recomendado.

## <a name="next-steps"></a>Próximas etapas

- [Acesse o Banco de Dados SQL com segurança usando uma identidade gerenciada](app-service-web-tutorial-connect-msi.md)
- [Acessar o armazenamento do Azure com segurança usando uma identidade gerenciada](scenario-secure-app-access-storage.md)
- [Chamar Microsoft Graph com segurança usando uma identidade gerenciada](scenario-secure-app-access-microsoft-graph-as-app.md)

[Referência Microsoft.Azure.Services.AppAuthentication]: /dotnet/api/overview/azure/service-to-service-authentication