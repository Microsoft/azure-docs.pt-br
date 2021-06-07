---
title: 'Tutorial: Autenticar usuários E2E'
description: Saiba como usar a autenticação e a autorização do Serviço de Aplicativo para proteger seus aplicativos do Serviço de Aplicativo de ponta a ponta, incluindo o acesso a APIs remotas.
keywords: serviço de aplicativo, serviço de aplicativo do azure, authN, authZ, proteger, segurança, várias camadas, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e35a4abbcaf0cd2298d55c06de0ecfbef0dff354
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765752"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure

::: zone pivot="platform-windows"  

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Além disso, o Serviço de Aplicativo tem suporte interno para [autenticação e autorização de usuário](overview-authentication-authorization.md). Este tutorial mostra como proteger seus aplicativos com os recursos de autenticação e autorização do Serviço de Aplicativo. Ele usa um aplicativo ASP.NET Core com um front-end Angular.js como exemplo. O recurso de autorização e autenticação do Serviço de Aplicativo dá suporte a runtimes de todas as linguagens, e você pode aprender como aplicá-lo à sua linguagem preferida seguindo o tutorial.

::: zone-end

::: zone pivot="platform-linux"

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Além disso, o Serviço de Aplicativo tem suporte interno para [autenticação e autorização de usuário](overview-authentication-authorization.md). Este tutorial mostra como proteger seus aplicativos com os recursos de autenticação e autorização do Serviço de Aplicativo. Ele usa um aplicativo ASP.NET Core com um front-end Angular.js como exemplo. O recurso de autorização e autenticação do Serviço de Aplicativo dá suporte a runtimes de todas as linguagens, e você pode aprender como aplicá-lo à sua linguagem preferida seguindo o tutorial.

::: zone-end

![Autenticação e autorização simples](./media/tutorial-auth-aad/simple-auth.png)

