---
title: Como implementar a faceted navigation em uma hierarquia de categoria – Azure Search
description: Adicione a navegação de faceta aos aplicativos integrados ao Azure Search, um serviço de pesquisa hospedado na nuvem do Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6fc1e1aaaa3b2489dd4083f56d45ab0abc2b6892
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165964"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar a navegação facetada no Azure Search
A navegação facetada é um mecanismo de filtragem que fornece navegação de busca detalhada autodirigida em aplicativos de pesquisa. O termo 'navegação facetada' pode não ser familiar, mas provavelmente você já a usou antes. Como mostra o exemplo a seguir, a navegação facetada é nada mais do que as categorias usadas para filtrar resultados.

 ![Demonstração de Portal de trabalho do Azure Search](media/search-faceted-navigation/azure-search-faceting-example.png "demonstração de Portal de trabalho do Azure Search")

A navegação facetada é um ponto de entrada alternativo para a pesquisa. Ela oferece uma alternativa conveniente para digitar expressões de pesquisa complexas manualmente. As facetas podem ajudar a encontrar o que você está procurando, garantindo que você não obterá zero resultados. Como desenvolvedor, facetas permitem que você exponha os critérios de pesquisa mais úteis para navegar em seu índice de pesquisa. Em aplicativos de varejo online, a navegação facetada geralmente é criada sobre marcas, departamentos (sapatos infantis), tamanho, preço, popularidade e classificações. 

A implementação da navegação facetada varia entre diferentes tecnologias de pesquisa. No Azure Search, a navegação facetada baseia-se no tempo da consulta, usando os campos atribuídos especificados anteriormente no seu esquema.

-   Nas consultas compiladas pelo seu aplicativo, uma consulta deve enviar *parâmetros de faceta de consulta* para obter os valores de filtro de faceta disponíveis para esse conjunto de resultados do documento.

-   Para realmente recortar o conjunto de resultados do documento, o aplicativo também deve aplicar uma expressão `$filter`.

No desenvolvimento do aplicativo, escrever um código que construa consultas constitui a maior parte do trabalho. Muitos dos comportamentos de aplicativo que você esperaria da navegação facetada são fornecidos pelo serviço, incluindo suporte integrado para configuração de intervalos e obtenção de contagens de resultados de uma determinada faceta. O serviço também inclui padrões pertinentes, que o ajudarão a evitar estruturas de navegação complicadas. 

## <a name="sample-code-and-demo"></a>Demonstração e código de exemplo
Este artigo usa um portal de pesquisa de trabalhos como um exemplo. O exemplo é implementado como um aplicativo ASP.NET MVC.

