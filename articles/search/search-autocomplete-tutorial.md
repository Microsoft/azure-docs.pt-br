---
title: Adicione sugestões e preenchimento automático em uma caixa de pesquisa - Azure Search
description: Habilite ações de consulta de digitação antecipada no Azure Search criando sugestores e formular solicitações que preencham uma caixa de pesquisa com concluído termos ou frases.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: ed2e0bd352823a932cfea719c18e05ae6c913621
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126729"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Exemplo: Adicione sugestões ou preenchimento automático ao seu aplicativo do Azure Search

Neste artigo, saiba como usar [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [AutoCompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) para criar uma caixa de pesquisa poderoso que dá suporte a comportamentos de pesquisa-como-você-type.

+ *Sugestões* são sugeridos resultados gerados conforme você digita, onde cada sugestão é um único resultado do índice que corresponde ao que foi digitado até agora. 

+ *Preenchimento automático*, [um recurso de visualização](search-api-preview.md), "conclusão" a palavra ou frase que um usuário está digitando no momento. Em vez de retornar resultados, ele conclui uma consulta, que pode ser executado para retornar resultados. Assim como acontece com sugestões, uma concluído de palavra ou frase em uma consulta se baseia em uma correspondência no índice. O serviço não oferece consultas que retornam zero resultados no índice.

Você pode baixar e executar o código de exemplo **DotNetHowToAutocomplete** para avaliar esses recursos. O código de exemplo tem como alvo um índice predefinido preenchido com [dados de demonstração NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). O índice NYCJobs contém um [constructo Sugestor](index-add-suggesters.md), que é um requisito para usar sugestões ou preenchimento automático. Você pode usar o índice preparado hospedado em um serviço de área restrita, ou [preencher seu próprio índice](#configure-app) usando um carregador de dados na solução de exemplo NYCJobs. 

O **DotNetHowToAutocomplete** demonstra sugestões e preenchimento automático, em ambos os C# e versões de linguagem JavaScript. C#os desenvolvedores podem percorrer um aplicativo baseado no ASP.NET MVC que usa o [o SDK do .NET do Azure Search](https://aka.ms/search-sdk). A lógica para fazer chamadas de consulta sugeridos e o preenchimento automático pode ser encontrada no arquivo HomeController.cs. Os desenvolvedores de JavaScript encontrará a lógica de consulta equivalente no IndexJavaScript.cshtml, que inclui chamadas diretas para o [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Para ambas as versões de idioma, a experiência do usuário de front-end se baseia a [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) bibliotecas. Usamos essas bibliotecas para criar a caixa de pesquisa que dão suporte a sugestões e preenchimento automático. Entradas coletadas na caixa de pesquisa são emparelhadas com sugestões e ações de preenchimento automático, como aqueles conforme definido em HomeController.cs ou IndexJavaScript.cshtml.

Este exercício explica as seguintes tarefas:

> [!div class="checklist"]
> * Implementar uma caixa de entrada de pesquisa em JavaScript e emitir solicitações para correspondências sugeridas ou termos preenchida automaticamente
> * No C#, definir a ações de preenchimento automático e sugestões em HomeController.cs
> * No JavaScript, chame as APIs REST diretamente para fornecer a mesma funcionalidade

## <a name="prerequisites"></a>Pré-requisitos

Um serviço Azure Search é opcional para este exercício porque a solução usa um serviço de área restrita ao vivo hospedando um índice de demonstração NYCJobs preparado. Se você quiser executar este exemplo em seu próprio serviço de pesquisa, consulte [índice de trabalhos de NYC configurar](#configure-app) para obter instruções.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

* Baixe o [DotNetHowToAutoComplete exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

O exemplo é abrangente, cobrindo sugestões, preenchimento automático, a navegação facetada e cache do cliente. Você deve examinar o Leiame e comentários para obter uma descrição completa que oferece o exemplo.

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra **AutocompleteTutorial.sln** no Visual Studio. A solução contém um projeto ASP.NET MVC com uma conexão para o índice de demonstração de trabalhos em NYC.

2. Pressione F5 para executar o projeto e carregue a página em seu navegador preferido.

Na parte superior, você verá uma opção para selecionar C# ou JavaScript. O C# opção chama o HomeController do navegador e usa o SDK do .NET do Azure Search para recuperar os resultados. 

A opção JavaScript chama a API REST do Azure Search diretamente do navegador. Essa opção normalmente tem desempenho claramente melhor, pois ela usa o controlador fora do fluxo. Você pode escolher a opção que atender às suas necessidades e preferências de linguagem. Há vários exemplos de preenchimento automático na página com algumas diretrizes para cada um. Cada exemplo tem um texto de exemplo recomendado que você pode usar para experimentar.  

Tente digitar algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="search-box"></a>Caixa de pesquisa

Para ambos C# e versões de JavaScript, a implementação de caixa de pesquisa é exatamente o mesmo. 

Abra o **index. cshtml** arquivo sob a pasta \Views\Home para exibir o código:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Essa é uma caixa de texto de entrada simples com uma classe para aplicação de estilo, uma ID para serem referenciados pelo JavaScript e o texto de espaço reservado.  É a mágica no JavaScript incorporado.

O C# exemplo de linguagem usa JavaScript no index. cshtml para aproveitar a [biblioteca de preenchimento automático de interface do usuário do jQuery](https://jqueryui.com/autocomplete/). Esta biblioteca adiciona a experiência de preenchimento automático à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC para recuperar sugestões. A versão de linguagem JavaScript está em IndexJavaScript.cshtml. Ele inclui o script abaixo para a barra de pesquisa, bem como chamadas à API REST para o Azure Search.

Vamos examinar o código JavaScript para o primeiro exemplo, que chama a função de preenchimento automático de interface do usuário, passando uma solicitação para obter sugestões de jQuery:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

O código acima é executado no navegador no carregamento da página para configurar o preenchimento automático de interface do usuário do jQuery para a caixa de entrada "example1a".  `minLength: 3` faz com que as recomendações só sejam exibidas quando há pelo menos três caracteres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A linha acima informa à função de preenchimento automático de interface do usuário do jQuery onde obter a lista de itens a serem mostrados na caixa de pesquisa. Como esse é um projeto do MVC, ele chama a função de sugerir em HomeController.cs que contém a lógica para retornar sugestões de consulta (mais sobre sugerir na próxima seção). Essa função também passa alguns parâmetros termo, correspondência difusa e realces de controle. A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Estendendo o exemplo para dar suporte à correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa. Embora não seja necessário, ele melhora significativamente a robustez de uma experiência de digitação antecipada. Vamos experimentar isso alterando a linha de origem para habilitar a correspondência difusa.

Altere a linha abaixo da seguinte forma:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

para isto:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Inicie o aplicativo pressionando F5.

Tente digitar algo como "execativo" e observe como os resultados retornam para "Executivo", mesmo que não sejam uma correspondência exata para as letras digitadas.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery AutoCompletar apoiada de preenchimento automático do Azure Search

Até agora, a pesquisa de código de experiência do usuário tenha foi centralizada nas sugestões. O próximo bloco de código mostra a função de preenchimento automático de interface do usuário do jQuery (linha 91 em index. cshtml), passando uma solicitação para preenchimento automático do Azure Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Exemplo de C#

Agora que examinamos o código JavaScript para a página da web, vamos examinar o C# código de controlador do lado do servidor que, na verdade, recupera as correspondências sugeridas usando o SDK do .NET do Azure Search.

Abra o **HomeController.cs** arquivo sob o diretório Controllers. 

A primeira coisa que você pode notar é um método na parte superior da classe chamada `InitSearch`. Isso cria um cliente de índice HTTP autenticado para o serviço Azure Search. Para obter mais informações, consulte [como usar o Azure Search de um aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Na linha 41, observe a função Suggest. Ele se baseia a [DocumentsOperationsExtensions.Suggest método](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada. O método cria uma [SuggestParameters objeto](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), que é então passado para a API de sugerir. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

Na linha 69, observe a função de preenchimento automático. Ele se baseia a [DocumentsOperationsExtensions.Autocomplete método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

A função de preenchimento automático usa a entrada de termo de pesquisa. O método cria uma [AutoCompleteParameters objeto](https://docs.microsoft.com/rest/api/searchservice/autocomplete). O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.

(Opcional) Adicione um ponto de interrupção no início da função Suggest e explore o código. Observe que a resposta retornada pelo SDK e como ele é convertido para o resultado retornado do método.

Outros exemplos na página siga o mesmo padrão para adicionar o realce de ocorrências e facetas para dar suporte a cache do cliente dos resultados de preenchimento automático. Examine cada um deles para entender como eles funcionam e como aproveitá-los em sua experiência de pesquisa.

## <a name="javascript-example"></a>Exemplo de JavaScript

Uma implementação de Javascript de preenchimento automático e sugestões chama a API REST, usando um URI como a origem para especificar o índice e a operação. 

Para examinar a implementação de JavaScript, abra **IndexJavaScript.cshtml**. Observe que a função de preenchimento automático de interface do usuário do jQuery também é usado para a caixa de pesquisa, coletar entradas do termo de pesquisa e fazer chamadas assíncronas para o Azure Search para recuperar, sugerido correspondências ou concluída termos. 

Vamos examinar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se você comparar isso com o exemplo acima que chama o controlador Home, verá várias semelhanças.  A configuração de preenchimento automático para `minLength` e `position` são exatamente iguais. 

A alteração significativa aqui está na origem. Em vez de chamar o método sugerir no controlador principal, uma solicitação REST é criada em uma função de JavaScript e executado usando o Ajax. A resposta, em seguida, é processada em "success" e usada como a origem.

Chamadas REST usam URIs para especificar se um [AutoCompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) está sendo feita a chamada à API. Os URIs a seguir estão nas linhas 9 e 10, respectivamente.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Na linha 148, você pode encontrar um script que chama o `autocompleteUri`. A primeira chamada para `suggestUri` está na linha 39.

> [!Note]
> Fazendo chamadas REST para o serviço em JavaScript é oferecida aqui como uma demonstração conveniente da API REST, mas não deve ser interpretado como uma melhor prática recomendada ou recomendação. A inclusão de uma chave de API e o ponto de extremidade em um script abre seu serviço até ataques negação de serviço para qualquer pessoa que pode ler esses valores de script. Enquanto seu é seguro usar JavaScript para fins de aprendizado, talvez em índices hospedados no serviço gratuito, é recomendável usar Java ou C# para operações de indexação e consulta no código de produção. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurar NYCJobs para ser executado no seu serviço

Até agora, você já usa o índice de demonstração NYCJobs hospedado. Se você quiser visibilidade completa de todo o código, incluindo o índice, siga estas instruções para criar e carregar o índice em seu próprio serviço de pesquisa.

1. [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este exemplo. 

   > [!Note]
   > Se você estiver usando o serviço Azure Search gratuito, estará limitado a três índices. O carregador de dados NYCJobs cria dois índices. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos índices.

1. Baixe [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) código de exemplo.

1. Na pasta do carregador de dados do código de exemplo NYCJobs, abra **DataLoader.sln** no Visual Studio.

1. Adicione as informações de conexão para seu serviço Azure Search. Abra o App.config dentro do projeto DataLoader e altere o appSettings TargetSearchServiceName e TargetSearchServiceApiKey para refletir seu serviço Azure Search e a chave de API do serviço Azure Search. Ele pode ser encontrado no portal do Azure.

1. Pressione F5 para iniciar o aplicativo, criando dois índices e importando os dados de exemplo NYCJob.

1. Abra **AutocompleteTutorial.sln** e editar o Web. config na **AutocompleteTutorial** projeto. Altere os valores de SearchServiceName e SearchServiceApiKey para valores que são válidos para o serviço de pesquisa.

1. Pressione F5 para executar o aplicativo. Aplicativo web de exemplo é aberto no navegador padrão. A experiência é idêntica para a versão de área restrita, somente o índice e os dados são hospedados em seu serviço.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstra as etapas básicas para a criação de uma caixa de pesquisa que dá suporte a preenchimento automático e sugestões. Você viu como você poderia criar um aplicativo ASP.NET MVC e usar a API REST ou o SDK do .NET do Azure Search para recuperar sugestões.

Como uma próxima etapa, a tentativa de integração de sugestões e preenchimento automático em sua experiência de pesquisa. Os seguintes artigos de referência devem ajudar.

> [!div class="nextstepaction"]
> [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atributo de índice de facetas em uma API REST de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

