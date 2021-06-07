---
title: Criar, implantar um aplicativo de vários serviços na Malha do Service Fabric
description: Neste tutorial, você cria um aplicativo de malha de vários serviços do Azure Service Fabric consistindo em um site do ASP.NET Core que se comunica com um serviço Web de back-end, depura-o localmente e o publica no Azure.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: b0bdb3c09aead812e1c16f4d0d17aae58e141809
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626786"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Tutorial: Criar, depurar, implantar e atualizar um aplicativo de vários serviços para Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a primeira parte de uma série. Você aprenderá como usar o Visual Studio para criar um aplicativo de Malha do Azure Service Fabric com um front-end da Web do ASP.NET e um serviço de back-end da API Web do ASP.NET Core. Em seguida, você depurará o aplicativo no cluster de desenvolvimento local. Você publicará o aplicativo no Azure e, em seguida, fará alterações de configuração e código e atualizará o aplicativo. Por fim, você limpará os recursos do Azure não utilizados para que você não seja cobrado pelo que não está usando.

Quando tiver terminado, você terá passado pela maioria das fases do gerenciamento de ciclo de vida do aplicativo e terá criado um aplicativo que demonstra uma chamada de serviço a serviço em um aplicativo de Malha do Service Fabric.

Se você não quiser criar o aplicativo de tarefas pendentes manualmente, [baixe o código-fonte](https://github.com/azure-samples/service-fabric-mesh) do aplicativo concluído e vá direto para [Depurar o aplicativo localmente](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Use o Visual Studio para criar um aplicativo de Malha do Service Fabric com um front-end Web do ASP.NET.
> * Crie um modelo para representar itens de tarefas pendentes.
> * Crie um serviço de back-end e recupere dados dele.
> * Adicione um controlador e um DataContext como parte desse padrão de Controlador de Exibição de Modelo para o serviço de back-end.
> * Crie uma página da Web para exibir itens de tarefas pendentes.
> * Crie variáveis de ambiente que identificam o serviço de back-end

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um aplicativo da Malha do Service Fabric no Visual Studio
> * [Depurar um aplicativo da Malha do Service Fabric em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implantar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos da Malha do Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Configure o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), o que inclui a instalação do runtime do Service Fabric, do SDK, do Docker e do Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Crie um projeto de Malha do Service Fabric no Visual Studio

Abra o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto...**

No diálogo **Novo Projeto**, caixa **Pesquisar** na parte superior, digite `mesh`. Selecione o modelo **Aplicativo de Malha do Service Fabric**. (Se não for possível ver o modelo, verifique se você instalou o SDK de Malha e a versão prévia das ferramentas do VS, conforme descrito em [Configurar o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

Na caixa **Nome**, digite `todolistapp` e, na caixa **Local**, defina o caminho da pasta onde deseja armazenar os arquivos do projeto.

Verifique se a opção **Criar diretório para solução** está selecionado e clique em **OK** para criar o projeto de malha do Service Fabric.

![Captura de tela que mostra como criar um projeto da Malha do Service Fabric.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Em seguida, você verá o diálogo **Novo Serviço do Service Fabric**.

### <a name="create-the-web-front-end-service"></a>Criar o serviço de front-end da Web

No diálogo **Novo Serviço do Service Fabric**, selecione o tipo de projeto **ASP.NET Core** e verifique se o **SO do contêiner** está definido como **Windows**.

Defina o **nome do serviço** como **WebFrontEnd**. Pressione **OK** para criar o serviço do ASP.NET Core.

![Diálogo do novo projeto de malha do Service Fabric no Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Em seguida, você verá o diálogo Novo Aplicativo Web ASP.NET Core. Selecione **Aplicativo Web** e, em seguida, clique em **OK**.

![Captura de tela que realça o modelo Aplicativo Web.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Agora você tem um aplicativo de malha do Service Fabric. Em seguida, crie o modelo para informações de tarefas pendentes.

## <a name="create-the-to-do-items-model"></a>Criar o modelo de itens de tarefas pendentes

Para simplificar, os itens de tarefas pendentes são armazenados em uma lista na memória. Crie uma biblioteca de classes para os itens de tarefas pendentes e uma lista para armazená-los. No Visual Studio, que atualmente tem a solução **todolistapp** carregada, escolha **Arquivo** > **Adicionar** > **Novo Projeto**.

No diálogo **Adicionar Novo Projeto**, na caixa **Pesquisar** na parte superior, digite `C# .net core class`. Selecione o modelo **Biblioteca de Classes (.NET Core)** .

Na caixa **Nome**, digite `Model`. Clique em **OK** para criar a biblioteca de classes.

No Gerenciador de Soluções, em **Modelo**, clique com botão direito do mouse em **Class1.cs** e escolha **Renomear**. Renomeie a classe **ToDoItem.cs**. Quando uma solicitação for exibida perguntando se você deseja renomear todas as referências, clique em **Sim**.

Substitua o conteúdo da `class ToDoItem` vazia por:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Essa classe representa os itens de tarefas pendentes.

No Visual Studio, clique com botão direito do mouse na biblioteca de classes **Modelo** e selecione **Adicionar** > **Classe...**  para criar uma lista onde manter os itens de tarefas pendentes. O diálogo **Adicionar Novo Item** aparecerá. Defina o **Nome** como `ToDoList.cs` e clique em **Adicionar**.

Em **ToDoList.cs**, substitua a `class ToDoList` vazia por:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Em seguida, crie o serviço do Service Fabric que controlará os itens de tarefas pendentes.

## <a name="create-the-back-end-service"></a>Criar o serviço de back-end

Na janela do **Gerenciador de Soluções** do Visual Studio, clique com botão direito do mouse em **todolistapp** e clique em **Adicionar** > **Novo Serviço do Service Fabric...**

O diálogo **Novo Serviço do Service Fabric** é exibido. Selecione o tipo de projeto **ASP.NET Core** e verifique se o **SO do contêiner** está definido como **Windows**. Defina o **Nome do Serviço** como **ToDoService**. Clique em **OK** para criar o serviço do ASP.NET Core.

Em seguida, o diálogo **Novo Aplicativo Web ASP.NET Core** será exibido. Nesse diálogo, selecione **API** e **OK**; um projeto para o serviço é adicionado à solução.

![Novo aplicativo ASP.NET Core do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Como o serviço de back-end não fornece nenhuma interface do usuário, desative a inicialização do navegador quando o serviço for iniciado. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **ToDoService** e selecione **Propriedades**. Na janela de propriedades exibida, selecione a guia **Depurar** à esquerda e desmarque a opção **Iniciar navegador**. Pressione **Ctrl+S** para salvar as alterações.

Como esse serviço mantém as informações de tarefas pendentes, adicione uma referência à biblioteca de classes Model. No Gerenciador de Soluções, clique com botão direito do mouse em **ToDoService** e selecione **Adicionar** > **Referência...** . O diálogo **Gerenciador de Referências** será exibido.

No **Gerenciador de Referências**, marque a caixa de seleção **Model** e clique em **OK**.

### <a name="add-a-data-context&quot;></a>Adicionar um contexto de dados

Em seguida, crie um contexto de dados que coordena o fornecimento dos dados do modelo de dados.

Para adicionar a classe de contexto de dados, clique com o botão direito do mouse em **ToDoService** no gerenciador de soluções e em **Adicionar** > **Classe**.
No diálogo **Adicionar Novo Item** exibido, verifique se **Classe** está selecionado, defina o **nome** como `DataContext.cs` e clique em **Adicionar**.

Em **DataContext.cs**, substitua o conteúdo da `class DataContext` vazia por:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList(&quot;Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Esse contexto de dados mínimo popula alguns itens de tarefas pendentes de exemplo e fornece acesso a eles.

### <a name="add-a-controller"></a>Adicionar um controlador

Um controlador padrão, que manipula as solicitações HTTP e cria a resposta HTTP, foi fornecido pelo modelo quando o projeto **ToDoService** foi criado. No **Gerenciador de Soluções**, em **ToDoService**, abra a pasta **Controladores** pasta para ver o arquivo **ValuesController.cs**. 

Clique com botão direito do mouse em **ValuesController.cs** e em **Renomear**. Renomear o arquivo para `ToDoController.cs`. Se aparecer um prompt solicitando a renomeação de todas as referências, clique em **Sim**.

Abra o arquivo **ToDoController.cs** e substitua o conteúdo da `class ToDoController` por:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Este tutorial não implementa as opções de adicionar, excluir e assim por diante, para manter o foco na comunicação com outro serviço.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Criar página da Web que exibe itens de tarefas pendentes

Com o serviço de back-end implementado, codifique o site da Web que exibirá os itens de tarefas pendentes que ele fornece. As etapas a seguir ocorrem dentro do projeto **WebFrontEnd**.

A página da Web que exibe os itens de tarefas pendentes precisa acessar a classe e a lista **ToDoItem**.
No **Gerenciador de Soluções**, adicione uma referência ao projeto Modelo clicando em **WebFrontEnd** e selecionando **Adicionar** > **Referência ...** O diálogo **Gerenciador de Referências** será exibido.

No **Gerenciador de Referências**, marque a caixa de seleção **Model** e clique em **OK**.

No **Gerenciador de Soluções**, abra a página index. cshtml navegando até **WebFrontEnd** > **Páginas** > **index. cshtml** . Abra **index.cshtml**.

Substitua o conteúdo de todo o arquivo pelo seguinte HTML que define uma tabela simples para exibir itens de tarefas pendentes:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Clique no ícone suspenso do arquivo **Index.cshtml** no **Gerenciador de Soluções** e abra **Index.cshtml.cs**.

Na parte superior de **Index.cshtml.cs**, adicione `using System.Net.Http;`

Substitua o conteúdo de `public class IndexModel` por:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

A URL para o serviço de back-end é necessária para se comunicar com o serviço. Para fins deste tutorial, o seguinte trecho de código (que é definido acima como parte de IndexModel) lê as variáveis de ambiente para compor o URL:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

A URL é composta pelo nome do serviço e pela porta. Todas essas informações são encontradas no arquivo service.yaml do projeto **ToDoService**.

> [!IMPORTANT]
> Nas etapas a seguir, os arquivos YAML serão modificados.
> Use espaços, não tabulações, para recuar as variáveis no arquivo service.yaml ou ele não será compilado. O Visual Studio pode inserir tabulações conforme você cria as variáveis de ambiente. Substitua todas as tabulações por espaços. Embora você veja erros na saída de depuração **build**, o aplicativo ainda será iniciado, mas não funcionará até que você converta as guias em espaços e recompile. Para garantir que não há tabulações no arquivo service.yaml, você poderá tornar espaços em branco visíveis no editor do Visual Studio com **Editar**  > **Avançado**  > **Exibir Espaço em Branco**.
> Observe que os arquivos service.yaml são processados usando a localidade do inglês. Por exemplo, se você precisar usar um separador decimal, use um ponto em vez de uma vírgula.

Navegue no **Gerenciador de Soluções** até o projeto **ToDoService** e abra **Recursos de Serviço** > **service.yaml**.

![Figura 1: o arquivo service.yaml de ToDoService](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 O nome do serviço, `ToDoService`, é encontrado em `services:` Confira (1) na figura acima.

* A porta, `80`, é encontrada em `endpoints:` Confira (2) na figura acima. É provável que o número da porta do projeto seja diferente.

Em seguida, será preciso definir variáveis de ambiente que representam o nome do serviço e o número da porta no projeto WebFrontEnd para que ele possa chamar o serviço de back-end.

No **Gerenciador de Soluções**, navegue até **WebFrontEnd** > **Recursos do Serviço** > **service.yaml** para definir as variáveis que especificam o endereço do serviço de back-end.

No arquivo service.yaml, inclua as seguintes variáveis em `environmentVariables:` (primeiro você precisará remover o `#` para remover o comentário `environmentVariables:`) O espaçamento é importante, portanto, alinhe as variáveis adicionadas com as outras variáveis em `environmentVariables:`. É muito importante que o valor de ApiHostPort corresponda ao valor de porta de ToDoServiceListener que foi visto anteriormente no arquivo service.yaml do ToDoService.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Há duas maneiras de especificar o valor de `ToDoServiceName`: 
> - Apenas o nome do serviço, que resolverá ambos em um cenário de depuração no Windows 10 além de implantar o serviço na Malha do Azure Service Fabric.
> - Totalmente qualificado como servicename.appname. Isso só funcionará quando na depuração no Windows 10.
> É uma boa prática usar apenas o nome do serviço na resolução do serviço.

O **WebFrontEnd** do arquivo **service.yaml** do projeto deve ficar mais ou menos assim, embora seu valor `ApiHostPort` seja provavelmente diferente:

![Service.yaml no projeto WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Agora você está pronto para compilar e implantar a imagem do aplicativo de malha do Service Fabric, junto com o serviço Web de back-end, no cluster local.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Crie um aplicativo de Malha do Service Fabric com um front-end da Web do ASP.NET.
> * Crie um modelo para representar itens de tarefas pendentes.
> * Crie um serviço de back-end e recupere dados dele.
> * Adicione um controlador e um DataContext como parte desse padrão de Controlador de Exibição de Modelo para o serviço de back-end.
> * Crie uma página da Web para exibir itens de tarefas pendentes.
> * Crie variáveis de ambiente que identificam o serviço de back-end

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Depurar um aplicativo da Malha do Service Fabric em execução em seu cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)