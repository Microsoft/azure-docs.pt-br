---
title: "Unidades de solicitação e estimativa de produtividade – Azure Cosmos DB | Microsoft Docs"
description: "Saiba mais sobre como entender, especificar e estimar os requisitos de unidades de solicitação no Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 83cbc622975344ec2a5700d2e10a5c77371e9899
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB
Agora disponível: [calculadora de unidades de solicitação](https://www.documentdb.com/capacityplanner) do Azure Cosmos DB. Saiba mais em [Estimativa das necessidades de produção](request-units.md#estimating-throughput-needs).

![Calculadora de produtividade][5]

## <a name="introduction"></a>Introdução
O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é o multimodelo de banco de dados distribuído globalmente da Microsoft. Com o Azure Cosmos DB, você não precisa alugar máquinas virtuais, implantar softwares ou monitorar bancos de dados. O Azure Cosmos DB é operado e continuamente monitorado pelos melhores engenheiros da Microsoft para fornecer disponibilidade, desempenho e proteção de dados da mais alta qualidade. Você pode acessar seus dados usando as APIs de sua preferência, como [SQL do DocumentDB](documentdb-sql-query.md) (documento), MongoDB (documento), [Armazenamento de Tabelas do Azure](https://azure.microsoft.com/services/storage/tables/) (chave-valor) e [Gremlin](https://tinkerpop.apache.org/gremlin.html) (gráfico), todas com suporte nativo. A moeda do Azure Cosmos DB é a RU (Unidade de Solicitação). Com RUs, você não precisa reservar capacidades de leitura/gravação nem provisionar CPU, Memória e IOPS.

O Azure Cosmos DB dá suporte a uma série de APIs com operações diferentes que variam de leituras e gravações simples a consultas de gráfico complexas. Como nem todas as solicitações são iguais, elas são atribuídas a uma quantidade normalizada de **unidades de solicitação** com base na quantidade de computação necessária para atender à solicitação. O número de unidades de solicitação de uma operação é determinístico e você pode acompanhar o número de unidades de solicitação consumidas por uma operação no Azure Cosmos DB por meio de um cabeçalho de resposta. 

Para fornecer um desempenho previsível, você precisa reservar a produtividade em unidades de 100 RUs/segundo. 

Após ler este artigo, você poderá responder as perguntas a seguir:  

* O que são unidades de solicitação e solicitações de encargos?
* Como especificar a capacidade da unidade de solicitação para uma coleção?
* Como estimar as necessidades de unidades de solicitação de meu aplicativo?
* O que acontecerá se eu exceder a capacidade da unidade de solicitação de uma coleção?

Como o Azure Cosmos DB é um multimodelo de banco de dados, é importante observar que nos referiremos a uma coleção ou um documento como uma API de documento, um gráfico/nó como uma API de gráfico e uma tabela/entidade como uma API de tabela. Neste documento generalizaremos para os conceitos de contêiner/item.

## <a name="request-units-and-request-charges"></a>Unidades de solicitação e solicitações de encargos
O Azure Cosmos DB fornece desempenho rápido e previsível *reservando* recursos para atender às necessidades de produtividade do aplicativo.  Como os padrões de carga e acesso do aplicativo mudam com o tempo, o Azure Cosmos DB permite que você aumente ou diminua facilmente a quantidade de produtividade reservada disponível para o aplicativo.

Com o Azure Cosmos DB, a produtividade reservada é especificada em termos de processamento de unidades de solicitação por segundo. Você pode considerar as unidades de solicitação como a moeda de produtividade, com as quais você *reserva* uma quantidade de unidades de solicitação garantidas disponíveis para o aplicativo por segundo.  Cada operação do Azure Cosmos DB – gravar um documento, realizar uma consulta, atualizar um documento – consome CPU, memória e IOPS.  Ou seja, cada operação resulta em um *encargo de solicitação*, que é expressa em *unidades de solicitação*.  Ao entender os fatores que afetam os encargos de unidade de solicitação e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo da maneira mais econômica possível. O gerenciador de consultas também é uma ferramenta excelente para testar o núcleo de uma consulta.

Recomendamos que você comece assistindo ao vídeo a seguir, no qual Aravind Ramachandran explica as unidades de solicitação e o desempenho previsível com o Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Especificando a capacidade de unidades de solicitação no Azure Cosmos DB
Ao iniciar uma nova coleção, tabela ou um novo gráfico, especifique o número de unidades de solicitação por segundo (RU por segundo) que você deseja ter reservado. Com base na produtividade provisionada, o Azure Cosmos DB aloca partições físicas para hospedar sua coleção e divide/redistribui os dados entre partições conforme eles aumentam.

O Azure Cosmos DB exige que uma chave de partição seja especificada quando uma coleção é provisionada com 2.500 unidades de solicitação ou mais. Uma chave de partição também é necessária para dimensionar a produtividade da coleção além das 2.500 unidades de solicitação no futuro. Portanto, é altamente recomendável configurar uma [chave de partição](partition-data.md) durante a criação de um contêiner, independentemente da produtividade inicial. Como os dados podem estar divididos em várias partições, é necessário escolher uma chave de partição que tem uma cardinalidade alta (centenas a milhões de valores distintos), de forma que a coleção, a tabela ou o gráfico e as solicitações possam ser dimensionados de maneira uniforme pelo Azure Cosmos DB. 

> [!NOTE]
> Uma chave de partição é um limite lógico e não físico. Portanto, não é necessário limitar o número de valores de chave de partição distinta. Na verdade, já que o Azure Cosmos DB tem mais opções de balanceamento de carga, é melhor ter mais valores de chave de partição distintos do que menos.

Segue um trecho de código para criar uma coleção com 3.000 unidades de solicitação por segundo usando o SDK do .NET:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

O Azure Cosmos DB opera em um modelo de reserva na produtividade. Ou seja, você será cobrado pela quantidade de produtividade *reservada*, independentemente do quanto da produtividade estiver em *uso*. À medida que a carga, os dados e os padrões de uso do aplicativo mudarem, você poderá aumentar ou reduzir verticalmente a quantidade de RUs reservadas por meio de SDKs ou usando o [Portal do Azure](https://portal.azure.com).

Cada coleção/tabela/gráfico é mapeado para um recurso `Offer` no Azure Cosmos DB, que tem metadados sobre a produtividade provisionada. Altere a produtividade alocada procurando o recurso de oferta correspondente de um contêiner e, em seguida, atualizando-o com o novo valor de produtividade. Aqui está um trecho de código para alterar a taxa de transferência de uma coleção para 5.000 unidades de solicitação por segundo usando o SDK do .NET:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Não há nenhum impacto sobre a disponibilidade do contêiner quando a produtividade é alterada. Normalmente, a nova taxa de transferência reservada se torna eficaz em segundos no aplicativo da nova taxa de transferência.

## <a name="request-unit-considerations"></a>Considerações sobre unidades de solicitação
Ao estimar o número de unidades de solicitação a ser reservado para o contêiner do Azure Cosmos DB, é importante considerar as seguintes variáveis:

* **Tamanho do item**. À medida que o tamanho aumentar, as unidades consumidas para ler ou gravar os dados também aumentarão.
* **Contagem de propriedades do item**. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento, um nó ou uma entidade aumentarão conforme a contagem de propriedades aumentar.
* **Consistência de dados**. Ao usar os níveis de consistência de dados Forte ou Desatualização Limitada, unidades adicionais serão consumidas para ler os itens.
* **Propriedades indexadas**. Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas ou habilitando a indexação lenta.
* **Indexação de documentos**. Por padrão, cada item é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns dos itens.
* **Padrões de consulta**. A complexidade de uma consulta afeta a quantidade de Unidades de Solicitação que são consumidas para uma operação. O número de predicados, a natureza dos predicados, as projeções, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.
* **Uso de scripts**.  Assim como ocorre com as consultas, os procedimentos armazenados e os gatilhos consomem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

## <a name="estimating-throughput-needs"></a>Estimativa das necessidades de produção
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler (por meio de self link ou ID) um único item de 1 KB que consiste em 10 valores de propriedade exclusivos (excluindo as propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo item consumirá mais processamento do serviço e, assim, mais unidades de solicitação.   

> [!NOTE]
> A linha de base de 1 unidade de solicitação para um item de 1 KB corresponde a um GET simples por self link ou ID do item.
> 
> 

Por exemplo, esta é uma tabela que mostra quantas unidades de solicitação devem ser provisionadas em três tamanhos de item diferentes (1 KB, 4 KB e 64 KB) e em dois níveis de desempenho diferentes (500 leituras/segundo + 100 gravações/segundo e 500 leituras/segundo + 500 gravações/segundo). A consistência dos dados foi configurada em Sessão e a política de indexação foi definida como Nenhuma.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamanho do item</strong></p></td>
            <td valign="top"><p><strong>Leituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Gravações/segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitação</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1.350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4.150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9.800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29.000 RU/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Usar a calculadora de unidade de solicitação
Para ajudar os clientes a ajustar as estimativas de produtividade, há uma [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) baseada na Web que ajuda a fazer uma estimativa dos requisitos de unidade de solicitação para operações comuns, incluindo:

* Criações de itens (gravações)
* Leituras de itens
* Exclusões de itens
* Atualizações de itens

A ferramenta também inclui suporte para estimar as necessidades de armazenamento de dados com base nos itens de exemplo fornecidos.

Usar a ferramenta é simples:

1. Carregue um ou mais itens representativos.
   
    ![Carregar itens na calculadora de unidades de solicitação][2]
2. Para estimar os requisitos de armazenamento de dados, insira o número total de itens que você espera armazenar.
3. Insira o número de operações de criação, leitura e atualização e exclusão de itens necessário (por segundo). Para estimar as cobranças da unidade de solicitação das operações de atualização de itens, carregue uma cópia do item de exemplo da etapa 1 acima que inclui atualizações típicas de campo.  Por exemplo, se as atualizações de itens normalmente modificarem duas propriedades chamadas lastLogin e userVisits, basta copiar o item de exemplo, atualizar os valores dessas duas propriedades e carregar o item copiado.
   
    ![Inserir requisitos de produtividade na calculadora de unidade de solicitação][3]
4. Clique em Calcular e examinar os resultados.
   
    ![Resultados da calculadora de unidade de solicitação][4]

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, carregue uma amostra da cada *tipo* do item típico na ferramenta e, depois, calcule os resultados.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usar o cabeçalho de resposta de encargos da solicitação do Azure Cosmos DB
Todas as respostas do serviço Azure Cosmos DB incluem um cabeçalho personalizado (`x-ms-request-charge`) que contém as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos SDKs do Azure Cosmos DB. No SDK .NET, RequestCharge é uma propriedade do objeto ResourceResponse.  Para consultas, o Gerenciador de Consultas do Azure Cosmos DB no portal do Azure fornece informações de encargos de solicitação para as consultas executadas.

![Análise de encargos de RU no Gerenciador de Consultas][1]

Tendo isso em mente, um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um item representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.  Também meça e inclua consultas típicas e o uso de scripts do Azure Cosmos DB.

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidades de solicitação da operação aplicável associado a cada *tipo* de item típico.
> 
> 

Por exemplo:

1. Registre o encargo de unidades de solicitação para a criação (inserção) de um item típico. 
2. Registre o encargo de unidades de solicitação para a leitura de um item típico.
3. Registre o encargo de unidades de solicitação para a atualização de um item típico.
4. Registre o encargo de unidades de solicitação para consultas de itens comuns e típicos.
5. Registre o encargo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, gatilhos, funções definidas pelo usuário) utilizados pelo aplicativo
6. Calcule as unidades de solicitação necessárias, dado o número estimado de operações que você prevê que executará por segundo.

### <a id="GetLastRequestStatistics"></a>Usar o comando GetLastRequestStatistics da API para MongoDB
A API para MongoDB dá suporte a um comando personalizado, *getLastRequestStatistics*, para recuperar a carga de solicitação de operações especificadas.

Por exemplo, no Shell do Mongo, execute a operação para a qual você deseja verificar a carga de solicitação.
```
> db.sample.find()
```

Em seguida, execute o comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Tendo isso em mente, um método para estimar a quantidade de produtividade reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um item representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo.

> [!NOTE]
> Se você tiver tipos de itens que são muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidades de solicitação da operação aplicável associado a cada *tipo* de item típico.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Usar as métricas de portal da API para MongoDB
A maneira mais simples de obter uma boa estimativa dos encargos da unidade de solicitação para seu banco de dados da API para MongoDB é usar as métricas do [Portal do Azure](https://portal.azure.com). Com os gráficos *Número de solicitações* e *Custo da Solicitação*, você pode obter uma estimativa de quantas unidades de solicitação cada operação está consumindo, e quantas unidades de solicitação elas consomem com relação às outras operações.

![Métricas do portal da API para MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Um exemplo de estimativa de unidade de solicitação
Considere o seguinte documento de aproximadamente 1 KB:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Os documentos são reduzidos no Azure Cosmos DB e, portanto, o tamanho do documento acima calculado pelo sistema é um pouco menor que 1 KB.
> 
> 

A seguinte tabela mostra os encargos de unidades de solicitação aproximados para operações típicas nesse item (o encargo de unidades de solicitação aproximado pressupõe que o nível de consistência de conta seja definido como “Sessão” e que todos os itens sejam indexados automaticamente):

| Operação | Encargo de Unidade de Solicitação |
| --- | --- |
| Criar item |Cerca de 15 RUs |
| Ler item |Cerca de 1 RU |
| Consultar item por ID |Cerca de 2,5 RUs |

Adicionalmente, a tabela mostra os encargos de unidade de solicitação aproximados para consultas típicas usadas no aplicativo:

| Consultar | Encargo de Unidade de Solicitação | Nº de itens retornados |
| --- | --- | --- |
| Selecionar alimentos por id |Cerca de 2,5 RUs |1 |
| Selecionar alimentos por fabricante |Cerca de 7 RUs |7 |
| Selecionar por grupo de alimentos e solicitar por peso |Cerca de 70 RUs |100 |
| Selecionar os 10 alimentos principais em um grupo de alimentos |Cerca de 10 RUs |10 |

> [!NOTE]
> Os encargos de RU variam com base no número de itens retornados.
> 
> 

Com essas informações, podemos estimar os requisitos de RU para o aplicativo, dado o número de operações e consultas que esperamos por segundo:

| Operação/consulta | Número estimado por segundo | RUs necessárias |
| --- | --- | --- |
| Criar item |10 |150 |
| Ler item |100 |100 |
| Selecionar alimentos por fabricante |25 |175 |
| Selecionar por grupo de alimentos |10 |700 |
| Selecionar os 10 principais |15 |Total de 150 |

Nesse caso, esperamos um requisito de taxa de transferência médio de 1.275 RUs/s.  Arredondando para a centena mais próxima, vamos provisionar 1.300 RUs/s para a coleção desse aplicativo.

## <a id="RequestRateTooLarge"></a> Excedendo os limites de produtividade reservada no Azure Cosmos DB
Lembre-se de que o consumo de unidades de solicitação será avaliado como uma taxa por segundo se o orçamento estiver vazio. Para aplicativos que excedem a taxa de unidades de solicitação provisionada de um contêiner, as solicitações a essa coleção serão limitadas até que a taxa fique abaixo do nível reservado. Quando ocorre uma restrição, o servidor encerra preventivamente a solicitação com RequestRateTooLargeException (código de status HTTP 429) e retorna o cabeçalho x-ms-retry-after-ms, indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se estiver usando as consultas do SDK do cliente .NET e LINQ, em seguida, na maioria das vezes, você nunca precisará lidar com essa exceção, pois a versão atual do SDK do Cliente .NET captura implicitamente essa resposta, respeita o cabeçalho retry-after especificado pelo servidor e tenta novamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente acima da taxa de solicitação, o comportamento de repetição padrão poderá não ser suficiente e o cliente lançará uma DocumentClientException com o código de status 429 para o aplicativo. Em casos como esse, considere a manipulação do comportamento de repetição e da lógica nas rotinas de tratamento de erro do aplicativo ou o aumento da produtividade reservada para o contêiner.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Exceder os limites de taxa de transferência reservada na API para MongoDB
Os aplicativos que ultrapassarem as unidades de solicitação provisionadas para uma coleção serão limitados até que a taxa caia para baixo do nível reservado. Quando ocorrer uma limitação, o back-end terminará preventivamente a solicitação com um código de erro *16500* - *Muitas Solicitações*. Por padrão, a API para MongoDB repetirá automaticamente até 10 vezes antes de retornar um código de erro *Muitas Solicitações*. Se você estiver recebendo códigos de erro *Muitas Solicitações*, considere a repetição nas rotinas de manipulação de erro de seu aplicativo ou [aumentar a taxa de transferência reservada para a coleção](set-throughput.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a produtividade reservada com bancos de dados do Azure Cosmos DB, conheça estes recursos:

* [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Particionando dados no Azure Cosmos DB](partition-data.md)

Para saber mais sobre o Azure Cosmos DB, consulte a [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do Azure Cosmos DB. 

Para obter uma introdução aos testes de escala e desempenho com o Azure Cosmos DB, consulte [Testes de desempenho e escala com o Azure Cosmos DB](performance-testing.md).

[1]: ./media/request-units/queryexplorer.png 
[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png
