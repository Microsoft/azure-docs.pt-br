---
title: Configurar a autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar a autenticação para diferentes recursos e fluxos de trabalho no Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 30e4fede72df8eaf922745e7781c9e0d11f7ddb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210811"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning


Saiba como configurar a autenticação para seu espaço de trabalho do Azure Machine Learning. A autenticação para seu espaço de trabalho do Azure Machine Learning é baseada no __Azure Active Directory__ (AD do Azure) para a maioria das coisas. Em geral, há três fluxos de trabalho de autenticação que você pode usar ao conectar-se ao Workspace:

* __Interativo__: você usa sua conta no Azure Active Directory para autenticar-se diretamente ou para obter um token que é usado para autenticação. A autenticação interativa é usada durante a _experimentação e o desenvolvimento iterativo_. A autenticação interativa permite controlar o acesso a recursos (como um serviço Web) por usuário.

* __Entidade de serviço__: Crie uma conta de entidade de serviço no Azure Active Directory e use-a para autenticar ou obter um token. Uma entidade de serviço é usada quando você precisa de um _processo automatizado para autenticar_ o serviço sem a necessidade de interação do usuário. Por exemplo, um script de implantação e integração contínua que treina e testa um modelo toda vez que o código de treinamento é alterado.

* __Identidade gerenciada__: ao usar o SDK do Azure Machine Learning _em uma máquina virtual do Azure_, você pode usar uma identidade gerenciada para o Azure. Esse fluxo de trabalho permite que a VM se conecte ao espaço de trabalho usando a identidade gerenciada, sem armazenar credenciais no código Python ou solicitar que o usuário se autentique. Azure Machine Learning clusters de computação também podem ser configurados para usar uma identidade gerenciada para acessar o espaço de trabalho ao _treinar modelos_.

