---
title: 'Início Rápido: Criar um índice de pesquisa no JavaScript'
titleSuffix: Azure Cognitive Search
description: Neste guia de início rápido do JavaScript, saiba como criar um índice, carregar dados e executar consultas no Azure Cognitive Search usando JavaScript
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509394"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Início Rápido: Criar um índice de Azure Cognitive Search usando o SDK do JavaScript
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


Use o [SDK do Javascript/Typscript para Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme) para criar um aplicativo Node.js em JavaScript que cria, carrega e consulta um índice de pesquisa.

Este artigo demonstra como criar o aplicativo passo a passo. Como alternativa, você pode [baixar o código-fonte e os dados](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) e executar o aplicativo na linha de comando.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Criar um serviço](search-create-service-portal.md) ou [localizar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). É possível usar um serviço gratuito para este início rápido. 

+ [Node.js](https://nodejs.org) e [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) ou outro IDE


## <a name="set-up-your-project"></a>Configurar o seu projeto

Comece obtendo o ponto de extremidade e a chave para o serviço de pesquisa. Em seguida, crie um novo projeto com NPM conforme descrito abaixo.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiar uma chave e um ponto de extremidade

As chamadas ao serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. Como uma primeira etapa, localize a chave de API e a URL para adicioná-las ao projeto. Você especificará os dois valores ao criar o cliente em uma etapa posterior.

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço, necessários se você estiver criando ou excluindo objetos. Há duas chaves primárias e secundárias intercambiáveis. Você pode usar qualquer uma delas.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-rest/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

### <a name="create-a-new-npm-project"></a>Crie um projeto NPM

Comece abrindo o VS Code e seu [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) ou outro terminal, como o prompt de comando do Node.js.

1. Crie um diretório de desenvolvimento e dê a ele o nome `quickstart`:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Inicialize um projeto vazio com NPM ao executar 

    ```cmd
    npm init
    ```
     Aceite os valores padrão, exceto para a licença, que você deve definir como "MIT". 

3. Instale `@azure/search-documents`, o [SDK do JavaScript/Typscript para o Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Instale o `dotenv`, que é usado para importar as variáveis de ambiente, como o nome do serviço e a chave de API.
    ```cmd
    npm install dotenv
    ```

5. Confirme que você configurou os projetos e suas dependências verificando se o arquivo **package.json** é semelhante ao seguinte json:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Crie um arquivo **.env** para manter os parâmetros do serviço de pesquisa:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Substitua o valor `<search-service-name>` pelo nome do serviço de pesquisa. Substitua `<search-admin-key>` pelos valores de chave que você registrou anteriormente. 

### <a name="create-indexjs-file"></a>Criar o arquivo index.js

Em seguida, criamos um arquivo **index.js**, que é o arquivo principal que hospedará nosso código.

Na parte superior desse arquivo, importe a biblioteca `@azure/search-documents`:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Em seguida, precisamos exigir que o pacote `dotenv` leia os parâmetros do arquivo **.env** da seguinte maneira:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Com nossas importações e variáveis de ambiente em vigor, estamos prontos para definir a função principal.

A maior parte da funcionalidade no SDK é assíncrona, portanto, tornamos nossa função principal `async`. Também incluímos um `main().catch()` abaixo da função principal para detectar e registrar quaisquer erros encontrados:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Com isso em vigor, estamos prontos para criar um índice.

## <a name="1---create-index"></a>1 – Criar o índice 

Crie um arquivo **hotels_quickstart_index. JSON**.  Este arquivo define como a Pesquisa Cognitiva do Azure funciona com os documentos que você carregará na próxima etapa. Cada campo será identificado por um `name` e terá um `type` especificado. Cada campo também tem uma série de atributos de índice que especificam se a Pesquisa Cognitiva do Azure pode pesquisar, filtrar, classificar e facetar o campo. A maioria dos campos é composta por tipos de dados simples, mas alguns, como `AddressType`, são tipos complexos que permitem que você crie estruturas de dados avançadas em seu índice.  Leia mais sobre os [tipos de dados compatíveis](/rest/api/searchservice/supported-data-types) e os atributos de índice descritos em [Criar índice (REST)](/rest/api/searchservice/create-index). 

Adicione o seguinte a **hotels_quickstart_index.json** ou [baixe o arquivo](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Com nossa definição de índice em vigor, queremos importar **hotels_quickstart_index. JSON** na parte superior do **index.js** para que a função principal possa acessar a definição do índice.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

Na função principal, criamos um `SearchIndexClient`, que é usado para criar e gerenciar índices para o Azure Cognitive Search. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Em seguida, queremos excluir o índice, caso ele já exista. Essa é uma prática comum para código de teste/demonstração.

Fazemos isso definindo uma função simples que tenta excluir o índice.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Para executar a função, extraímos o nome do índice da definição do índice e passamos o `indexName` junto com o `indexClient` para a função `deleteIndexIfExists()`.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Depois disso, estamos prontos para criar o índice com o método `createIndex()`.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Execute o exemplo

E, neste ponto, você já pode executar o exemplo. Use uma janela de terminal para executar o comando a seguir:

```cmd
node index.js
```

Se você [baixou o código-fonte](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) e ainda não instalou os pacotes necessários, execute `npm install` primeiro.

Você deve ver uma série de mensagens que descrevem as ações que estão sendo executadas pelo programa. 

Abra a **Visão geral** do serviço de pesquisa no portal do Azure. Selecione a guia **Índices**. Você verá algo semelhante ao que se segue:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Captura de tela do portal do Azure, Visão Geral do Serviço de Pesquisa, guia Índices" border="false":::

Na próxima etapa, você adicionará dados ao índice. 

## <a name="2---load-documents"></a>2 - Carregar documentos 


Na Pesquisa Cognitiva do Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Você pode enviar esses dados para o índice ou usar um [indexador](search-indexer-overview.md). Nesse caso, programaticamente enviaremos os documentos para o índice.

As entradas de documento podem ser linhas em um banco de dados, blobs no armazenamento de Blobs ou, como neste exemplo, documentos JSON em disco. Você pode baixar [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) ou criar seu próprio arquivo **hotels.json** com o seguinte conteúdo:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Semelhante ao que fizemos com o indexDefinition, também precisamos importar `hotels.json` na parte superior do **index.js** para que os dados possam ser acessados em nossa função principal.

```javascript
const hotelData = require('./hotels.json');
```


Para indexar dados no índice de pesquisa, agora precisamos criar um `SearchClient`. Embora o `SearchIndexClient` seja usado para criar e gerenciar um índice, o `SearchClient` é usado para carregar documentos e consultar o índice.

Há duas maneiras de criar um `SearchClient`. A primeira opção é criar um `SearchClient` do zero:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Como alternativa, você pode usar o método `getSearchClient()` de `SearchIndexClient` para criar o `SearchClient`:

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Agora que o cliente está definido, carregue os documentos no índice de pesquisa. Nesse caso, usamos o método `mergeOrUploadDocuments()`, que vai carregar os documentos ou mesclá-los com um documento existente se já houver um documento com a mesma chave.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Execute o programa novamente com `node index.js`. Você verá um conjunto de mensagens ligeiramente diferente daquelas vistas na Etapa 1. Desta vez, o índice *existe* e você deverá ver uma mensagem sobre como excluí-lo antes que o aplicativo crie o novo índice e poste dados nele. 

Antes de executarmos as consultas na próxima etapa, defina uma função para que o programa aguarde um segundo. Isso é feito apenas para fins de teste/demonstração, para garantir que a indexação seja concluída e que os documentos estejam disponíveis no índice para nossas consultas.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Para que o programa aguarde um segundo, chame a função `sleep`, como abaixo:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Com um índice criado e documentos carregados, você está pronto para enviar consultas para o índice. Nesta seção, enviaremos cinco consultas diferentes para o índice de pesquisa para demonstrar diferentes partes da funcionalidade de consulta disponível para você.

As consultas são gravadas em uma função `sendQueries()` que chamaremos na função principal da seguinte maneira:

```javascript
await sendQueries(searchClient);
```

As consultas são enviadas usando o método `search()` de `searchClient`. O primeiro parâmetro é o texto de pesquisa e o segundo parâmetro é qualquer opção de pesquisa adicional.

A primeira consulta pesquisa `*`, que é equivalente a pesquisar tudo e seleciona três dos campos no índice. É uma prática recomendada a apenas `select` dos campos de que você precisa, pois a extração de dados desnecessários pode adicionar latência às suas consultas.

O `searchOptions` para essa consulta também tem `includeTotalCount` definido como `true`, o que retornará o número de resultados correspondentes encontrados.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

As consultas restantes descritas abaixo também devem ser adicionadas à função `sendQueries()`. Elas são separadas aqui para facilitar a leitura.

Na próxima consulta, especificamos o termo de pesquisa `"wifi"` e incluímos um filtro para retornar apenas os resultados em que o estado é igual a `'FL'`. Os resultados também são ordenados pela `Rating` do hotel.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Em seguida, a pesquisa é limitada a um único campo pesquisável usando o parâmetro `searchFields`. Essa é uma ótima opção para tornar sua consulta mais eficiente, se você souber que está interessado apenas em correspondências em determinados campos. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Outra opção comum a ser incluída em uma consulta é `facets`. As facetas permitem que você crie filtros em sua interface do usuário para tornar mais fácil para os usuários saberem quais valores eles podem filtrar.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

A consulta final usa o método `getDocument()` de `searchClient`. Isso permite que você recupere com eficiência um documento por sua chave. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Execute o exemplo

Execute o programa com `node index.js`. Agora, além das etapas anteriores, as consultas serão enviadas e os resultados gravados no console.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido do JavaScript, você trabalhou com uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. 

Se você já tiver alguma experiência com a Pesquisa Cognitiva do Azure, use este exemplo como um trampolim para experimentar sugestões (consultas de preenchimento automático ou de digitação antecipada), filtros e navegação facetada. Se você for novo na Pesquisa Cognitiva do Azure, recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. 

> [!div class="nextstepaction"]
> [Criar um front-end de reação para Azure Cognitive Search](https://github.com/dereklegenzoff/azure-search-react-template)