-   Veja e teste a demonstração de trabalho online na [Demonstração do portal do trabalho do Azure Search](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código do [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introdução
Se você é novo no desenvolvimento de pesquisa, a melhor maneira de pensar em navegação facetada é que ela mostra as possibilidades de pesquisa autodirigida. É um tipo de experiência de busca detalhada, com base em filtros predefinidos usados para restringir os resultados da pesquisa rapidamente por meio de ações de apontar e clicar. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação facetada é iterativa, então vamos começar entendê-la como uma sequência de consultas que se desenrolam em resposta às ações do usuário.

O ponto de partida é uma página de aplicativo que oferece navegação facetada, normalmente posicionada na periferia. A navegação facetada costuma ser uma estrutura de árvore, com caixas de seleção para cada valor ou texto que pode ser clicado. 

1. Uma consulta enviada para o Azure Search especifica a estrutura de navegação facetada por meio de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com uma opção `:values` ou `:sort` para refinar ainda mais a apresentação.
2. A camada de apresentação renderiza uma página de pesquisa que oferece navegação facetada, usando as facetas especificadas na solicitação.
3. Dada uma estrutura de navegação facetada que inclui classificação, você clica em "4" para indicar que somente os produtos com uma classificação 4 ou superior devem ser mostrados. 
4. Em resposta, o aplicativo envia uma consulta que inclui `$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzido apenas com os itens que atendem aos novos critérios (nesse caso, os produtos classificados como 4 e superior).

Uma faceta é um parâmetro de consulta, mas não a confunda com uma entrada de consulta. Ela nunca é usada como critério de seleção em uma consulta. Em vez disso, pense em facetas usadas como parâmetros de consulta como entradas para a estrutura de navegação que retorna como resposta. Para cada parâmetro de consulta de faceta que você fornecer, o Azure Search avaliará quantos documentos estão nos resultados parciais para cada valor de faceta.

Observe o `$filter` na etapa 4. O filtro é um aspecto importante da navegação facetada. Embora facetas e filtros sejam independentes na API, você precisará de ambos para fornecer a experiência que pretende. 

### <a name="app-design-pattern"></a>Padrão de design do aplicativo

No código do aplicativo, o padrão é usar parâmetros de consulta de faceta para retornar a estrutura de navegação facetada juntamente com os resultados da faceta, mais de uma expressão $filter.  A expressão de filtro manipula o evento de clique no valor da faceta. Imagine a expressão `$filter` como o código por trás do verdadeiro recorte dos resultados da pesquisa retornados para a camada de apresentação. Dada uma faceta de cores, clicar na cor Vermelho é implementado através de uma expressão `$filter` que seleciona somente os itens que têm cor vermelha. 

### <a name="query-basics"></a>Noções básicas sobre consulta

No Azure Search, uma solicitação é especificada por meio de um ou mais parâmetros de consulta (consulte [Procurar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta é necessário, mas você deve ter pelo menos um para que uma consulta seja válida.

A precisão, entendida como a capacidade de filtrar correspondências irrelevantes, é obtida por meio de uma ou de ambas as expressões:

-   **search=**  
    O valor deste parâmetro constitui a expressão de pesquisa. Pode ser uma única parte de texto ou uma expressão de pesquisa complexa que inclua vários termos e operadores. No servidor, uma expressão de pesquisa é usada para pesquisa de texto completo, consultando os campos de pesquisa no índice para correspondência de termos, retornando resultados em ordem de classificação. Se você definir `search` como nulo, a consulta é executada sobre todo o índice (ou seja, `search=*`). Nesse caso, outros elementos da consulta, como um `$filter` ou perfil de pontuação, são os principais fatores que afetam quais documentos são retornados `($filter`) e em qual ordem (`scoringProfile` ou `$orderby`).

-   **$filter=**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos específicos do documento. Um `$filter` é avaliado primeiro, seguido pela lógica de facetagem que gera os valores disponíveis e as contagens correspondentes para cada valor

As expressões de pesquisa complexas diminuem o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construídas para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa a uma que inclui uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Embora ambas as consultas sejam válidas, a segunda é melhor se você está procurando estabelecimentos que não sejam motéis com estacionamento em Seattle.
-   A primeira consulta depende dessas palavras específicas serem mencionadas ou não nos campos de cadeia de caracteres como Nome, Descrição e qualquer outro campo que contenha dados pesquisáveis.
-   A segunda consulta procura correspondências precisas em dados estruturados, e provavelmente será muito mais precisa.

Em aplicativos que incluam navegação facetada, garanta que cada ação do usuário em uma estrutura de navegação facetada seja acompanhada de uma redução dos resultados da pesquisa. Para restringir os resultados, use uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Criar um aplicativo de navegação facetada
Você pode implementar a navegação facetada com o Azure Search em seu código de aplicativo que cria a solicitação de pesquisa. A navegação facetada se baseia em elementos no esquema que você definiu anteriormente.

Predefinido em sua pesquisa de índice está o atributo de índice `Facetable [true|false]` , definido nos campos selecionados para habilitar ou desabilitar seu uso em uma estrutura de navegação facetada. Sem `"Facetable" = true`, um campo não pode ser usado na navegação facetada.

A camada de apresentação em seu código fornece a experiência do usuário. Ele deve listar as partes constituintes da navegação facetada, como o rótulo, valores, caixas de seleção e a contagem. A API REST de Azure Search é independente de plataforma, portanto, use qualquer idioma e plataforma que desejar. O importante é incluir elementos de interface do usuário que deem suporte a atualização incremental, com estado da interface do usuário sendo atualizado quando cada faceta adicional é selecionada. 

No momento da consulta, o código do aplicativo cria uma solicitação que inclui `facet=[string]`, um parâmetro de solicitação que fornece o campo pelo qual facetar. Uma consulta pode ter múltiplas facetas, como `&facet=color&facet=category&facet=rating`, cada uma separada por um caractere “e” comercial (&).

O código do aplicativo também deve construir uma expressão `$filter` para manipular os eventos de clique na navegação facetada. Um `$filter` reduz os resultados da pesquisa, usando o valor da faceta como critério de filtro.

O Azure Search retorna os resultados da pesquisa com base em um ou mais termos que você insere, junto com as atualizações para a estrutura de navegação facetada. No Azure Search, a navegação facetada é uma construção de nível único, com valores de facetas e contagens de quantos resultados são encontrados para cada uma delas.

Nas seções a seguir, examinaremos mais detalhadamente como criar cada parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Compilar o índice
A facetagem está habilitada no índice de campo por campo, por meio desse atributo de índice: `"Facetable": true`.  
Todos os tipos de campo que poderiam ser usados na navegação facetada são `Facetable` por padrão. Esses tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset` e todos os tipos de campo numérico (basicamente, todos os tipos de campo são facetáveis exceto `Edm.GeographyPoint`, que não pode ser usado na navegação facetada). 

Ao criar um índice, uma prática recomendada para navegação facetada é desativar explicitamente a facetagem para os campos que nunca devem ser usados como uma faceta.  Em particular, os campos de cadeia de caracteres para valores únicos, como uma ID ou nome de produto, devem ser definidos como `"Facetable": false` para evitar seu uso acidental (e ineficaz) na navegação facetada. Desligar a facetagem onde você não precisa dela ajuda a manter o tamanho do índice pequeno e geralmente melhora o desempenho.

A seguir, parte do esquema para o aplicativo de exemplo de Demonstração do Portal de Trabalho, sem alguns atributos para reduzir o tamanho:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Como você pode ver no esquema de exemplo, `Facetable` é desativado para os campos de cadeia de caracteres que não devem ser usados como facetas, como os valores de ID. Desligar a facetagem onde você não precisa dela ajuda a manter o tamanho do índice pequeno e geralmente melhora o desempenho.

> [!TIP]
> Como prática recomendada, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` seja ativado por padrão para quase todos os campos, definir propositadamente cada atributo pode ajudá-lo a considerar as implicações de cada decisão do esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verificar os dados
A qualidade dos seus dados tem um impacto direto sobre se a estrutura de navegação facetada se materializa como esperado. Ela também afeta a facilidade de criação de filtros para reduzir o conjunto de resultados.

Se você quiser realizar a facetagem por Marca ou Preço, cada documento deverá conter valores para *BrandName* e *ProductPrice* que sejam válidos, consistentes e produtivos como uma opção de filtragem.

Veja alguns lembretes sobre o que se deve procurar:

* Pergunte-se, para cada campo segundo o qual você deseja realizar a facetagem, se ele contém valores que são adequados como filtros na pesquisa autodirigida. Os valores devem ser curtos, descritivos e suficientemente diferentes para oferecer uma escolha clara entre as opções de concorrentes.
* Erros de ortografia ou valores quase correspondentes. Se você realiza a facetagem por Cor e os valores de campo incluem Laranja e Laranja (erro), uma facetagem baseada no campo Cor utilizaria ambos.
* Texto compostode caracteres maiúsculos e minúsculos também pode causar estragos na navegação facetada, com laranja e Laranja aparecendo como dois valores diferentes. 
* Versões do mesmo valor no singular e no plural podem resultar em uma faceta separada para cada uma.

Como você pode imaginar, a auditoria na preparação dos dados é um aspecto essencial para uma navegação facetada eficiente.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a interface do usuário
Trabalhar na camada de apresentação pode ajudá-lo a descobrir requisitos que de outro modo poderiam ter sido ignorados, além de entender quais recursos são essenciais para a experiência de pesquisa.

Em termos de navegação facetada, sua página ou aplicativo da Web exibe a estrutura de navegação facetada, detecta a entrada do usuário na página e insere os elementos alterados. 

Para aplicativos da Web, o AJAX normalmente é usado na camada de apresentação porque ele permite que você atualize as alterações incrementais. Você também pode usar o MVC ASP.NET ou qualquer outra plataforma de visualização que possa se conectar a um serviço de Azure Search via HTTP. O aplicativo de exemplo referenciado neste artigo – a **Demonstração do Portal de Trabalho do Azure Search** – trata-se de um aplicativo ASP.NET MVC.

No exemplo, a navegação facetada baseia-se na página de resultados da pesquisa. O exemplo a seguir, extraído do arquivo `index.cshtml` do aplicativo de exemplo, mostra a estrutura HTML estática para exibir a navegação facetada em sua página de resultados da pesquisa. A lista de facetas é criada ou recriada dinamicamente quando você envia um termo de pesquisa ou marca ou desmarca uma faceta.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

O snippet de código a seguir da página `index.cshtml` cria dinamicamente o HTML para exibir a primeira faceta, Cargo. Funções semelhantes criam dinamicamente o HTML para as outras facetas. Cada faceta tem um rótulo e uma contagem, que exibe o número de itens encontrados para o resultado dessa faceta.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Ao criar a página de resultados da pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se você adicionar caixas de seleção, poderá ver com facilidade como limpar os filtros. Para outros layouts, talvez seja necessário um padrão de navegação estrutural ou outra abordagem criativa. Por exemplo, no aplicativo de exemplo do Portal de Pesquisa de Trabalho, você pode clicar no `[X]` após uma faceta selecionada para limpar a faceta.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Compilar a consulta
O código que você escreve para a compilação de consultas deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação – tudo o que for usado para formular uma solicitação. Nesta seção, exploramos onde as facetas se encaixam em uma consulta e como os filtros são usados com facetas para fornecer um conjunto de resultados reduzido.

Observe que as facetas são integrais nesse aplicativo de exemplo. A experiência de pesquisa na Demonstração do Portal de Trabalho foi criada em torno de filtros e navegação facetada. O posicionamento proeminente de navegação facetada na página demonstra sua importância. 

Um exemplo é geralmente um bom lugar para começar. O exemplo a seguir, extraído do arquivo `JobsSearch.cs`, compila uma solicitação que cria a navegação facetada com base no Cargo, no Local, no Tipo de Lançamento e no Salário Mínimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de faceta para consulta é definido como um campo e, dependendo do tipo de dados, pode ser ainda mais parametrizado por uma lista delimitada por vírgulas que inclua `count:<integer>`, `sort:<>`, `interval:<integer>` e `values:<list>`. Há suporte para dados numéricos em uma lista de valores ao configurar intervalos. Consulte [Pesquisar documentos (API do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter detalhes de utilização.

Além de facetas, a solicitação formulada pelo seu aplicativo também deve criar filtros para restringir o conjunto de documentos candidatos com base em uma seleção de valor da faceta. Para uma loja de bicicletas, a navegação facetada oferece dicas para perguntas como *Quais cores, fabricantes e tipos de bicicletas estão disponíveis?* . A filtragem de respostas a perguntas como *Quais bicicletas exatamente são vermelhas, mountain bikes e estão neste intervalo de preços?* . Quando você clica em "Vermelho" para indicar que somente os produtos vermelhos devem ser mostrados, a próxima consulta enviada pelo aplicativo inclui `$filter=Color eq ‘Red’`.

O seguinte snippet de código da página `JobsSearch.cs` adiciona o Cargo selecionado de negócios ao filtro se você selecionar um valor na faceta Cargo.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Dicas e melhores práticas

### <a name="indexing-tips"></a>Dicas de indexação
**Melhorar a eficiência de índice se você não usar uma caixa de Pesquisa**

Se seu aplicativo usa a navegação facetada exclusivamente (ou seja, sem nenhuma caixa de pesquisa), marque o campo como `searchable=false`, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre somente em valores de faceta inteiros, sem nenhuma quebra de palavra nem indexação das partes componentes de um valor com várias palavras.

**Definir quais campos podem ser usados como facetas**

Lembre-se que o esquema de índice determina quais campos estão disponíveis para uso como uma faceta. Supondo que um campo seja passível de facetagem, a consulta especifica por quais campos realizar essa facetagem. O campo pelo qual você está realizando a facetagem fornece os valores que aparecem abaixo do rótulo. 

Os valores que aparecem em cada rótulo são recuperados do índice. Por exemplo, se o campo da faceta for *Cor*, os valores disponíveis para filtragem adicional serão os valores para esse campo - Vermelho, Preto e assim por diante.

Apenas para valores dos campos Numérico e DataHora, você pode definir explicitamente os valores no campo da faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). Uma lista de valores é permitida para esses tipos de campo para simplificar a separação dos resultados da faceta em intervalos contíguos (qualquer um dos intervalos com base em valores numéricos ou períodos de tempo). 

**Por padrão, você só pode ter um nível de navegação facetada** 

Como observado, não há nenhum suporte direto para aninhamento de facetas em uma hierarquia. Por padrão, a navegação facetada no Azure Search dá suporte a apenas um nível de filtros. No entanto, existem soluções alternativas. Você pode codificar uma estrutura hierárquica de facetas em um `Collection(Edm.String)` com um ponto de entrada por hierarquia. Implementar essa solução alternativa está além do escopo deste artigo. 

### <a name="querying-tips"></a>Dicas de consulta
**Validar campos**

Se você criar a lista de facetas dinamicamente com base na entrada do usuário não confiável, valide se os nomes dos campos facetados são válidos. Ou escape os nomes ao criar URLs usando `Uri.EscapeDataString()` no .NET ou o equivalente em sua plataforma de preferência.

### <a name="filtering-tips"></a>Dicas de filtragem
**Aumentar a precisão da pesquisa com filtros**

Utilize filtros. Se você depender apenas de expressões de pesquisa, a lematização pode fazer com que seja retornado um documento que não tenha o valor preciso da faceta em nenhum de seus campos.

**Aumentar o desempenho da pesquisa com filtros**

Filtros restringem o conjunto de documentos candidatos para pesquisa e exclui-los da classificação. Se você tiver um grande conjunto de documentos, usar uma busca detalhada de faceta seletiva geralmente proporciona melhor desempenho.
  
**Filtrar somente os campos facetados**

Em uma busca detalhada facetada, você geralmente deseja incluir apenas os documentos que tenham o valor da faceta em um campo específico (facetado), não em qualquer lugar por todos os campos de pesquisa pesquisáveis. A adição de um filtro reforça o campo alvo, direcionando o serviço para pesquisar somente no campo facetado por um valor correspondente.

**Recortar resultados da faceta com mais filtros**

Resultados da faceta são documentos encontrados nos resultados da pesquisa que correspondem a um termo usado como faceta. No exemplo a seguir, nos resultados da pesquisa para *computação em nuvem*, 254 itens também têm *especificação interna* como um tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item atende aos critérios de ambos os filtros, ele será contado em cada um deles. Essa duplicação é possível ao realizar a facetagem em campos `Collection(Edm.String)`, que geralmente são usados para implementar a marcação do documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se você descobrir que os resultados da faceta costumam ser muito grandes, é recomendável adicionar mais filtros para fornecer aos usuários mais opções para limitar a pesquisa.

### <a name="tips-about-result-count"></a>Dicas sobre contagem de resultados

**Limitar o número de itens na navegação facetada**

Para cada campo facetado na árvore de navegação, há um limite padrão de 10 valores. Esse padrão faz sentido para estruturas de navegação porque mantém a lista de valores de um tamanho gerenciável. Você pode substituir o padrão, atribuindo um valor para a contagem.

* `&facet=city,count:5` especifica que apenas as cinco primeiras cidades localizadas nos resultados com a melhor classificação são retornadas como um resultado da faceta. Considere um exemplo de consulta com um termo de pesquisa "aeroporto" e 32 correspondências. Se a consulta especificar `&facet=city,count:5`, somente as cinco primeiras cidades únicas com a maioria dos documentos nos resultados da pesquisa serão incluídas nos resultados da faceta.

Observe a diferença entre os resultados da pesquisa e os resultados da faceta. Os resultados da pesquisa são todos os documentos que correspondem à consulta. Os resultados da faceta são as correspondências para cada valor da faceta. No exemplo, os resultados da pesquisa incluem nomes de cidades que não estão na lista de classificação da faceta (5 em nosso exemplo). Os resultados que são filtrados por meio de navegação facetada se tornam visíveis quando você limpa facetas ou escolhe outras facetas além de Cidade. 

> [!NOTE]
> Discutir `count` quando há mais de um tipo pode ser confuso. A tabela a seguir oferece um breve resumo de como o termo é usado na API de Azure Search, um código de exemplo e a documentação. 

* `@colorFacet.count`<br/>
  No código de apresentação, você deverá ver um parâmetro de contagem na faceta, usada para exibir o número de resultados da faceta. Nos resultados da faceta, a contagem indica o número de documentos que correspondam ao intervalo ou termo da faceta em questão.
* `&facet=City,count:12`<br/>
  Em uma consulta de faceta, você pode definir a contagem para um determinado valor.  O padrão é 10, mas você pode defini-lo com um valor maior ou menor. Definir `count:12` retorna as 12 correspondências melhor classificadas nos resultados da faceta pela contagem de documento.
* "`@odata.count`"<br/>
  Na resposta da consulta, esse valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, ele é maior que a soma de todos os resultados de faceta combinados, devido à presença de itens que correspondem ao termo de pesquisa, mas não tem correspondências com o valor da faceta.

**Obter contagens dos resultados da faceta**

Ao adicionar um filtro a uma consulta facetada, talvez você queira manter a instrução da faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, facet=Rating não é necessária, mas mantê-la retorna as contagens de valores de faceta para classificações iguais ou superiores a 4. Por exemplo, se você clica em "4" e a consulta inclui um filtro para maior ou igual a "4", as contagens são retornadas para cada classificação igual ou superior a 4.  

**Certifique-se de obter contagens de facetas precisas**

Em determinadas circunstâncias, você pode achar que as contagens de faceta não coincidem com os conjuntos de resultados (consulte [Navegação facetada no Azure Search (postagem no fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

As contagens de faceta podem ser imprecisas devido à arquitetura de fragmentação. Cada índice de pesquisa tem vários fragmentos, e cada um deles relata as N facetas com maior classificação por contagem de documento, que em seguida são combinadas em um único resultado. Se alguns fragmentos têm muitos valores correspondentes enquanto outros têm menos, você poderá descobrir que alguns valores de faceta estão faltando ou com contagem reduzida nos resultados.

Embora esse comportamento possa mudar a qualquer momento, se você encontrar esse problema hoje, você pode contorná-lo artificialmente aumentando count:\<número > para um número grande para impor a emissão de relatórios completos de cada fragmento. Se o valor de contagem: é maior que ou igual ao número de valores exclusivos no campo, você tem a garantia de resultados precisos. No entanto, quando as contagens de documento são altas há uma penalidade de desempenho, portanto, use essa opção criteriosamente.

### <a name="user-interface-tips"></a>Dicas da interface do usuário
**Adicionar rótulos para cada campo na navegação facetada**

Os rótulos geralmente são definidos no HTML ou no formulário (`index.cshtml` no aplicativo de exemplo). Não há API no Azure Search para rótulos de navegação facetada ou qualquer outro metadado.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrar com base em um intervalo
A facetagem em intervalos de valores é um requisito comum de aplicativo de pesquisa. Intervalos têm suporte para dados numéricos e valores de DataHora. Você pode ler mais sobre cada abordagem em [Procurar documentos (API do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

O Azure Search simplifica a construção do intervalo, fornecendo duas abordagens para a computação de um intervalo. Para ambas as abordagens, o Azure Search cria os intervalos apropriados, considerando as entradas que você forneceu. Por exemplo, se você especificar valores de intervalo de 10|20|30, ele criará automaticamente intervalos de 0-10, 10-20, 20-30 -10, 10-20 e 20-30. Seu aplicativo pode, opcionalmente, remover quaisquer intervalos que estejam vazios. 

**Abordagem 1: Usar o parâmetro de intervalo**  
Para definir facetas de preço em incrementos de US$ 10, você deve especificar: `&facet=price,interval:10`

**Abordagem 2: Usar uma lista de valores**  
Para dados numéricos, você pode usar uma lista de valores.  Considere o intervalo da faceta para um campo `listPrice`, renderizado como a seguir:

  ![Lista de valores de exemplo](media/search-faceted-navigation/Facet-5-Prices.PNG "lista de valores de exemplo")

Para especificar um intervalo da faceta como a mostrada na captura de tela anterior, use uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada intervalo é criado usando 0 como ponto de partida, um valor da lista como um ponto de extremidade e então recortado do intervalo anterior para criar intervalos discretos. O Azure Search faz essas coisas como parte da navegação facetada. Você não precisa escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Criar um filtro para um intervalo
Para filtrar os documentos com base em um intervalo selecionado por você, será possível usar os operadores de filtragem `"ge"` e `"lt"` em uma expressão de duas partes que defina os pontos de extremidade do intervalo. Por exemplo, se você escolher o intervalo 10-25 para uma `listPrice` campo, o filtro será `$filter=listPrice ge 10 and listPrice lt 25`. No código de exemplo, a expressão de filtro usa os parâmetros **priceFrom** e **priceTo** para definir os pontos de extremidade. 

  ![Consultar um intervalo de valores](media/search-faceted-navigation/Facet-6-buildfilter.PNG "consulta para um intervalo de valores")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrar com base na distância
É comum ver filtros que ajudam você a escolher uma loja, restaurante ou destino com base na proximidade desse local com relação ao local onde você está atualmente. Embora esse tipo de filtro possa parecer com a navegação facetada, trata-se apenas de um filtro. Mencionamos isso aqui para aqueles que estão procurando especificamente conselhos de implementação para um problema de design específico.

Há duas funções geoespaciais no Azure Search, **geo.distance** e **geo.intersects**.

* A função **geo.distance** retorna a distância em quilômetros entre dois pontos. Um ponto é um campo e o outro é uma constante passada como parte do filtro. 
* A função **geo.intersects** retornará true se um determinado ponto estiver dentro de um polígono determinado. O ponto é um campo e o polígono é especificado como uma lista constante de coordenadas passadas como parte do filtro.

Você pode encontrar exemplos de filtros em [Sintaxe de expressão OData (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Experimente a demonstração
A Demonstração do Portal de Trabalho do Azure Search contém os exemplos mencionados neste artigo.

-   Veja e teste a demonstração de trabalho online na [Demonstração do portal do trabalho do Azure Search](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código do [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Ao trabalhar com os resultados da pesquisa, monitore a URL quanto a alterações na construção de consulta. Esse aplicativo acrescenta facetas ao URI conforme você seleciona cada uma.

1. Para usar a funcionalidade de mapeamento do aplicativo de demonstração, obtém uma chave do Bing Mapas no [Centro de Desenvolvimento do Bing Mapas](https://www.bingmapsportal.com/). Cole-a sobre a chave existente na página `index.cshtml`. A configuração `BingApiKey` no arquivo `Web.config` não é usada. 

2. Execute o aplicativo. Faça o tour opcional ou ignore a caixa de diálogo.
   
3. Insira um termo de pesquisa, como "analista" e clique no ícone de pesquisa. A consulta é executada rapidamente.
   
   Uma estrutura de navegação facetada também é retornada com os resultados da pesquisa. Na página de resultados de pesquisa, a estrutura de navegação facetada inclui contagens para cada resultado da faceta. Nenhuma faceta está selecionada, todos os resultados correspondentes são retornados.
   
   ![Resultados da pesquisa antes de selecionar facetas](media/search-faceted-navigation/faceted-search-before-facets.png "resultados da pesquisa antes de selecionar facetas")

4. Clique em um Cargo, Local ou Salário Mínimo. As facetas eram nulas na pesquisa inicial, mas já que elas recebem valores, os resultados da pesquisa são recortados eliminando-se os itens que não correspondem mais.
   
   ![Resultados da pesquisa depois de selecionar facetas](media/search-faceted-navigation/faceted-search-after-facets.png "resultados da pesquisa depois de selecionar facetas")

5. Para limpar a consulta facetada para que você possa tentar comportamentos de consulta diferentes, clique o `[X]` após as facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saiba mais
Assista [Aprofundamento no Azure Search](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Em 45:25, há uma demonstração de como implementar facetas.

Para obter mais informações sobre princípios de design para navegação facetada, recomendamos os seguintes links:

* [Padrões de design: Faceted Navigation](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Preocupações de Front-End ao implementar uma pesquisa facetada – parte 1](https://articles.uie.com/faceted_search2/)

