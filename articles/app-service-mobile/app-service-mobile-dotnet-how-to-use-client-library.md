---
title: "Trabalhando com a biblioteca de cliente gerenciado dos Aplicativos Móveis do Serviço de Aplicativo (Windows | Microsoft Docs"
description: "Saiba como usar um cliente do .NET para os Aplicativos Móveis do Serviço de Aplicativo do Azure com aplicativos do Windows e Xamarin."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: glenga
ms.openlocfilehash: 2b83dfe2f3e2645713ff7e1acfb4d7f26fe0d0cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como usar o cliente gerenciado para Aplicativos Móveis do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Visão geral
Este guia mostra como executar cenários comuns usando a biblioteca de cliente gerenciado para os Aplicativos Móveis do Serviço de Aplicativo do Azure em aplicativos do Windows e Xamarin. Se você for iniciante nos Aplicativos Móveis, primeiro conclua o tutorial [Início rápido dos Aplicativos Móveis do Azure][1] . Neste guia, abordaremos o SDK gerenciado do lado do cliente. Para saber mais sobre os SDKs no lado do servidor para Aplicativos Móveis, veja a documentação do [SDK do .NET Server][2] ou do [SDK do Servidor Node.js][3].

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o SDK do cliente está localizada aqui: [Referência do cliente .NET dos Aplicativos Móveis do Azure][4].
Você também pode encontrar vários exemplos de cliente no [Repositório GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Plataformas com suporte
A plataforma .NET dá suporte às seguintes plataformas:

* Versões de Xamarin Android para API 19 a 24 (KitKat usando Nougat)
* Versões de Xamarin iOS para versões 8.0 e posteriores do iOS
* Plataforma Universal do Windows
* Windows Phone 8,1
* Windows Phone 8.0, exceto para aplicativos Silverlight

A autenticação de "fluxo de servidor" usa um modo de exibição da Web para a interface do usuário apresentada.  Se o dispositivo não for capaz de apresentar uma interface do usuário do modo de exibição da Web, outros métodos de autenticação serão necessários.  Esse SDK, portanto, não é adequado para relógios ou dispositivos similarmente restritos.

## <a name="setup"></a>Configuração e Pré-requisitos
Supomos que você já criou e publicou o projeto de back-end do Aplicativo Móvel, que inclui pelo menos uma tabela.  No código usado neste tópico, a tabela é denominada `TodoItem` e tem as seguintes colunas: `Id`, `Text` e `Complete`. Essa tabela é a mesma tabela criada ao concluir o [Início rápido dos Aplicativos Móveis do Azure][1].

O tipo em C# do lado do cliente tipado correspondente é a seguinte classe:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute][6] é usada para definir o mapeamento de *PropertyName* entre o campo de cliente e o campo de tabela.

Para saber como criar tabelas no back-end de Aplicativos Móveis, veja os tópicos [SDK do .NET Server][7] ou [SDK do Servidor Node.js][8]. Se você tiver criado o back-end do Aplicativo Móvel no portal do Azure usando o Início Rápido, também poderá usar a configuração **Tabelas Fáceis** no [portal do Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Como instalar o pacote SDK do cliente gerenciado
Use um dos métodos a seguir para instalar o pacote SDK do cliente gerenciado para Aplicativos Móveis do [NuGet][9]:

* No **Visual Studio**, clique com o botão direito do mouse no projeto, clique em **Gerenciar Pacotes NuGet**, pesquise pelo pacote `Microsoft.Azure.Mobile.Client` e clique em **Instalar**.
* No **Xamarin Studio**, clique com o botão direito do mouse no projeto, clique em **Adicionar** > **Adicionar Pacotes NuGet**, pesquise pelo pacote `Microsoft.Azure.Mobile.Client ` e clique em **Adicionar Pacote**.

No arquivo de atividade principal, lembre-se de adicionar a seguinte instrução **using** :

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Como trabalhar com símbolos de depuração no Visual Studio
Os símbolos para o namespace Microsoft.Azure.Mobile estão disponíveis em [SymbolSource][10].  Veja as [instruções do SymbolSource][11] para integrar o SymbolSource ao Visual Studio.

## <a name="create-client"></a>Criar o cliente dos Aplicativos Móveis
O código a seguir cria o objeto [MobileServiceClient][12] usado para acessar o back-end do seu Aplicativo Móvel.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

No código anterior, substitua `MOBILE_APP_URL` pela URL do back-end do Aplicativo Móvel, que está localizada na folha de back-end de seu Aplicativo Móvel no [portal do Azure]. O objeto MobileServiceClient deve ser um singleton.

## <a name="work-with-tables"></a>Trabalhar com tabelas
A seção a seguir fornece detalhes sobre como pesquisar e recuperar registros e modificar os dados na tabela.  Os seguintes tópicos são abordados:

* [Criar uma referência de tabela](#instantiating)
* [Consultar dados](#querying)
* [Filtrar dados retornados](#filtering)
* [Classificar dados retornados](#sorting)
* [Retornar dados em páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Pesquisar um registro por ID](#lookingup)
* [Lidando com consultas sem tipo](#untypedqueries)
* [Inserindo dados](#inserting)
* [Atualizando dados](#updating)
* [Excluindo dados](#deleting)
* [Resolução de Conflitos e Simultaneidade Otimista](#optimisticconcurrency)
* [Associação a uma Interface de Usuário do Windows](#binding)
* [Alterando o Tamanho da Página](#pagesize)

### <a name="instantiating"></a>Como criar uma referência de tabela
Todos os códigos que acessam e modificam dados em uma tabela de back-end chamam funções no objeto `MobileServiceTable` . Obtenha uma referência à tabela chamando o método [GetTable] da seguinte maneira:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

O objeto retornado usa o modelo de serialização tipado. Também há suporte para um modelo de serialização não tipado. O exemplo abaixo [cria uma referência para uma tabela não tipada]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Em consultas não tipadas, você deve especificar a cadeia de caracteres de consulta OData subjacente.

### <a name="querying"></a>Como consultar dados do seu Aplicativo Móvel
Esta seção descreve como emitir consultas para o back-end do Aplicativo Móvel, que inclui as seguintes funcionalidades:

* [Filtrar dados retornados](#filtering)
* [Classificar dados retornados](#sorting)
* [Retornar dados em páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Pesquisar dados por ID](#lookingup)

> [!NOTE]
> Um tamanho de página controlado por servidor é usado para impedir que todas as linhas sejam retornadas.  A paginação impede que as solicitações padrão de grandes conjuntos de dados prejudiquem o serviço.  Para retornar mais de 50 linhas, use os métodos `Skip` e `Take`, conforme descrito em [Retornar dados em páginas](#paging).

### <a name="filtering"></a>Como filtrar dados retornados
O código a seguir ilustra como filtrar dados incluindo uma cláusula `Where` em uma consulta. Ele retorna todos os itens de `todoTable`, cuja propriedade `Complete` é igual a `false`. A função [Where] aplica um predicado de filtragem de linha à consulta na tabela.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Você pode exibir o URI da solicitação enviado ao back-end usando um software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. Se você examinar o URI, verá que a própria cadeia de caracteres da consulta está sendo modificada:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Essa solicitação de OData é convertida em uma consulta SQL pelo SDK do Servidor:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A função passada para o método `Where` pode ter um número arbitrário de condições.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Esse exemplo deve ser convertido em uma consulta SQL pelo SDK do Servidor:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Essa consulta pode ser dividida em várias cláusulas:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável.  A opção anterior&mdash;de concatenar vários predicados em uma consulta&mdash;é mais compacta e recomendada.

A cláusula `Where` dá suporte a operações que são convertidas para o subconjunto OData. As operações incluem:

* Operadores relacionais (==,!=, <, <=, >, >=),
* Operadores aritméticos (+, -, /, *, %),
* Número de precisão (Math.Floor, Math.Ceiling),
* Funções de cadeia de caracteres (Length, Substring, Replace, IndexOf, StartsWith, EndsWith),
* Propriedades de data (ano, mês, dia, hora, minuto, segundo),
* Propriedades de acesso de um objeto e
* Expressões que combinam qualquer uma dessas operações.

Ao considerar o que é compatível com o SDK do Servidor, você pode consultar a [Documentação do OData v3].

### <a name="sorting"></a>Como classificar dados retornados
O código a seguir ilustra como classificar dados incluindo uma função [OrderBy] ou [OrderByDescending] na consulta. Ele retorna os itens da `todoTable` classificada em ordem crescente pelo campo `Text`.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Como retornar dados em páginas
Por padrão, o back-end retorna apenas as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [Take] . Use `Take` juntamente com o método [Ignorar] para solicitar uma "página" específica do conjunto de dados total retornado pela consulta. A consulta a seguir, quando executada, retorna os três itens principais na tabela.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A consulta revisada a seguir ignora os três primeiros resultados e retorna os três seguintes. Essa consulta produz a segunda "página" de dados, onde o tamanho da página é de três itens.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

O método [IncludeTotalCount] solicita a contagem total de *todos* os registros que seriam retornados, ignorando cláusulas de paginação/limite especificadas:

```
query = query.IncludeTotalCount();
```

Em um aplicativo de verdade, você pode usar consultas semelhantes ao exemplo anterior com um controle de paginação ou interface do usuário semelhante para navegar entre páginas.

> [!NOTE]
> Para substituir o limite de 50 linhas em um back-end do Aplicativo Móvel, você também deve aplicar o [EnableQueryAttribute] ao método GET público e especificar o comportamento de paginação. Quando aplicado ao método, o seguinte define o máximo de linhas retornadas para 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Como selecionar colunas específicas
Você pode especificar qual conjunto de propriedades incluir nos resultados adicionando uma cláusula [Select] à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

```
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Todas as funções descritas até agora são aditivas e, portanto, podemos continuar a encadeá-las. Cada chamada encadeada afeta mais a consulta. Mais um exemplo:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Como pesquisar dados pela ID
A função [LookupAsync] pode ser usada para procurar objetos do banco de dados com uma ID específica.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Como executar consultas sem tipo
Ao executar uma consulta usando um objeto de tabela sem tipo, você deve especificar expressamente a cadeia de consulta OData chamando [ReadAsync], como no exemplo a seguir:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Você recupera valores JSON que podem ser usados como um recipiente de propriedades. Para obter mais informações sobre JToken e Newtonsoft Json.NET, confira o site [Json.NET] .

### <a name="inserting"></a>Como inserir dados em um back-end do Aplicativo Móvel
Todos os tipos de cliente devem conter um membro chamado **Id**, que é por padrão uma cadeia de caracteres. Essa **Id** é necessária para executar operações CRUD para sincronização offline. O código a seguir ilustra como usar o método [InsertAsync] para inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

```
await todoTable.InsertAsync(todoItem);
```

Se um valor exclusivo de ID personalizada não é incluído no `todoItem` durante uma inserção, um GUID é gerado pelo servidor.
Você poderá recuperar a Id gerada ao inspecionar o objeto após o retorno da chamada.

Para inserir dados não tipados, você pode tirar proveito do Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Aqui está um exemplo usando um endereço de email como uma id de cadeia de caracteres exclusiva:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabalhando com valores de ID
Os Aplicativos Móveis dão suporte a valores exclusivos e personalizados de cadeia de caracteres para a coluna **id** da tabela. Um valor de cadeia de caracteres permite que aplicativos usem valores personalizados, como endereços de email ou nomes de usuário para a ID.  IDs de cadeia de caracteres fornecem os seguintes benefícios:

* As IDs são geradas sem fazer uma varredura no banco de dados.
* Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
* Os valores de IDs podem integrar-se melhor a uma lógica do aplicativo.

Quando um valor de ID de cadeia de caracteres não está definido em um registro inserido, o back-end do Aplicativo Móvel gera um valor exclusivo para a ID. Você pode usar o método [Guid.NewGuid] para gerar seus próprios valores de ID, seja no cliente ou no back-end.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Como modificar dados em um back-end de Aplicativo Móvel
O código a seguir ilustra como usar o método [UpdateAsync] para atualizar um registro existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Para atualizar dados não tipados, você pode tirar proveito do [Json.NET] da seguinte forma:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Um campo `id` deve ser especificado ao fazer uma atualização. O back-end usa o campo `id` para identificar a linha a ser atualizada. O campo `id` pode ser obtido do resultado da chamada `InsertAsync`. Quando você tenta atualizar um item sem fornecer o valor `id`, uma `ArgumentException` é gerada.

### <a name="deleting"></a>Como excluir dados em um back-end do Aplicativo Móvel
O código a seguir ilustra como usar o método [DeleteAsync] para excluir uma instância existente. A instância é identificada pelo campo `id` definido em `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Para excluir dados não tipados, você pode tirar proveito do Json.NET da seguinte forma:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Quando você faz uma solicitação de exclusão, uma ID deve ser especificada. Outras propriedades não são passadas para o serviço ou são ignoradas no serviço. O resultado de uma chamada de `DeleteAsync` geralmente é `null`. A ID a ser passada pode ser obtida do resultado da chamada de `InsertAsync` . Um `MobileServiceInvalidOperationException` é gerado quando você tenta excluir um item sem especificar o campo `id`.

### <a name="optimisticconcurrency"></a>Como usar a simultaneidade otimista para resolução de conflitos
Dois ou mais clientes podem gravar alterações no mesmo item ao mesmo tempo. Sem detecção de conflito, a última gravação substituiria as atualizações anteriores. **Controle de simultaneidade otimista** pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio.  Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida.

Os Aplicativos Móveis dão suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item na coluna de propriedades do sistema `version` definida para cada tabela no back-end do Aplicativo Móvel. Cada vez que um registro é atualizado, os Aplicativos Móveis definem a propriedade `version` desse registro como um novo valor. Durante cada solicitação de atualização, a propriedade `version` do registro incluído na solicitação é comparada à mesma propriedade do registro no servidor. Se a versão transmitida com a solicitação não corresponder ao back-end, a biblioteca de cliente gerará uma exceção `MobileServicePreconditionFailedException<T>` . O tipo incluído com a exceção é o registro do back-end que contém a versão do registro dos servidores. O aplicativo poderá, então, usar essas informações para decidir se deve executar a solicitação de atualização novamente com o valor de `version` correto do back-end para confirmar as alterações.

Define uma coluna na classe da tabela para a propriedade do sistema `version` para habilitar a simultaneidade otimista. Por exemplo:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Aplicativos que usam tabelas não tipadas habilitam a simultaneidade otimista definindo o sinalizador `Version` nas `SystemProperties` da tabela da seguinte maneira.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Além de habilitar a simultaneidade otimista, você também deve capturar a exceção `MobileServicePreconditionFailedException<T>` em seu código ao chamar [UpdateAsync].  Resolva o conflito aplicando a `version` correta ao registro atualizado e chame [UpdateAsync] com o registro resolvido. O código a seguir mostra como resolver um conflito de gravação quando detectado:

```
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resoltion between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Para obter mais informações, confira o tópico [Sincronização de Dados Offline nos Aplicativos Móveis do Azure] .

### <a name="binding"></a>Como: associar dados dos Aplicativos Móveis a uma interface do usuário do Windows
Esta seção mostra como exibir os objetos de dados retornados usando elementos da interface do usuário em um aplicativo do Windows.  O exemplo de código a seguir associa a origem da lista a uma consulta de itens incompletos. O [MobileServiceCollection] cria uma coleção de associações com reconhecimento de Aplicativos Móveis.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Alguns controles no tempo de execução gerenciado dão suporte a uma interface chamada [ISupportIncrementalLoading]. Essa interface permite que os controles solicitem dados adicionais quando o usuário rola. Há suporte interno para essa interface para aplicativos universais do Windows via [MobileServiceIncrementalLoadingCollection], que processa as chamadas automaticamente por meio dos controles. Use `MobileServiceIncrementalLoadingCollection` em aplicativos do Windows, da seguinte maneira:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para usar a nova coleção nos aplicativos do Windows Phone 8 e do “Silverlight”, use os métodos da extensão `ToCollection` em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar dados, chame `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Quando usa a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você obtém uma coleção que pode ser vinculada a controles da interface do usuário.  Essa coleção tem reconhecimento de paginação.  Como a coleção está carregando dados da rede, o carregamento às vezes falha. Para lidar com essas falhas, substitua o método `OnException` na `MobileServiceIncrementalLoadingCollection` para tratar das exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Pense que sua tabela tem muitos campos, mas você só deseja exibir alguns deles em seu controle. Você pode usar as diretrizes contidas na seção anterior "[Selecionar colunas específicas](#selecting)" para selecionar colunas específicas a serem exibidas na interface do usuário.

### <a name="pagesize"></a>Alterar o Tamanho da página
Os Aplicativos Móveis do Azure retornam, no máximo, 50 itens por solicitação por padrão.  Você pode alterar o tamanho de paginação aumentando o tamanho máximo da página no cliente e no servidor.  Para aumentar o tamanho da página solicitada, especifique `PullOptions` ao usar `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Supondo que você deixou o `PageSize` igual ou maior a 100 no servidor, uma solicitação retorna até 100 itens.

## <a name="#offlinesync"></a>Trabalhar com tabelas offline
Tabelas offline usam um armazenamento local do SQLite para armazenamento de dados para uso no modo offline.  Todas as operações da tabela são executadas no armazenamento local do SQLite em vez de no armazenamento do servidor remoto.  Para criar uma tabela offline, primeiro, prepare seu projeto:

1. No Visual Studio, clique com o botão direito do mouse na solução > **Gerenciar Pacotes NuGet para a Solução...**, procure e instale o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos na solução.
2. (Opcional) Para dar suporte a dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de Execução do Windows 8.1**: instale o [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1**: instale o [SQLite para Windows Phone 8.1][4].
   * **Plataforma Universal do Windows**: instale o [SQLite para a Plataforma Universal do Windows][5].
3. (Opcional). Para dispositivos Windows, clique com botão direito do mouse em **Referências** > **Adicionar Referência…**, expanda a pasta **Windows** > **Extensões**, habilite o SDK do **SQLite para Windows** apropriado junto com o SDK do **Tempo de Execução do Visual C++ 2013 para Windows**.
    Os nomes do SDK do SQLite variam ligeiramente de acordo com cada plataforma Windows.

Antes que uma referência de tabela possa ser criada, o armazenamento local precisa ser preparado:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

A inicialização do armazenamento normalmente é feita imediatamente depois que o cliente é criado.  O **OfflineDbPath** deve ser um nome de arquivo adequado para uso em todas as plataformas que tem suporte.  Se o caminho for um caminho totalmente qualificado (ou seja, começa com uma barra invertida), então, ele será usado.  Se o caminho não for totalmente qualificado, o arquivo será colocado em um local específico da plataforma.

* Para dispositivos Android e iOS, o caminho padrão é a pasta "Arquivos pessoais".
* Para dispositivos Windows, o caminho padrão é a pasta "AppData" específica do aplicativo.

Uma referência de tabela pode ser obtida usando o método `GetSyncTable<>`:

```
var table = client.GetSyncTable<TodoItem>();
```

Você não precisa se autenticar para usar uma tabela offline.  Você precisa se autenticar apenas quando estiver se comunicando com o serviço de back-end.

### <a name="syncoffline"></a>Sincronizando uma tabela Offline
Tabelas off-line não são sincronizadas com o back-end, por padrão.  A sincronização é dividida em duas partes.  Você pode enviar alterações separadamente de download de novos itens.  Este é um método de sincronização típico:

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Se o primeiro argumento para `PullAsync` for nulo, a sincronização incremental não será usado.  Todas as operações de sincronização recuperam todos os registros.

O SDK executa um `PushAsync()` implícito antes de extrair os registros.

Manipulação de conflito ocorre em um método `PullAsync()`.  Você pode lidar com conflitos da mesma maneira que com tabelas on-line.  O conflito é produzido quando `PullAsync()` é chamado em vez de durante a inserção, atualização ou exclusão. Se vários conflitos ocorrerem, eles serão agrupados em uma única MobileServicePushFailedException.  Gerenciar cada falha separadamente.

## <a name="#customapi"></a>Trabalhar com uma API personalizada
Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

Você pode chamar uma API personalizada chamando um dos métodos [InvokeApiAsync] no cliente. Por exemplo, a seguinte linha de código envia uma solicitação POST à API **completeAll** no back-end:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Essa forma é uma chamada de método tipada e exige que o tipo de retorno **MarkAllResult** seja definido. Os dois métodos, tipado e não tipado, são aceitos.

O método InvokeApiAsync() precede '/api /' para a API que você deseja chamar, a menos que a API comece com '/'.
Por exemplo:

* `InvokeApiAsync("completeAll",...)` chama /api/completeAll no back-end
* `InvokeApiAsync("/.auth/me",...)` chama /.auth/me no back-end

Você pode usar InvokeApiAsync para chamar qualquer API Web, incluindo as que não são definidas com aplicativos móveis do Azure.  Ao usar InvokeApiAsync(), os cabeçalhos apropriados, incluindo os cabeçalhos de autenticação, são enviados com a solicitação.

## <a name="authentication"></a>Autenticar usuários
Os Aplicativos Móveis dão suporte à autenticação e à autorização de usuários de aplicativo, usando vários provedores de identidade externos: Facebook, Google, Conta da Microsoft, Twitter e o Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo].

Dois fluxos de autenticação têm suporte: fluxo *gerenciado pelo cliente* e fluxo *gerenciado pelo servidor*. O fluxo gerenciado pelo servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo gerenciado pelo cliente permite uma integração mais profunda com recursos específicos ao dispositivo pois depende dos SDKs específicos ao provedor e ao dispositivo.

> [!NOTE]
> Recomendamos o uso de um fluxo gerenciado pelo cliente em seus aplicativos de produção.

Para configurar a autenticação, você precisa registrar seu aplicativo com um ou mais provedores de identidade.  O provedor de identidade gera uma ID de cliente e um segredo do cliente para seu aplicativo.  Esses valores são definidos no seu back-end para habilitar a autenticação/autorização de Serviço de Aplicativo do Azure.  Para saber mais, siga as instruções detalhadas no tutorial [Adicionar autenticação ao seu aplicativo].

Os tópicos a seguir são abordados nesta seção:

* [Autenticação gerenciada pelo cliente](#clientflow)
* [Autenticação gerenciada pelo servidor](#serverflow)
* [Armazenando o token de autenticação em cache](#caching)

### <a name="clientflow"></a>Autenticação gerenciada pelo cliente
Seu aplicativo pode entrar em contato de forma independente com o provedor de identidade e fornecer o token retornado durante o login com seu backend. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade. É melhor usar a autenticação de fluxo de cliente do que usar um fluxo de servidor, já que o SDK do provedor de identidade fornece uma aparência mais nativa do UX e permite uma maior personalização.

Veja exemplos para os seguintes padrões de autenticação de fluxo de cliente:

* [Biblioteca de Autenticação do Active Directory](#adal)
* [Facebook ou Google](#client-facebook)
* [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autenticar usuários com a Active Directory Authentication Library
Você pode usar a ADAL (Biblioteca de autenticação do Active Directory) para iniciar a autenticação do usuário a partir do cliente usando a autenticação do Azure Active Directory.

1. Configure o seu back-end de aplicativo móvel para entrada no AAD seguindo o tutorial [Como configurar o Serviço de Aplicativo para logon no Active Directory] . Complete a etapa opcional de registrar um aplicativo cliente nativo.
2. No Visual Studio ou Xamarin Studio, abra o projeto e adicione uma referência ao pacote NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory` . Ao pesquisar, inclua versões de pré-lançamento.
3. Adicione o código a seguir ao seu aplicativo, de acordo com a plataforma que você está usando. Em cada um, faça as seguintes substituições:

   * Substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário no qual o aplicativo foi provisionado. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Esse valor pode ser copiado da guia Domínio no Azure Active Directory no [portal clássico do Azure].
   * Substitua **INSERT-RESOURCE-ID-HERE** pela ID do cliente do seu back-end de aplicativo móvel. Você pode obter a ID do cliente na guia **Avançadas** em **Configurações do Azure Active Directory** no portal.
   * Substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo cliente nativo.
   * Substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade */.auth/login/done* do site, usando o esquema HTTPS. Esse valor deve ser similar a *https://contoso.azurewebsites.net/.auth/login/done*.

     Veja a seguir o código necessário para cada plataforma:

     **Windows:**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

     **Xamarin.iOS**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ```

#### <a name="client-facebook"></a>Entrada única usando um token do Facebook ou do Google
Você pode usar o fluxo de cliente como mostra este trecho de código para o Facebook ou o Google.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

#### <a name="client-livesdk"></a>Logon único usando a Conta da Microsoft com o Live SDK
Para autenticar usuários, você deverá registrar seu aplicativo na Central de desenvolvedores da conta da Microsoft. Configure os detalhes do registro no back-end do Aplicativo Móvel. Para criar um registro de conta da Microsoft e conectá-lo ao back-end do Aplicativo Móvel., conclua as etapas em [Registrar seu aplicativo para usar um logon de conta da Microsoft]. Se você tiver as versões da Windows Store e do Windows Phone 8/Silverlight de seu aplicativo, registre a versão da Windows Store primeiro.

O código a seguir é autenticado usando o Live SDK e usa o token retornado para entrar no back-end do seu Aplicativo Móvel.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Windows Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

Para saber mais, confira a documentação do [SDK do Windows Live] .

### <a name="serverflow"></a>Autenticação gerenciada pelo servidor
Depois de registrar seu provedor de identidade, chame o método [LoginAsync] no [MobileServiceClient] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, o código a seguir inicia uma entrada de fluxo do servidor usando o Facebook.

```
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Se você estiver usando um provedor de identidade além do Facebook, altere o valor [MobileServiceAuthenticationProvider] para o valor de seu provedor.

Em um fluxo de servidor, o Serviço de Aplicativo do Azure gerencia o fluxo de autenticação OAuth exibindo a página de entrada do provedor selecionado.  Depois que o provedor de identidade retorna, o Serviço de Aplicativo do Azure gera um token de autenticação do Serviço de Aplicativo. O método [LoginAsync] retorna um [MobileServiceUser], que fornece a [UserId] do usuário autenticado e o [MobileServiceAuthenticationToken] como um JWT (token da Web JSON). Esse token pode ser armazenado em cache e reutilizado até que expire. Para obter mais informações, consulte [Armazenando o token de autenticação em cache](#caching).

### <a name="caching"></a>Armazenando o token de autenticação em cache
Em alguns casos, a chamada para o método de logon pode ser evitada após a primeira autenticação bem-sucedida armazenando o token de autenticação do provedor.  Os aplicativos da Windows Store e UWP podem usar [PasswordVault] para armazenar em cache o token de autenticação atual após uma conexão bem-sucedida, da seguinte maneira:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

O valor de UserId é armazenado como o UserName da credencial e o token é armazenado como a Senha. Em inicializações subsequentes, você pode verificar o **PasswordVault** para ter acesso às credenciais armazenadas em cache. O exemplo a seguir usa as credenciais armazenadas em cache quando elas forem encontradas e tenta autenticar novamente com o back-end:

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Quando você faz o logoff de um usuário, também deve remover a credencial armazenada, da seguinte maneira:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Os aplicativos Xamarin usam as APIs [Xamarin.Auth] para armazenar com segurança as credenciais em um objeto **Account** . Para obter um exemplo de como usar essas APIs, confira o arquivo de código [AuthStore.cs] no [exemplo de compartilhamento de fotos de ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Quando você usa a autenticação gerenciada pelo cliente, também pode armazenar em cache o token de acesso obtido de seu provedor, como o Facebook ou Twitter. Esse token pode ser fornecido para solicitar um novo token de autenticação a partir do back-end, da seguinte maneira:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Notificações por Push
Os tópicos a seguir abordam Notificações por Push:

* [Registrar notificações por push](#register-for-push)
* [Obter um SID do pacote da Windows Store](#package-sid)
* [Registrar com modelos de Plataforma cruzada](#register-xplat)

### <a name="register-for-push"></a>Como se registrar para receber notificações por push
O cliente de Aplicativos Móveis permite que você se registrar para notificações por push com Hubs de Notificação do Azure. Ao se registrar, você obtém um identificador obtido do PNS (Serviço de Notificação por Push) específico da plataforma. Então fornece este valor, juntamente com quaisquer marcas, no momento em que cria o registro. O seguinte código registra seu aplicativo do Windows para notificações de push no WNS (Serviço de Notificação do Windows):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Se você estiver enviando WNS, DEVERÁ [obter um SID do pacote da Windows Store](#package-sid).  Para saber mais sobre os aplicativos do Windows, inclusive como se registrar para obter registros de modelo, confira [Adicionar notificações por push ao seu aplicativo].

A solicitação de marcas do cliente não tem suporte.  As solicitações de marca são descartadas silenciosamente do registro.
Se você deseja registrar seu dispositivo com marcas, crie uma API personalizada que usa a API de Hubs de Notificação para realizar o registro em seu nome.  [Chame a API Personalizada](#customapi) em vez do método `RegisterNativeAsync()`.

### <a name="package-sid"></a>Como obter um SID do pacote da Windows Store
Um SID de pacote é necessário para habilitar notificações por push em aplicativos da Windows Store.  Registre seu aplicativo na Windows Store para receber um SID do pacote.

Para obter esse valor:

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, clique em **Armazenar** > **Associar Aplicativo à Store...**.
2. No assistente, clique em **Avançar**, entre com sua conta da Microsoft, digite um nome para seu aplicativo em **Reservar um novo nome de aplicativo** e clique em **Reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o nome do aplicativo, clique em **Avançar** e em **Associar**.
4. Faça logon na [Central de Desenvolvimento do Windows] usando a sua Conta da Microsoft. Em **Meus aplicativos**, clique no registro de aplicativo que você criou.
5. Clique em **Gerenciamento de aplicativos** > **Identidade de aplicativos** e, em seguida, role para baixo até encontrar o **SID do Pacote**.

Muitos usos do SID do pacote o tratam como um URI; nesse caso, você precisa usar *ms-app://* como o esquema. Anote a versão do SID do pacote formado pela concatenação desse valor como um prefixo.

Os aplicativos Xamarin exigem mais código para poder registrar um aplicativo em execução nas plataformas iOS ou Android. Para saber mais, confira o tópico para sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Como registrar modelos de envio por push para enviar notificações entre plataformas
Para registrar modelos, use o método `RegisterAsync()` com os modelos, da seguinte maneira:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Seus modelos devem ser do tipo `JObject` e podem conter vários modelos no seguinte formato JSON:

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

O método **RegisterAsync()** também aceita Blocos Secundários:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Todas as marcações são retiradas durante o registro para segurança. Para adicionar marcas a instalações ou modelos dentro de instalações, confira [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure].

Para enviar notificações usando esses modelos registrados, consulte as [APIs dos Hubs de Notificação].

## <a name="misc"></a>Tópicos Diversos
### <a name="errors"></a>Como tratar erros
Quando ocorre um erro no back-end, o SDK do cliente dispara uma `MobileServiceInvalidOperationException`.  O seguinte exemplo mostra como manipular uma exceção que é retornada pelo back-end:

```
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Outro exemplo de lidar com condições de erro pode ser encontrado no [exemplo de arquivos de Aplicativos Móveis]. O exemplo de [LoggingHandler] fornece um manipulador de representante de registro em log para registrar as solicitações que estão sendo feitas no back-end.

### <a name="headers"></a>Como personalizar cabeçalhos de solicitação
Para dar suporte ao seu cenário específico de aplicativo, convém personalizar a comunicação com o back-end do Aplicativo Móvel. Por exemplo, convém adicionar um cabeçalho personalizado para cada solicitação de saída, ou até mesmo alterar códigos de status de respostas. Você pode usar um [DelegatingHandler]personalizado, como no exemplo abaixo:

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Adicionar autenticação ao seu aplicativo]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronização de Dados Offline nos Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Adicionar notificações por push ao seu aplicativo]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrar seu aplicativo para usar um logon de conta da Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar o Serviço de Aplicativo para logon no Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[cria uma referência para uma tabela não tipada]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignorar]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Central de Desenvolvimento do Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK do Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[APIs dos Hubs de Notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[exemplo de arquivos de Aplicativos Móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentação do OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
