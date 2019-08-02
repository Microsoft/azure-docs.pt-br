---
title: Visão geral de identidades gerenciadas – Serviço de Aplicativo do Azure | Microsoft Docs
description: Guia de configuração e referência conceitual para identidades gerenciadas no Serviço de Aplicativo do Azure e no Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 8bc30d50772dffddca32d9f6e22c3d7cec566c70
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297148"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions

> [!NOTE] 
> O suporte à identidade gerenciada para o Serviço de Aplicativo Web para Contêineres e o Linux está atualmente em versão prévia.

> [!Important] 
> Identidades gerenciadas para Serviço de Aplicativo e Azure Functions não se comportarão conforme o esperado se seu aplicativo for migrado entre assinaturas/locatários. O aplicativo precisará obter uma nova identidade, que pode ser feito ao desabilitar e reabilitar o recurso. Consulte [removendo uma identidade](#remove) abaixo. Recursos de downstream também precisarão ter políticas de acesso atualizadas para usar a nova identidade.

Este tópico mostra como criar uma identidade gerenciada para aplicativos do Serviço de Aplicativo e do Azure Functions e como usá-la para acessar outros recursos. Uma identidade gerenciada do Azure Active Directory permite que o aplicativo acesse facilmente os outros recursos protegidos pelo AAD, como o Azure Key Vault. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou alternância de nenhum segredo. Para obter mais informações sobre identidades gerenciadas no AAD, veja [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades: 
- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se o seu aplicativo for excluído. Um aplicativo só pode ter uma identidade atribuída pelo sistema. O suporte de identidade atribuído pelo sistema geralmente está disponível para aplicativos do Windows. 
- Uma **identidade atribuída pelo usuário** é um recurso do Azure independente que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário. O suporte de identidade atribuído pelo usuário está em pré-visualização para todos os tipos de aplicativos.

## <a name="adding-a-system-assigned-identity"></a>Adicionando uma identidade designada pelo sistema

Criar um aplicativo com uma identidade designada pelo sistema requer uma propriedade adicional a ser definida no aplicativo.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para configurar uma identidade gerenciada no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.

2. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda.

3. Selecione **Identidade gerenciada**.

4. Na guia **Sistema atribuído**, alterne o **Status** para **Ligado**. Clique em **Salvar**.

![Identidade gerenciada no Serviço de Aplicativo](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando a CLI do Azure, será preciso usar o comando `az webapp identity assign` em um aplicativo existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou mais recente) se você preferir usar um console da CLI local. 

As etapas a seguir o guiarão na criação de um aplicativo Web e na atribuição de uma identidade a ele usando a CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar o aplicativo:

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

As etapas a seguir guiarão você pela criação de um aplicativo da Web e pela atribuição de uma identidade usando o Azure PowerShell:

1. Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Login-AzAccount` para criar uma conexão com o Azure.

2. Crie um aplicativo da Web usando o Azure PowerShell. Para obter mais exemplos de como usar o Azure PowerShell com o Serviço de Aplicativo, consulte [ Amostras do PowerShell do Serviço de Aplicativo ](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Execute o comando `Set-AzWebApp -AssignIdentity` para criar a identidade para este aplicativo:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre a implantação do Serviço de Aplicativo e do Azure Functions, consulte [Automatizar a implantação de recursos no Serviço de Aplicativo](../app-service/deploy-complex-application-predictably.md) e [Automatizar a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a propriedade a seguir na definição de recurso:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

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

No qual `<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade tenantId identifica a qual locatário do AAD a identidade pertence. O principalId é um identificador exclusivo para a nova identidade do aplicativo. No AAD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Serviço de Aplicativo ou das Funções do Azure.


## <a name="adding-a-user-assigned-identity-preview"></a>Adicionando uma identidade atribuída pelo usuário (versão prévia)

> [!NOTE] 
> As identidades atribuídas pelo usuário estão atualmente em pré-visualização. Nuvens soberanas ainda não são compatíveis.

Criar um aplicativo com uma identidade atribuída pelo usuário exige que você crie a identidade e, em seguida, adicione seu identificador de recursos à configuração do aplicativo.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

> [!NOTE] 
> Essa experiência do portal está sendo implantada e pode ainda não estar disponível em todas as regiões.

Primeiro, você precisará criar um recurso de identidade atribuído pelo usuário.

1. Crie um recurso de identidade gerenciado atribuído pelo usuário de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.

3. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda.

4. Selecione **Identidade gerenciada**.

5. Dentro de **usuário atribuído (versão prévia)** , clique em **Add**.

6. Procure a identidade que você criou anteriormente e selecione-a. Clique em **Adicionar** .

![Identidade gerenciada no Serviço de Aplicativo](media/app-service-managed-service-identity/msi-blade-user.png)

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
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

Adicionar o tipo atribuído pelo usuário informa ao Azure para criar e gerenciar a identidade do seu aplicativo.

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

No qual `<PRINCIPALID>` e `<CLIENTID>` são substituídos por GUIDs. O principalId é um identificador exclusivo para a identidade que é usada para a administração do AAD. O clientId é um identificador exclusivo para a nova identidade do aplicativo que é usado para especificar qual identidade usar durante as chamadas de tempo de execução.


## <a name="obtaining-tokens-for-azure-resources"></a>Obter tokens para recursos do Azure

Um aplicativo pode usar sua identidade para obter tokens para outros recursos protegidos pelo AAD, como o Azure Key Vault. Esses tokens representam o acesso do aplicativo ao recurso e não um usuário específico do aplicativo. 

> [!IMPORTANT]
> Talvez seja necessário configurar o recurso de destino para permitir o acesso do aplicativo. Por exemplo, se você solicitar um token para o Key Vault, será necessário se certificar de que uma política de acesso que inclui a identidade do aplicativo foi adicionada. Caso contrário, as chamadas para o Key Vault serão rejeitadas, mesmo se elas incluírem o token. Para saber mais sobre os recursos que oferecem suporte a tokens do Azure Active Directory, veja [Serviços do Azure que dão suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Há um protocolo REST simples para obter um token no Serviço de Aplicativo e no Azure Functions. Para aplicativos .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local.

### <a name="asal"></a>Usar a biblioteca de Microsoft.Azure.Services.AppAuthentication do .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do pacote Microsoft.Azure.Services.AppAuthentication. Essa biblioteca também permitirá que você teste seu código localmente em sua máquina de desenvolvimento, usando sua conta de usuário do Visual Studio, a [Azure CLI](/cli/azure) ou a Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte a [Referência Microsoft.Azure.Services.AppAuthentication]. Esta seção mostra a você como começar a usar a biblioteca no seu código.

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

Para saber mais sobre o Microsoft.Azure.Services.AppAuthentication e as operações que ele expõe, consulte a [Referência Microsoft.Azure.Services.AppAuthentication] e [Serviço de Aplicativo e KeyVault com a amostra MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).


### <a name="using-the-azure-sdk-for-java"></a>Usando o SDK do Azure para Java

Para aplicativos e funções Java, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java). Esta seção mostra a você como começar a usar a biblioteca no seu código.

1. Adicione uma referência à [biblioteca do SDK do Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para projetos Maven, você pode adicionar esse trecho à `dependencies` seção do arquivo POM do projeto:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.23.0</version>
</dependency>
```

2. Use o `AppServiceMSICredentials` objeto para autenticação. Este exemplo mostra como esse mecanismo pode ser usado para trabalhar com Azure Key Vault:

```java
import com.microsoft.azure.AzureEnvironment;
import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.keyvault.Vault
//...
Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
        .withSubscription(subscriptionId);
Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

```

### <a name="using-the-rest-protocol"></a>Usar o protocolo REST

Um aplicativo com uma identidade gerenciada tem duas variáveis de ambiente definidas:

- MSI_ENDPOINT: a URL para o serviço de token local.
- MSI_SECRET: um cabeçalho usado para ajudar a reduzir os ataques de falsificação da solicitação do lado do servidor (SSRF). O valor é trocado pela plataforma.

A **MSI_ENDPOINT** é uma URL local da qual o aplicativo pode solicitar tokens. Para obter um token para um recurso, solicite uma HTTP GET para esse ponto de extremidade, incluindo os seguintes parâmetros:

> |Nome do parâmetro|No|DESCRIÇÃO|
> |-----|-----|-----|
> |resource|Consultar|O URI do recurso do AAD do recurso para o qual um token deve ser obtido. Pode ser um dos [serviços do Azure que dão suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou a qualquer outro URI de recurso.|
> |api-version|Consultar|A versão da API do token a ser usada. Atualmente, a única versão com suporte é 2017-09-01.|
> |secret|Cabeçalho|O valor da variável de ambiente MSI_SECRET. Esse cabeçalho é usado para ajudar a reduzir os ataques de falsificação da solicitação do lado do servidor (SSRF).|
> |clientid|Consultar|(Opcional) O ID da identidade atribuída pelo usuário a ser usada. Se omitido, a identidade atribuída pelo sistema é usada.|

Uma resposta bem-sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> |Nome da propriedade|DESCRIÇÃO|
> |-------------|----------|
> |access_token|O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento.|
> |expires_on|A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache.|
> |resource|O URI da ID de aplicativo do serviço Web de recebimento.|
> |token_type|Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador. Para saber mais sobre os tokens de portador, consulte [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Essa resposta é igual a [resposta à solicitação do AAD de token de acesso de serviço para serviço](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Variáveis de ambiente são configuradas quando o processo é iniciado pela primeira vez, então após habilitar a identidade gerenciada para o seu aplicativo, você pode precisar reiniciar o aplicativo ou reimplantar o código dele antes que `MSI_ENDPOINT` e `MSI_SECRET` estejam disponíveis para seu código.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Uma solicitação de exemplo pode ser semelhante ao seguinte:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Uma resposta de exemplo pode ser semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Exemplos de código

<a name="token-csharp"></a>Para fazer essa solicitação no C#:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> Para as linguagens .NET, também é possível usar [Microsoft.Azure.Services.AppAuthentication](#asal) em vez de criar essa solicitação por conta própria.

<a name="token-js"></a>No Node. js:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>No PowerShell:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Removendo uma identidade

Uma identidade atribuída pelo sistema pode ser removida desabilitando o recurso usando o portal, o PowerShell ou a CLI da mesma forma que foi criado. As identidades atribuídas pelo usuário podem ser removidas individualmente. Para remover todas as identidades, no protocolo de modelo REST / ARM, isso é feito definindo o tipo como "None":

```json
"identity": {
    "type": "None"
}
```

Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do AAD. As identidades atribuídas pelo sistema também são automaticamente removidas do AAD quando o recurso do aplicativo é excluído.

> [!NOTE]
> Há também uma configuração de aplicativo que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desativa o serviço de token local. No entanto, ele deixa a identidade no local e ferramentas ainda mostrará a identidade gerenciada como "ligada" ou "habilitada". Como resultado, o uso dessa configuração não é recomendado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Acesse o Banco de Dados SQL com segurança usando uma identidade gerenciada](app-service-web-tutorial-connect-msi.md)

[Referência Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