> [!IMPORTANT]
> Independentemente do fluxo de trabalho de autenticação usado, o controle de acesso baseado em função do Azure (RBAC do Azure) é usado para delimitar o nível de acesso (autorização) permitido aos recursos. Por exemplo, um processo de administrador ou automação pode ter acesso para criar uma instância de computação, mas não usá-la, enquanto um cientista de dados pode usá-la, mas não excluí-la ou criá-la. Para obter mais informações, consulte [gerenciar o acesso ao Azure Machine Learning espaço de trabalho](how-to-assign-roles.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou use uma [instância de computação Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="azure-active-directory"></a>Azure Active Directory

Todos os fluxos de trabalho de autenticação para o seu Workspace contam com Azure Active Directory. Se você quiser que os usuários se autentiquem usando contas individuais, eles deverão ter contas no Azure AD. Se você quiser usar entidades de serviço, elas deverão existir no Azure AD. Identidades gerenciadas também são um recurso do Azure AD. 

Para saber mais sobre o Azure AD, confira [o que é a autenticação Azure Active Directory](..//active-directory/authentication/overview-authentication.md).

Depois de criar as contas do Azure AD, consulte [gerenciar o acesso ao Azure Machine Learning espaço de trabalho](how-to-assign-roles.md) para obter informações sobre como conceder acesso ao espaço de trabalho e a outras operações no Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Configurar uma entidade de serviço

Para usar uma entidade de serviço (SP), você deve primeiro criar o SP e conceder a ele acesso ao seu espaço de trabalho. Como mencionado anteriormente, o controle de acesso baseado em função do Azure (RBAC do Azure) é usado para controlar o acesso, portanto, você também deve decidir qual acesso conceder ao SP.

> [!IMPORTANT]
> Ao usar uma entidade de serviço, conceda a ela o __acesso mínimo necessário para a tarefa para a__ qual ela é usada. Por exemplo, você não concederá a um proprietário de entidade de serviço ou acesso de colaborador se tudo o que for usado para o estiver lendo o token de acesso para uma implantação da Web.
>
> O motivo para conceder o acesso mínimo é que uma entidade de serviço usa uma senha para autenticar e a senha pode ser armazenada como parte de um script de automação. Se a senha for vazada, o acesso mínimo necessário para uma tarefa específica minimizará o uso mal-intencionado do SP.

A maneira mais fácil de criar um SP e conceder acesso ao seu espaço de trabalho é usando o [CLI do Azure](/cli/azure/install-azure-cli). Para criar uma entidade de serviço e conceder acesso ao seu espaço de trabalho, use as seguintes etapas:

> [!NOTE]
> Você deve ser um administrador na assinatura para executar todas essas etapas.

1. Autentique-se na assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisará abrir um navegador e seguir as instruções na linha de comando. As instruções envolvem a navegação para [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e a inserção de um código de autorização.

    Se você tiver várias assinaturas do Azure, poderá usar o `az account set -s <subscription name or ID>` comando para definir a assinatura. Para saber mais, confira [Use multiple Azure subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli) (Usar várias assinaturas do Azure).

    Para outros métodos de autenticação, confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

1. Instale a extensão de Azure Machine Learning:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Crie a entidade de serviço. No exemplo a seguir, um SP chamado **ml-auth** é criado:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    A saída será um JSON semelhante ao seguinte. Anote os campos `clientId`, `clientSecret` e `tenantId`, pois você precisará deles para outras etapas neste artigo.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Recupere os detalhes da entidade de serviço usando o `clientId` valor retornado na etapa anterior:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    O JSON a seguir é um exemplo simplificado da saída do comando. Anote o campo `objectId`, pois você precisará do valor dele para a próxima etapa.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Permitir que o SP acesse seu espaço de trabalho do Azure Machine Learning. Você precisará do nome do workspace e do nome do grupo de recursos dele para os parâmetros `-w` e `-g`, respectivamente. Para o parâmetro `--user`, use o valor `objectId` da etapa anterior. O `--role` parâmetro permite que você defina a função de acesso para a entidade de serviço. No exemplo a seguir, o SP é atribuído à função de **proprietário** . 

    > [!IMPORTANT]
    > O acesso do proprietário permite que a entidade de serviço faça praticamente qualquer operação em seu espaço de trabalho. Ele é usado neste documento para demonstrar como conceder acesso; em um ambiente de produção, a Microsoft recomenda conceder a entidade de serviço o acesso mínimo necessário para executar a função para a qual você pretendia. Para obter informações sobre como criar uma função personalizada com o acesso necessário para seu cenário, consulte [gerenciar o acesso ao Azure Machine Learning espaço de trabalho](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Essa chamada não produz nenhuma saída em caso de êxito.

## <a name="configure-a-managed-identity"></a>Configurar uma identidade gerenciada

> [!IMPORTANT]
> A identidade gerenciada só tem suporte ao usar o SDK do Azure Machine Learning de uma máquina virtual do Azure ou com um cluster de computação Azure Machine Learning. O uso de uma identidade gerenciada com um cluster de computação está atualmente em versão prévia.

### <a name="managed-identity-with-a-vm"></a>Identidade gerenciada com uma VM

1. Habilite uma [identidade gerenciada atribuída pelo sistema para recursos do Azure na VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho e, em seguida, selecione __controle de acesso (iam)__, __Adicionar atribuição de função__ e selecione __máquina virtual__ na lista suspensa __atribuir acesso a__ . Por fim, selecione a identidade da VM.

1. Selecione a função a ser atribuída a essa identidade. Por exemplo, colaborador ou uma função personalizada. Para obter mais informações, consulte [controlar o acesso aos recursos](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Identidade gerenciada com cluster de computação

Para obter mais informações, consulte [Configurar a identidade gerenciada para cluster de computação](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Usar autenticação interativa

> [!IMPORTANT]
> A autenticação interativa usa seu navegador e requer cookies (incluindo cookies de terceiros). Se você tiver desabilitado cookies, poderá receber um erro como "não foi possível conectá-lo". Esse erro também pode ocorrer se você tiver habilitado a [autenticação multifator do Azure ad](../active-directory/authentication/concept-mfa-howitworks.md).

A maioria dos exemplos na documentação e nos exemplos usa a autenticação interativa. Por exemplo, ao usar o SDK, há duas chamadas de função que irão solicitar automaticamente um fluxo de autenticação baseado em interface do usuário:

* Chamar a função `from_config()` emitirá o prompt.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    A função `from_config()` procura um arquivo JSON com as informações de conexão de seu workspace.

* O uso do `Workspace` construtor para fornecer a assinatura, o grupo de recursos e as informações do espaço de trabalho também solicitará a autenticação interativa.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Se você tiver acesso a vários locatários, talvez seja necessário importar a classe e definir explicitamente qual é o locatário de destino. Chamar o construtor para `InteractiveLoginAuthentication` também solicitará que você faça logon de maneira semelhante às chamadas acima.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Ao usar o CLI do Azure, o `az login` comando é usado para autenticar a sessão da CLI. Para obter mais informações, consulte Introdução [ao CLI do Azure](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Se você estiver usando o SDK de um ambiente no qual você já autenticou interativamente usando o CLI do Azure, poderá usar a `AzureCliAuthentication` classe para se autenticar no espaço de trabalho usando as credenciais armazenadas em cache pela CLI:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para se autenticar no seu espaço de trabalho do SDK, usando uma entidade de serviço, use o `ServicePrincipalAuthentication` Construtor de classe. Use os valores que você obteve ao criar o provedor de serviços como os parâmetros. O parâmetro `tenant_id` é mapeado para `tenantId` acima, `service_principal_id` é mapeado para `clientId` e `service_principal_password` é mapeado para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Agora, a variável `sp` contém um objeto de autenticação que você usa diretamente no SDK. Em geral, é uma boa ideia armazenar as IDs/segredos usados acima em variáveis de ambiente, conforme mostrado no código a seguir. O armazenamento em variáveis de ambiente impede que as informações sejam verificadas acidentalmente em um repositório GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que são executados no Python e usam principalmente o SDK, você pode usar esse objeto no estado em que se encontra na maioria dos casos para autenticação. O código a seguir é autenticado em seu espaço de trabalho usando o objeto de autenticação que você criou.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Usar uma entidade de serviço do CLI do Azure

Você pode usar uma entidade de serviço para CLI do Azure comandos. Para obter mais informações, consulte [entrar usando uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Usar uma entidade de serviço com a API REST (versão prévia)

A entidade de serviço também pode ser usada para autenticar para a [API REST](/rest/api/azureml/) do Azure Machine Learning (versão prévia). Use o [fluxo de concessão de credenciais de cliente](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) do Azure Active Directory, que permite fazer chamadas de serviço a serviço para autenticação sem periféricos em fluxos de trabalho automatizados. Os exemplos são implementados com a [Biblioteca ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) no Python e no Node.js, mas você pode usar qualquer biblioteca de software livre com suporte para OpenID Connect 1.0.

> [!NOTE]
> A MSAL.js é uma biblioteca mais recente do que a ADAL, mas não é possível fazer a autenticação de serviço a serviço usando credenciais do cliente com a MSAL.js, pois ela é basicamente uma biblioteca do lado do cliente destinada à autenticação interativa/de interface do usuário vinculada a um usuário específico. É recomendável usar a ADAL, conforme mostrado abaixo, para criar fluxos de trabalho automatizados com a API REST.

#### <a name="nodejs"></a>Node.js

Use as etapas a seguir para gerar um token de autenticação usando o Node.js. Em seu ambiente, execute `npm install adal-node`. Em seguida, use `tenantId`, `clientId` e `clientSecret` da entidade de serviço criada nas etapas acima como valores para as variáveis correspondentes no script a seguir.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

A variável `tokenResponse` é um objeto que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. O trecho a seguir é uma resposta de exemplo.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use a propriedade `accessToken` para buscar o token de autenticação. Consulte a [Documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

#### <a name="python"></a>Python

Use as etapas a seguir para gerar um token de autenticação usando o Python. Em seu ambiente, execute `pip install adal`. Em seguida, use `tenantId`, `clientId` e `clientSecret` da entidade de serviço criada nas etapas acima como valores para as variáveis adequadas no script a seguir.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

A variável `token_response` é um dicionário que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por uma hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. O trecho a seguir é uma resposta de exemplo.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use `token_response["accessToken"]` para buscar o token de autenticação. Consulte a [Documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

#### <a name="java"></a>Java

No Java, recupere o token de portador usando uma chamada REST padrão:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

O código anterior teria que lidar com exceções e códigos de status diferentes de `200 OK` , mas mostra o padrão: 

- Use a ID do cliente e o segredo para validar que seu programa deve ter acesso
- Use sua ID de locatário para especificar onde `login.microsoftonline.com` deve estar olhando
- Usar Azure Resource Manager como a origem do token de autorização

## <a name="use-managed-identity-authentication"></a>Usar a autenticação de identidade gerenciada

Para autenticar no espaço de trabalho de uma VM ou cluster de computação configurado com uma identidade gerenciada, use a `MsiAuthentication` classe. O exemplo a seguir demonstra como usar essa classe para autenticar em um espaço de trabalho:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Próximas etapas

* [Como usar segredos no treinamento](how-to-use-secrets-in-runs.md).
* [Como configurar a autenticação para modelos implantados como um serviço Web](how-to-authenticate-web-service.md).
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).