Ele também mostra como proteger um aplicativo de várias camadas acessando uma API de back-end protegida em nome do usuário autenticado, tanto [do código do servidor](#call-api-securely-from-server-code) quanto [do código do navegador](#call-api-securely-from-browser-code).

![Autenticação e Autorização avançadas](./media/tutorial-auth-aad/advanced-auth.png)

Essas são apenas alguns dos possíveis cenários de autenticação e autorização no Serviço de Aplicativo. 

Veja uma lista mais completa do que você vai aprender no tutorial:

> [!div class="checklist"]
> * Habilitar autorização e autenticação internas
> * Proteger aplicativos contra solicitações não autenticadas
> * Usar o Azure Active Directory como o provedor de identidade
> * Acessar um aplicativo remoto em nome do usuário conectado
> * Proteger chamadas entre serviços com autenticação de token
> * Usar os tokens de acesso do código do servidor
> * Usar os tokens de acesso do código do cliente (navegador)

Você pode seguir as etapas deste tutorial no macOS, no Linux e no Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instale o SDK do .NET Core 3.1 mais recente</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Criar um aplicativo .NET Core local

Nesta etapa, você configura o projeto .NET Core local. Use o mesmo projeto para implantar um aplicativo de API de back-end e um aplicativo Web de front-end.

### <a name="clone-and-run-the-sample-application"></a>Clonar e executar o aplicativo de exemplo

Execute os comandos a seguir para clonar o repositório de exemplo e executá-lo.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Navegue até `http://localhost:5000` e tente adicionar, editar e remover itens de tarefas pendentes. 

![API do ASP.NET Core em execução localmente](./media/tutorial-auth-aad/local-run.png)

Para parar o ASP.NET Core a qualquer momento, pressione `Ctrl+C` no terminal.

## <a name="deploy-apps-to-azure"></a>Implantar aplicativos no Azure

Nesta etapa, implante o projeto em dois aplicativos do Serviço de Aplicativo. Um é o aplicativo de front-end e o outro é o aplicativo de back-end.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Criar recursos do Azure

::: zone pivot="platform-windows"  

No Cloud Shell, execute os comandos a seguir para criar dois aplicativos Web Windows. Substitua _\<front-end-app-name>_ e _\<back-end-app-name>_ por dois nomes de aplicativo exclusivos globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). Para saber mais sobre cada comando, confira [API RESTful com CORS no Serviço de Aplicativo do Azure](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

No Cloud Shell, execute os comandos a seguir para criar dois aplicativos Web. Substitua _\<front-end-app-name>_ e _\<back-end-app-name>_ por dois nomes de aplicativo exclusivos globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). Para obter mais informações sobre cada comando, confira [Criar um aplicativo .NET Core no Serviço de Aplicativo do Azure](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Salve as URLs dos remotos do Git para os aplicativos de front-end e back-end que são mostradas na saída de `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Voltando à _janela do terminal local_, execute os comandos do Git a seguir para implantar o aplicativo de back-end. Substitua _\<deploymentLocalGitUrl-of-back-end-app>_ pela URL do Git remoto que você salvou em [Criar recursos do Azure](#create-azure-resources). Quando precisar fornecer credenciais pelo Gerenciador de Credenciais do Git, insira [suas credenciais de implantação](deploy-configure-credentials.md), não as credenciais usadas para entrar no portal do Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Na janela do terminal local, execute os comandos do Git a seguir para implantar o mesmo código no aplicativo front-end. Substitua _\<deploymentLocalGitUrl-of-front-end-app>_ pela URL do Git remoto que você salvou em [Criar recursos do Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Navegar para os aplicativos

Navegue até as URLs a seguir em um navegador e veja os dois aplicativos funcionando.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Captura de tela de um Exemplo de API REST do Serviço de Aplicativo do Azure em uma janela do navegador, que mostra um aplicativo de lista de Tarefas pendentes.":::

> [!NOTE]
> Se seu aplicativo foi reiniciado, talvez você tenha notado que os novos dados foram apagados. Esse comportamento é por design porque o aplicativo ASP.NET Core de exemplo usa um banco de dados na memória.
>
>

## <a name="call-back-end-api-from-front-end"></a>Chamar a API de back-end do front-end

Nesta etapa, aponte o código do servidor de front-end do aplicativo para acessar a API de back-end. Posteriormente, você habilitará o acesso autenticado do front-end para o back-end.

### <a name="modify-front-end-code"></a>Modificar o código de front-end

No repositório local, abra _Controllers/TodoController.cs_. No início da classe `TodoController`, adicione as seguintes linhas e substitua _\<back-end-app-name>_ pelo nome do seu aplicativo de back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Localize o método que está decorado com `[HttpGet]` e substitua o código entre chaves por:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

A primeira linha faz uma chamada `GET /api/Todo` para o aplicativo de API de back-end.

Em seguida, localize o método que está decorado com `[HttpGet("{id}")]` e substitua o código entre chaves por:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

A primeira linha faz uma chamada `GET /api/Todo/{id}` para o aplicativo de API de back-end.

Em seguida, localize o método que está decorado com `[HttpPost]` e substitua o código entre chaves por:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

A primeira linha faz uma chamada `POST /api/Todo` para o aplicativo de API de back-end.

Em seguida, localize o método que está decorado com `[HttpPut("{id}")]` e substitua o código entre chaves por:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

A primeira linha faz uma chamada `PUT /api/Todo/{id}` para o aplicativo de API de back-end.

Em seguida, localize o método que está decorado com `[HttpDelete("{id}")]` e substitua o código entre chaves por:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

A primeira linha faz uma chamada `DELETE /api/Todo/{id}` para o aplicativo de API de back-end.

Salve todas as alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Verificar as alterações

Navegue até `http://<front-end-app-name>.azurewebsites.net` e adicione alguns itens, como `from front end 1` e `from front end 2`.

Navegue até `http://<back-end-app-name>.azurewebsites.net` para ver os itens adicionados do aplicativo de front-end. Além disso, adicione alguns itens, como `from back end 1` e `from back end 2` e atualize o aplicativo de front-end para ver se ele reflete as alterações.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Captura de tela de um Exemplo de API REST do Serviço de Aplicativo do Azure em uma janela do navegador, que mostra um aplicativo de lista de Tarefas pendentes com itens adicionados no aplicativo de front-end.":::

## <a name="configure-auth"></a>Configurar autenticação

Nesta etapa, habilite o recurso de autenticação e autorização para os dois aplicativos. Você também pode configurar o aplicativo front-end para gerar um token de acesso que pode ser usado para fazer chamadas autenticadas para o aplicativo de back-end.

Use o Azure Active Directory como o provedor de identidade. Para saber mais, confira [Configurar a autenticação do Azure Active Directory para seu aplicativo do Serviços de Aplicativo](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Habilitar autenticação e autorização para o aplicativo de back-end

No menu do [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** ou pesquise e selecione *Grupos de recursos* em qualquer página.

Clique em **grupos de recursos** e selecione o grupo de recursos. Em **Visão geral**, selecione a página de gerenciamento do aplicativo de back-end.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Captura de tela da janela Grupos de recursos que mostra a Visão geral de um grupo de recursos de exemplo e uma página de gerenciamento do aplicativo de back-end selecionada.":::

No menu esquerdo do aplicativo de back-end, selecione **Autenticação/Autorização** e habilite a autenticação do Serviço de Aplicativo selecionando **Ativo**.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Entrar com o Azure Active Directory**.

Em **Provedores de Autenticação**, selecione **Azure Active Directory**.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Captura de tela do menu à esquerda no aplicativo de back-end que mostra a Autenticação/Autorização selecionada e as configurações selecionadas no menu à direita.":::

Selecione **Expresso**, aceite as configurações padrão para criar um novo aplicativo AD e selecione **OK**.

Na página **Autenticação/Autorização**, selecione **Salvar**.

Quando você vir a notificação com a mensagem `Successfully saved the Auth Settings for <back-end-app-name> App`, atualize a página do portal.

Selecione **Azure Active Directory** novamente e selecione **Aplicativo Azure AD**.

Copie a **ID do Cliente** do aplicativo do Azure AD para um bloco de notas. Você precisará desse valor mais tarde.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Captura de tela da janela Configurações do Azure Active Directory que mostra o Aplicativo Azure AD; e a janela Aplicativos Azure AD que mostra a ID do Cliente a ser copiada.":::

Se parar por aqui, você terá um aplicativo autossuficiente que já está protegido pela autenticação e pela autorização do Serviço de Aplicativo. As seções restantes mostram como proteger uma solução de vários aplicativos "levando" o usuário autenticado do front-end para o back-end. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Habilitar autenticação e autorização para o aplicativo de front-end

Siga as mesmas etapas para o aplicativo de front-end, mas ignore a última etapa. Não é necessário ter a ID do cliente para o aplicativo de front-end.

Se desejar, navegue até `http://<front-end-app-name>.azurewebsites.net`. Ele agora deve direcioná-lo a uma página de entrada segura. Depois de entrar, *você ainda não pode acessar os dados do aplicativo de back-end*, pois o aplicativo de back-end agora requer a conexão ao Azure Active Directory do aplicativo de front-end. Você precisa fazer três coisas:

- Conceder ao front-end acesso ao back-end
- Configurar o Serviço de Aplicativo para retornar um token utilizável
- Usar o token em seu código

> [!TIP]
> Se você encontrar erros e reconfigurar as configurações de autenticação/autorização do aplicativo, é possível que os tokens no repositório de tokens não sejam regenerados com as novas configurações. Para fazer com que os tokens sejam regenerados, saia e entre novamente no aplicativo. Uma maneira fácil de fazer isso é usar o navegador em modo privado e fechar e reabrir o navegador em modo privado depois de alterar as configurações nos aplicativos.

### <a name="grant-front-end-app-access-to-back-end"></a>Conceder ao aplicativo de front-end acesso ao back-end

Agora que você habilitou o recurso de autenticação e autorização para ambos os aplicativos, cada um tem suporte de um aplicativo AD. Nesta etapa, você deve fornecer as permissões ao aplicativo de front-end para acessar o back-end em nome do usuário. (Tecnicamente, você concede ao _aplicativo AD_ de front-end as permissões para acessar o _aplicativo AD_ de back-end em nome do usuário.)

No menu [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** ou pesquise e selecione *Azure Active Directory* em qualquer página.

Selecione **Registros de aplicativo** > **Aplicativos próprios** > **Exibir todos os aplicativos neste diretório**. Selecione o nome do aplicativo front-end e, em seguida, **permissões de API**.

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Captura de tela da janela Microsoft – Registros de aplicativo com Aplicativos próprios, um nome do aplicativo de front-end e permissões de API selecionados.":::

Selecione **Adicionar uma permissão** e, em seguida, selecione **APIs que a minha organização usa** >  **\<back-end-app-name>** .

Na página **Solicitar permissões de API** para o aplicativo de back-end, selecione **Permissões delegadas** e **user_impersonation**, então selecione **Adicionar permissões**.

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Captura de tela da página Solicitar permissões de API que mostra as permissões Delegadas, user_impersonation e o botão Adicionar permissão selecionado.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurar o Serviço de Aplicativo para retornar um token de acesso utilizável

O aplicativo de front-end agora tem as permissões necessárias para acessar o aplicativo de back-end como o usuário conectado. Nesta etapa, configure o recurso de autenticação e autorização do Serviço de Aplicativo para fornecer um token de acesso usado para acessar o back-end. Para esta etapa, você precisa da ID do cliente de back-end copiada de [Habilitar autenticação e autorização para o aplicativo de back-end](#enable-authentication-and-authorization-for-back-end-app).

Navegue até [Azure Resource Explorer](https://resources.azure.com) e usando, a árvore de recursos, localize seu aplicativo Web front-end.

O [Azure Resource Explorer](https://resources.azure.com) agora está aberto com seu aplicativo de front-end selecionado na árvore de recursos. Na parte superior da página, clique em **Leitura/Gravação** para permitir a edição dos recursos do Azure.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Captura de tela dos botões Somente Leitura e Leitura/Gravação na parte superior da página do Azure Resource Explorer, com o botão Leitura/Gravação selecionado.":::

No navegador esquerdo, faça uma busca detalhada até **config** > **authsettings**.

No modo de exibição **authsettings**, clique em **Editar**. Defina `additionalLoginParams` para a cadeia de caracteres JSON a seguir usando a ID do cliente copiada. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Captura de tela de um exemplo de código no modo de exibição authsettings que mostra a cadeia de caracteres additionalLoginParams com um exemplo de uma ID de cliente.":::

Salve suas configurações clicando em **PUT**.

Os aplicativos estão configurados. O front-end está pronto para acessar o back-end com um token de acesso apropriado.

Para saber mais sobre como configurar o token de acesso para outros provedores, confira [Atualizar tokens do provedor de identidade](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Chamada API com segurança do código do servidor

Nesta etapa, habilite o código do servidor modificado anteriormente para fazer chamadas autenticadas à API de back-end.

Seu aplicativo de front-end agora tem a permissão necessária e também adiciona a ID do cliente do back-end aos parâmetros de logon. Assim, ele pode obter um token de acesso para autenticação no aplicativo de back-end. O Serviço de Aplicativo fornece esse token para o código do servidor inserindo um cabeçalho `X-MS-TOKEN-AAD-ACCESS-TOKEN` em cada solicitação autenticada (confira [Recuperar tokens no código do aplicativo](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Esses cabeçalhos são injetados em todas as linguagens com suporte. Acesse-os usando o padrão de cada linguagem.

No repositório local, abra _Controllers/TodoController.cs_ novamente. No construtor `TodoController(TodoContext context)`, adicione o seguinte código:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Esse código adiciona o cabeçalho HTTP padrão `Authorization: Bearer <access-token>` a todas as chamadas à API remotas. No pipeline de execução da solicitação MVC ASP.NET Core, `OnActionExecuting` é executado logo antes da respectiva ação, de modo que cada uma das suas chamadas à API de saída apresentem o token de acesso.

Salve todas as alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Entre em `https://<front-end-app-name>.azurewebsites.net` novamente. Na página do contrato de uso do dados do usuário, clique em **Aceitar**.

Agora você deve ser capaz de criar, ler, atualizar e excluir dados do aplicativo back-end como fazia antes. A única diferença é que ambos os aplicativos agora são protegidos pelo recurso de autenticação e autorização, do Serviço de Aplicativo, incluindo as chamadas entre serviços.

Parabéns! O código do servidor agora está acessando os dados de back-end em nome do usuário autenticado.

## <a name="call-api-securely-from-browser-code"></a>Chamar API do código do navegador com segurança

Nesta etapa, aponte o aplicativo Angular.js de front-end para a API de back-end. Dessa forma, você aprenderá a recuperar o token de acesso e a usá-lo para fazer chamadas à API para o aplicativo de back-end.

Embora o código do servidor tenha acesso a cabeçalhos de solicitação, o código do cliente pode acessar `GET /.auth/me` para obter os mesmos tokens de acesso (confira [Recuperar tokens no código do aplicativo](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> Esta seção usa os métodos HTTP padrão para demonstrar as chamadas HTTP seguras. No entanto, você pode usar a [Biblioteca de Autenticação do Active Directory para JavaScript da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-js) para simplificar o padrão do aplicativo Angular.js.
>

### <a name="configure-cors"></a>Configurar o CORS

No Cloud Shell, habilite o CORS para URL do cliente usando o comando [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add). Substitua os espaços reservados _\<back-end-app-name>_ e _\<front-end-app-name>_ .

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Essa etapa não está relacionada ao recurso de autenticação e autorização. No entanto, você precisa dela para que seu navegador permita chamadas à API do seu aplicativo Angular.js entre domínios. Para saber mais, confira [Adicionar funcionalidade CORS](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Apontar aplicativo Angular.js para a API de back-end

No repositório local, abra _wwwroot/index.html_.

Na linha 51, defina a variável `apiEndpoint` como a URL HTTPS do aplicativo de back-end (`https://<back-end-app-name>.azurewebsites.net`). Substitua _\<back-end-app-name>_ pelo nome do aplicativo no Serviço de Aplicativo.

No repositório local, abra _wwwroot/app/scripts/todoListSvc.js_ e veja se `apiEndpoint` foi acrescentado a todas as chamadas à API. Seu aplicativo Angular.js agora está chamando as APIs de back-end. 

### <a name="add-access-token-to-api-calls"></a>Adicionar token de acesso a chamadas à API

Em _wwwroot/app/scripts/todoListSvc.js_, acima da lista de chamadas à API (acima da linha `getItems : function(){`), adicione à lista a seguinte função:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Essa função é chamada para definir o cabeçalho `Authorization` padrão com o token de acesso. Você a chamará na próxima etapa.

No repositório local, abra _wwwroot/app/scripts/app.js_ e localize o código abaixo:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Substitua todo o bloco de código pelo seguinte código:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

A nova alteração adiciona o mapeamento `resolve` que chama `/.auth/me` e define o token de acesso. Ela verifica se você tem o token de acesso antes de criar uma instância do controlador `todoListCtrl`. Dessa forma, todas as chamadas à API pelo controlador incluirão o token.

### <a name="deploy-updates-and-test"></a>Implantar atualizações e testar

Salve todas as alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navegue até `https://<front-end-app-name>.azurewebsites.net` novamente. Agora você deve poder criar, ler, atualizar e excluir dados do aplicativo de back-end diretamente no aplicativo Angular.js.

Parabéns! O código do cliente agora está acessando os dados de back-end em nome do usuário autenticado.

## <a name="when-access-tokens-expire"></a>Quando os tokens de acesso expiram

O token de acesso expira após algum tempo. Para obter informações sobre como atualizar seus tokens de acesso sem exigir que os usuários autentiquem novamente no aplicativo, confira [Atualizar tokens do provedor de identidade](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando no Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Esse comando pode demorar um pouco para ser executado.

<a name="next"></a>
## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Habilitar autorização e autenticação internas
> * Proteger aplicativos contra solicitações não autenticadas
> * Usar o Azure Active Directory como o provedor de identidade
> * Acessar um aplicativo remoto em nome do usuário conectado
> * Proteger chamadas entre serviços com autenticação de token
> * Usar os tokens de acesso do código do servidor
> * Usar os tokens de acesso do código do cliente (navegador)

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)
