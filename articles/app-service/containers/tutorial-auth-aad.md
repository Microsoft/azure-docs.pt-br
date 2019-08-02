---
title: Autenticar e autorizar usuários de ponta a ponta no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar o recurso de autenticação e autorização do Serviço de Aplicativo de para proteger seus aplicativos do Serviço de Aplicativo em execução no Linux, incluindo o acesso a APIs remotas.
keywords: serviço de aplicativo, serviço de aplicativo do azure, authN, authZ, proteger, segurança, várias camadas, azure active directory, azure ad
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/26/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2c173da9bfb60f74b90a17f4f3c5ea6f930ca528
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705829"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure no Linux

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Além disso, o Serviço de Aplicativo tem suporte interno para [autenticação e autorização de usuário](../overview-authentication-authorization.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). Este tutorial mostra como proteger seus aplicativos com os recursos de autenticação e autorização do Serviço de Aplicativo. Ele usa um aplicativo ASP.NET Core com um front-end Angular.js, mas é usado apenas como exemplo. O recurso de autorização e autenticação do Serviço de Aplicativo dá suporte a tempos de execução de todas as linguagens, e você pode aprender como aplicá-lo à sua linguagem preferida seguindo o tutorial.

O tutorial usa o aplicativo de exemplo para mostrar como proteger um aplicativo autocontido (em [Habilitar autenticação e autorização para o aplicativo de back-end](#enable-authentication-and-authorization-for-back-end-app)).

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

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instalar o Git](https://git-scm.com/).
* [Instalar o .NET Core 2.0](https://www.microsoft.com/net/core/).

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Implantar aplicativos no Azure

Nesta etapa, implante o projeto em dois aplicativos do Serviço de Aplicativo. Um é o aplicativo de front-end e o outro é o aplicativo de back-end.

### <a name="create-azure-resources"></a>Criar recursos do Azure

No Cloud Shell, execute os comandos a seguir para criar dois aplicativos do Serviço de Aplicativo. Substitua _&lt;front\_end\_app\_name>_ e  _&lt;back\_end\_app\_name>_ por dois nomes de aplicativo exclusivos (os caracteres válidos são `a-z`, `0-9` e `-`). Para obter mais informações sobre cada comando, confira [Criar um aplicativo .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku B1 --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Salve as URLs dos remotos do Git para os aplicativos de front-end e back-end que são mostradas na saída de `az webapp create`.
>

### <a name="configure-cors"></a>Configurar o CORS

Essa etapa não está relacionada ao recurso de autenticação e autorização. No entanto, você precisa dele posteriormente ao [chamar a API de back-end do código do navegador de front-end](#call-api-securely-from-browser-code), de modo que seu navegador permita que as chamadas de API do seu aplicativo Angular.js entre domínios. Agora, o Serviço de Aplicativo no Linux dá suporte à funcionalidade CORS, assim como [seu equivalente no Windows](../app-service-web-tutorial-rest-api.md#add-cors-functionality).

No repositório local, abra o arquivo _Startup.cs_. No método `ConfigureServices(IServiceCollection services)`, adicione a linha de código a seguir:

```csharp
services.AddCors();
```

No método `Configure(IApplicationBuilder app)`, adicione a seguinte linha de código ao início (substitua *\<front_end_app_name >* ):

```csharp
app.UseCors(builder =>
    builder.WithOrigins("http://<front_end_app_name>.azurewebsites.net"));
```

Salve suas alterações. De volta na _janela do terminal local_, execute os seguintes comandos para confirmar as alterações no repositório do Git.

```bash
git add .
git commit -m "add CORS to back end"
```

> [!NOTE]
> Não se preocupe em compartilhar esse código entre os aplicativos front-end e back-end. Ele não tem nenhum efeito de CORS no aplicativo front-end.
> 

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Na janela do terminal local, execute os comandos do Git a seguir para implantar o aplicativo de back-end. Substitua _&lt;deploymentLocalGitUrl-of-back-end-app>_ pela URL do Git remoto que você salvou de [Criar recursos do Azure](#create-azure-resources). Quando solicitado a fornecer credenciais pelo Gerenciador de Credenciais do Git, insira [suas credenciais de implantação](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), não as credenciais usadas para fazer logon no portal do Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Na janela do terminal local, execute os comandos do Git a seguir para implantar o mesmo código no aplicativo front-end. Substitua _&lt;deploymentLocalGitUrl-of-front-end-app>_ pela URL do Git remoto que você salvou de [Criar recursos do Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-apps"></a>Navegar para os aplicativos do Azure

Navegue até as URLs a seguir em um navegador e veja os dois aplicativos funcionando.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Se seu aplicativo foi reiniciado, talvez você tenha notado que os novos dados foram apagados. Esse comportamento é por design porque o aplicativo ASP.NET Core de exemplo usa um banco de dados na memória.
>
>

## <a name="call-back-end-api-from-front-end"></a>Chamar a API de back-end do front-end

Nesta etapa, aponte o código do servidor de front-end do aplicativo para acessar a API de back-end. Posteriormente, você habilitará o acesso autenticado do front-end para o back-end.

### <a name="modify-front-end-code"></a>Modificar o código de front-end

No repositório local, abra _Controllers/TodoController.cs_. No início da classe `TodoController`, adicione as seguintes linhas e substitua  _&lt;back\_end\_app\_name>_ pelo nome do seu aplicativo de back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Localize o método `GetAll()` e substitua o código entre chaves por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

A primeira linha faz uma chamada `GET /api/Todo` para o aplicativo de API de back-end.

Em seguida, localize o método `GetById(long id)` e substitua o código entre chaves por:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

A primeira linha faz uma chamada `GET /api/Todo/{id}` para o aplicativo de API de back-end.

Em seguida, localize o método `Create([FromBody] TodoItem item)` e substitua o código entre chaves por:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

A primeira linha faz uma chamada `POST /api/Todo` para o aplicativo de API de back-end.

Em seguida, localize o método `Update(long id, [FromBody] TodoItem item)` e substitua o código entre chaves por:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

A primeira linha faz uma chamada `PUT /api/Todo/{id}` para o aplicativo de API de back-end.

Em seguida, localize o método `Delete(long id)` e substitua o código entre chaves por:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

A primeira linha faz uma chamada `DELETE /api/Todo/{id}` para o aplicativo de API de back-end.

Salve todas as suas alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Verificar as alterações

Navegue até `http://<front_end_app_name>.azurewebsites.net` e adicione alguns itens, como `from front end 1` e `from front end 2`.

Navegue até `http://<back_end_app_name>.azurewebsites.net` para ver os itens adicionados do aplicativo de front-end. Além disso, adicione alguns itens, como `from back end 1` e `from back end 2` e atualize o aplicativo de front-end para ver se ele reflete as alterações.

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Configurar autenticação

Nesta etapa, habilite o recurso de autenticação e autorização para os dois aplicativos. Você também pode configurar o aplicativo front-end para gerar um token de acesso que pode ser usado para fazer chamadas autenticadas para o aplicativo de back-end.

Use o Azure Active Directory como o provedor de identidade. Para saber mais, confira [Configurar a autenticação do Azure Active Directory para seu aplicativo do Serviços de Aplicativo](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Habilitar autenticação e autorização para o aplicativo de back-end

No [portal do Azure](https://portal.azure.com), abra a página de gerenciamento do aplicativo de back-end clicando no menu à esquerda: **Grupos de recursos** > **myAuthResourceGroup** >  _\<back\_end\_app\_name>_ .

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/portal-navigate-back-end.png)

No menu esquerdo do aplicativo de back-end, clique em **Autenticação/Autorização** e habilite a autenticação do Serviço de Aplicativo clicando em **Ativo**.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Entrar com o Azure Active Directory**.

Em **Provedores de Autenticação**, clique em **Azure Active Directory** 

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/configure-auth-back-end.png)

Clique em **Expresso**, aceite as configurações padrão para criar um novo aplicativo AD e clique em **OK**.

Na página **Autenticação/Autorização**, clique em **Salvar**. 

Quando você vir a notificação com a mensagem `Successfully saved the Auth Settings for <back_end_app_name> App`, atualize a página.

Clique em **Azure Active Directory** novamente e clique em **Gerenciar Aplicativo**.

Na página de gerenciamento do aplicativo AD, copie a **ID do aplicativo** em um bloco de notas. Você precisará desse valor mais tarde.

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Habilitar autenticação e autorização para o aplicativo de front-end

Siga as mesmas etapas para o aplicativo de front-end, mas ignore a última etapa. Não é necessário ter a **ID do aplicativo** para o aplicativo de front-end. Mantenha a página **Configurações do Azure Active Directory** aberta.

Se desejar, navegue até `http://<front_end_app_name>.azurewebsites.net`. Ele agora deve direcioná-lo a uma página de entrada. Depois de entrar, você ainda não conseguirá acessar os dados do aplicativo de back-end porque precisa fazer três coisas:

- Conceder ao front-end acesso ao back-end
- Configurar o Serviço de Aplicativo para retornar um token utilizável
- Usar o token em seu código

> [!TIP]
> Se você encontrar erros e reconfigurar as configurações de autenticação/autorização do aplicativo, é possível que os tokens no repositório de tokens não sejam regenerados com as novas configurações. Para fazer com que os tokens sejam regenerados, saia e entre novamente no aplicativo. Uma maneira fácil de fazer isso é usar o navegador em modo privado e fechar e reabrir o navegador em modo privado depois de alterar as configurações nos aplicativos.

### <a name="grant-front-end-app-access-to-back-end"></a>Conceder ao aplicativo de front-end acesso ao back-end

Agora que você habilitou o recurso de autenticação e autorização para ambos os aplicativos, cada um tem suporte de um aplicativo AD. Nesta etapa, você deve fornecer as permissões ao aplicativo de front-end para acessar o back-end em nome do usuário. (Tecnicamente, você concede ao _aplicativo AD_ de front-end as permissões para acessar o _aplicativo AD_ de back-end em nome do usuário.)

Neste ponto, você deve estar na página **Configurações do Azure Active Directory** do aplicativo de front-end. Caso contrário, volte para essa página. 

Clique em **Gerenciar Permissões** > **Adicionar** > **Selecionar uma API**.

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/add-api-access-front-end.png)

Na página **Selecionar uma API**, digite o nome do aplicativo AD do seu aplicativo de back-end, que é o mesmo do aplicativo de back-end por padrão. Selecione-o na lista e clique em **Selecionar**.

Marque a caixa de seleção ao lado **Acessar  _&lt;nome\_aplicativo\_AD>_** . Clique em **Selecionar** > **Concluído**.

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurar o Serviço de Aplicativo para retornar um token de acesso utilizável

Agora, o aplicativo de front-end tem as permissões necessárias. Nesta etapa, configure o recurso de autenticação e autorização do Serviço de Aplicativo para fornecer um token de acesso usado para acessar o back-end. Para esta etapa, você precisa da ID do aplicativo de back-end copiada de [Habilitar autenticação e autorização para o aplicativo de back-end](#enable-authentication-and-authorization-for-back-end-app).

Entre no [Azure Resource Explorer](https://resources.azure.com). Na parte superior da página, clique em **Leitura/Gravação** para permitir a edição dos recursos do Azure.

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/resources-enable-write.png)

No navegador à esquerda, clique em **assinaturas** >  **_&lt;sua\_assinatura >_**   >  **resourceGroups** > **myAuthResourceGroup** > **provedores** > **Microsoft.Web**  >  **sites** >  **_\<nome\_aplicativo\_front\_end>_**   >  **config** > **authsettings**.

No modo de exibição **authsettings**, clique em **Editar**. Defina `additionalLoginParams` para a cadeia de caracteres JSON a seguir usando a ID do aplicativo copiada. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![API do ASP.NET Core em execução no Serviço de Aplicativo do Azure](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Salve suas configurações clicando em **PUT**.

Os aplicativos estão configurados. O front-end está pronto para acessar o back-end com um token de acesso apropriado.

Para saber mais sobre como configurar isso para outros provedores, confira [Atualizar tokens de acesso](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Chamada API com segurança do código do servidor

Nesta etapa, habilite o código do servidor modificado anteriormente para fazer chamadas autenticadas à API de back-end.

Seu aplicativo de front-end agora tem a permissão necessária e também adiciona a ID do aplicativo do back-end aos parâmetros de logon. Assim, ele pode obter um token de acesso para autenticação no aplicativo de back-end. O Serviço de Aplicativo fornece esse token para o código do servidor inserindo um cabeçalho `X-MS-TOKEN-AAD-ACCESS-TOKEN` em cada solicitação autenticada (confira [Recuperar tokens no código do aplicativo](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!NOTE]
> Esses cabeçalhos são injetados em todas as linguagens com suporte. Acesse-os usando o padrão de cada linguagem.

No repositório local, abra _Controllers/TodoController.cs_ novamente. No construtor `TodoController(TodoContext context)`, adicione o seguinte código:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["x-ms-token-aad-access_token"]);
}
```

Esse código adiciona o cabeçalho HTTP padrão `Authorization: Bearer <access_token>` a todas as chamadas à API remotas. No pipeline de execução da solicitação MVC ASP.NET Core, `OnActionExecuting` é executado logo antes do respectivo método de ação (como `GetAll()`), de modo que cada uma das suas chamadas à API de saída apresentem o token de acesso.

Salve todas as suas alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Entre em `http://<front_end_app_name>.azurewebsites.net` novamente. Na página do contrato de uso do dados do usuário, clique em **Aceitar**.

Agora você deve ser capaz de criar, ler, atualizar e excluir dados do aplicativo back-end como fazia antes. A única diferença é que ambos os aplicativos agora são protegidos pelo recurso de autenticação e autorização, do Serviço de Aplicativo, incluindo as chamadas entre serviços.

Parabéns! O código do servidor agora está acessando os dados de back-end em nome do usuário autenticado.

## <a name="call-api-securely-from-browser-code"></a>Chamar API do código do navegador com segurança

Nesta etapa, aponte o aplicativo Angular.js de front-end para a API de back-end. Dessa forma, você aprenderá a recuperar o token de acesso e a usá-lo para fazer chamadas à API para o aplicativo de back-end.

Embora o código do servidor tenha acesso a cabeçalhos de solicitação, o código do cliente pode acessar `GET /.auth/me` para obter os mesmos tokens de acesso (confira [Recuperar tokens no código do aplicativo](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!TIP]
> Esta seção usa os métodos HTTP padrão para demonstrar as chamadas HTTP seguras. No entanto, você pode usar [ADAL (Biblioteca de Autenticação do Active Directory) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) a fim de simplificar o padrão do aplicativo Angular.js.
>

### <a name="point-angularjs-app-to-back-end-api"></a>Apontar aplicativo Angular.js para a API de back-end

No repositório local, abra _wwwroot/index.html_.

Na linha 51, defina a variável `apiEndpoint` como a URL do aplicativo de back-end (`http://<back_end_app_name>.azurewebsites.net`). Substitua _\<back\_end\_app\_name>_ pelo nome do seu aplicativo no Serviço de Aplicativo.

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

A nova alteração adiciona o mapeamento `revolve` que chama `/.auth/me` e define o token de acesso. Ela verifica se você tem o token de acesso antes de criar uma instância do controlador `todoListCtrl`. Dessa forma, todas as chamadas à API pelo controlador incluirão o token.

### <a name="deploy-updates-and-test"></a>Implantar atualizações e testar

Salve todas as suas alterações. Na janela do terminal local, implante as alterações no aplicativo front-end com os seguintes comandos do Git:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navegue até `http://<front_end_app_name>.azurewebsites.net` novamente. Agora você deve poder criar, ler, atualizar e excluir dados do aplicativo de back-end diretamente no aplicativo Angular.js.

Parabéns! O código do cliente agora está acessando os dados de back-end em nome do usuário autenticado.

## <a name="clean-up-resources"></a>Limpar recursos

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
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
