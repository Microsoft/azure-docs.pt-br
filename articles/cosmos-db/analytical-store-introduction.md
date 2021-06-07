---
title: O que é Azure Cosmos DB repositório analítico?
description: Saiba mais sobre o repositório transacional (baseado em linha) e analítico (baseado em coluna) do Azure Cosmos DB. Benefícios do repositório analítico, impacto no desempenho para cargas de trabalho de larga escala e sincronização automática de dados do repositório transacional para o repositório analítico
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 450514541a90a01ea6b70f77491f116adb404887
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046204"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>O que é Azure Cosmos DB repositório analítico?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB repositório analítico é um repositório de coluna totalmente isolado para habilitar a análise em larga escala contra dados operacionais em seu Azure Cosmos DB, sem nenhum impacto em suas cargas de trabalho transacionais. 

O repositório transacional do Azure Cosmos DB é independente de esquema e permite que você faça uma iteração nos seus aplicativos transacionais sem a necessidade de lidar com gerenciamento de índices ou de esquemas. De modo contrastante, o repositório analítico do Azure Cosmos DB é esquematizado para otimizar o desempenho de consultas analíticas. Este artigo descreve detalhadamente o armazenamento analítico.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Desafios com análise em larga escala sobre dados operacionais

Os dados operacionais multimodelo em um contêiner do Azure Cosmos DB são armazenados internamente em um "repositório transacional" baseado em linha e indexado. O formato de repositório de linha foi projetado para permitir leituras e gravações transacionais rápidas nos tempos de resposta na casa de milissegundos e consultas operacionais. Se o conjunto de dados aumentar, as consultas analíticas complexas poderão ser caras em termos de produtividade provisionada nos dados armazenados nesse formato. O alto consumo de produtividade provisionada, por sua vez, afeta o desempenho de cargas de trabalho transacionais que são usadas por seus aplicativos e serviços em tempo real.

Tradicionalmente, para analisar grandes quantidades de dados, os dados operacionais são extraídos do repositório transacional do Azure Cosmos DB e armazenados em uma camada de dados separada. Por exemplo, os dados são armazenados em um data warehouse ou em um data lake em um formato adequado. Esses dados são usados posteriormente para a análise em larga escala e são analisados usando o um mecanismo de computação, como os clusters do Apache Spark. Essa separação do armazenamento analítico e das camadas de computação dos dados operacionais resulta em latência adicional, pois os pipelines de ETL (extração, transformação e carregamento) são executados com menos frequência para minimizar o impacto potencial em suas cargas de trabalho transacionais.

Os pipelines de ETL também se tornam complexos ao lidar com atualizações dos dados operacionais quando comparados ao tratamento apenas de dados operacionais recentemente ingeridos. 

## <a name="column-oriented-analytical-store"></a>Repositório analítico orientado por coluna

O repositório analítico do Azure Cosmos DB aborda os desafios de complexidade e latência que ocorrem com os pipelines de ETL tradicionais. O repositório analítico do Azure Cosmos DB pode sincronizar automaticamente seus dados operacionais em um repositório de coluna separado. O formato de repositório de coluna é adequado para consultas analíticas em larga escala a serem executadas de maneira otimizada, resultando na melhoria da latência dessas consultas.

Usando o link Synapse do Azure, agora você pode criar soluções de HTAP sem ETL vinculando diretamente a Azure Cosmos DB repositório analítico do Azure Synapse Analytics. Isso permite que você execute análises de larga escala quase em tempo real em seus dados operacionais.

## <a name="features-of-analytical-store"></a>Recursos do repositório analítico 

Quando você habilitar o repositório analítico em um contêiner do Azure Cosmos DB, um novo repositório de coluna será criado internamente com base nos dados operacionais em seu contêiner. Esse repositório de coluna é persistido separadamente do repositório transacional orientado por linha desse contêiner. As inserções, atualizações e exclusões dos seus dados operacionais são sincronizadas automaticamente ao repositório analítico. Você não precisa do feed de alterações ou da ETL para sincronizar os dados.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Repositório de coluna para cargas de trabalho analíticas em dados operacionais

As cargas de trabalho analíticas normalmente envolvem agregações e exames sequenciais dos campos selecionados. Ao armazenar os dados em uma ordem de coluna principal, o repositório analítico permite que um grupo de valores de cada campo seja serializado em conjunto. Esse formato reduz as IOPS necessárias para verificar ou computar estatísticas em campos específicos. Ele melhora significativamente os tempos de resposta de consulta para verificações em grandes conjuntos de dados. 

Por exemplo, se suas tabelas operacionais estiverem no seguinte formato:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Exemplo de tabela operacional" border="false":::

O repositório de linha persiste os dados acima em um formato serializado, por linha, no disco. Esse formato permite leituras, gravações e consultas operacionais transacionais mais rápidas, como “Retornar informações sobre Product1”. No entanto, como o conjunto de dados aumenta e se você quiser executar consultas analíticas complexas sobre eles, isso pode se tornar caro. Por exemplo, se você quiser saber “as tendências de vendas de um produto sob a categoria chamada ‘equipamentos’ entre diferentes unidades de negócios e meses”, você precisará executar uma consulta complexa. Grandes exames nesse conjunto de recursos podem ser caros em termos de taxa de transferência provisionada e também podem impactar o desempenho das cargas de trabalho transacionais, capacitando seus aplicativos e serviços em tempo real.

O repositório analítico, que é um repositório de coluna, é mais adequado para essas consultas porque ele serializa campos semelhantes de dados juntos e reduz as IOPS de disco.

A imagem a seguir mostra o repositório de linhas transacional em comparação ao repositório de colunas analíticas no Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Repositório de linhas transacional em comparação ao repositório de colunas analíticas no Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Desempenho separado para cargas de trabalho analíticas

Não há nenhum impacto no desempenho de suas cargas de trabalho transacionais devido a consultas analíticas, pois o repositório analítico fica separado do repositório transacional.  O repositório analítico não precisa de RUs (unidades de solicitação) separadas para ser alocado.

### <a name="auto-sync"></a>Sincronização automática

A sincronização automática refere-se ao recurso totalmente gerenciado de Azure Cosmos DB onde as inserções, atualizações, exclusões em dados operacionais são sincronizadas automaticamente do armazenamento transacional para o repositório analítico em tempo quase real. A latência de sincronização automática geralmente é de 2 minutos. Em casos de banco de dados de produtividade compartilhado com um grande número de contêineres, a latência de sincronização automática de contêineres individuais pode ser maior e levar até 5 minutos. Gostaríamos de saber mais sobre como essa latência se adapta a seus cenários. Para isso, entre em contato com a [equipe de Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

O recurso de sincronização automática, junto com o repositório analítico, fornece os seguintes benefícios principais:

### <a name="scalability--elasticity"></a>Escalabilidade e elasticidade

Ao usar o particionamento horizontal, o repositório transacional do Azure Cosmos DB pode escalar elasticamente o armazenamento e a taxa de transferência sem nenhum tempo de inatividade. O particionamento horizontal no repositório transacional fornece escalabilidade e elasticidade na sincronização automática para garantir que os dados sejam sincronizados com o repositório analítico quase em tempo real. A sincronização de dados ocorre independentemente da taxa de transferência do tráfego transacional, sejam 1.000 ou 1 milhão de operações/s, e não impacta a taxa de transferência provisionada no repositório transacional. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Lidar automaticamente com atualizações de esquema

O repositório transacional do Azure Cosmos DB é independente de esquema e permite que você faça uma iteração nos seus aplicativos transacionais sem a necessidade de lidar com gerenciamento de índices ou de esquemas. De modo contrastante, o repositório analítico do Azure Cosmos DB é esquematizado para otimizar o desempenho de consultas analíticas. Com o recurso de sincronização automática, o Azure Cosmos DB gerencia a inferência de esquema sobre as atualizações mais recentes do armazenamento transacional.  Ele também gerencia a representação do esquema no repositório analítico pronto para uso, que inclui o tratamento de tipos de dados aninhados.

À medida que o esquema evolui e novas propriedades são adicionadas ao longo do tempo, o repositório analítico apresenta automaticamente um esquema Union em todos os esquemas históricos no armazenamento transacional.

#### <a name="schema-constraints"></a>Restrições de esquema

As seguintes restrições são aplicáveis aos dados operacionais em Azure Cosmos DB quando você habilita o repositório analítico para inferir e representar automaticamente o esquema corretamente:

* Você pode ter um máximo de 1000 Propriedades em qualquer nível de aninhamento no esquema e uma profundidade máxima de aninhamento de 127.
  * Somente as primeiras 1000 propriedades são representadas no repositório analítico.
  * Somente os primeiros 127 níveis aninhados são representados no repositório analítico.

* Embora os documentos JSON (e Cosmos DB coleções/contêineres) diferenciam maiúsculas de minúsculas da perspectiva de exclusividade, o repositório analítico não é.

  * **No mesmo documento:** Os nomes de propriedades no mesmo nível devem ser exclusivos quando comparados sem distinção entre maiúsculas e minúsculas. Por exemplo, o documento JSON a seguir tem "Name" e "Name" no mesmo nível. Embora seja um documento JSON válido, ele não satisfaz a restrição de exclusividade e, portanto, não será totalmente representado no repositório analítico. Neste exemplo, "Name" e "Name" são os mesmos quando comparados em uma maneira não sensível a maiúsculas e minúsculas. Somente `"Name": "fred"` será representado no repositório analítico, porque é a primeira ocorrência. E `"name": "john"` não serão representados.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **Em documentos diferentes:** As propriedades no mesmo nível e com o mesmo nome, mas em casos diferentes, serão representadas na mesma coluna, usando o formato de nome da primeira ocorrência. Por exemplo, os documentos JSON a seguir têm `"Name"` e `"name"` no mesmo nível. Como o primeiro formato de documento é `"Name"` , isso é o que será usado para representar o nome da propriedade no repositório analítico. Em outras palavras, o nome da coluna no repositório analítico será `"Name"` . Ambos `"fred"` e `"john"` serão representados na `"Name"` coluna.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* O primeiro documento da coleção define o esquema inicial do repositório analítico.
  * As propriedades no primeiro nível do documento serão representadas como colunas.
  * Documentos com mais propriedades do que o esquema inicial gerarão novas colunas no repositório analítico.
  * Não é possível remover colunas.
  * A exclusão de todos os documentos em uma coleção não redefine o esquema de armazenamento analítico.
  * Não há controle de versão do esquema. A última versão inferida do armazenamento transacional é o que você verá no repositório analítico.

* Atualmente, não damos suporte a nomes de colunas de leitura do Azure Synapse Spark que contenham espaços em branco (espaço em branco).

* Espere um comportamento diferente em relação aos `null` valores explícitos:
  * Os pools do Spark no Azure Synapse lerá esses valores como `0` (zero).
  * Os pools sem SQL Server no Azure Synapse lerá esses valores como `NULL` se o primeiro documento da coleção tiver, para a mesma propriedade, um valor com um `non-numeric` tipo de dados.
  * Os pools sem SQL Server no Azure Synapse lerá esses valores como `0` (zero) se o primeiro documento da coleção tiver, para a mesma propriedade, um valor com um `numeric` tipo de dados.

* Espere um comportamento diferente em relação às colunas ausentes:
  * Os pools do Spark no Azure Synapse irão representar essas colunas como `undefined` .
  * Os pools sem SQL Server no Azure Synapse irão representar essas colunas como `NULL` .

#### <a name="schema-representation"></a>Representação de esquema

Há dois modos de representação de esquema no repositório analítico. Esses modos têm compensações entre a simplicidade de uma representação de coluna, a manipulação dos esquemas polimórficos e a simplicidade da experiência de consulta:

* Representação de esquema bem definida
* Representação de esquema de fidelidade total

> [!NOTE]
> Para contas de API do SQL (núcleo), quando o repositório analítico está habilitado, a representação de esquema padrão no repositório analítico é bem definida. Enquanto para as contas Azure Cosmos DB API para MongoDB, a representação de esquema padrão no repositório analítico é uma representação de esquema de fidelidade total. Se você tiver cenários que exigem uma representação de esquema diferente da oferta padrão para cada uma dessas APIs, entre em contato com a [equipe de Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) para habilitá-la.

**Representação de esquema bem definida**

A representação de esquema bem definida cria uma representação de tabela simples dos dados independentes de esquema no armazenamento transacional. A representação de esquema bem definida tem as seguintes considerações:

* Uma propriedade sempre tem o mesmo tipo em vários itens.

  * Por exemplo, `{"a":123} {"a": "str"}` não tem um esquema bem definido porque, às vezes, `"a"` é uma cadeia de caracteres e, outras vezes, um número. Nesse caso, o armazenamento analítico registra o tipo de dados de `"a"` como o tipo de dados do `“a”` no primeiro item que ocorre no tempo de vida do contêiner. O documento ainda será incluído no repositório analítico, mas os itens em que o tipo de dados `"a"` diferem não serão.
  
    Essa condição não se aplica a propriedades nulas. Por exemplo, `{"a":123} {"a":null}` ainda está bem definido.

* Os tipos de matriz devem conter um único tipo repetido.

  * Por exemplo, `{"a": ["str",12]}` não é um esquema bem definido porque a matriz contém uma mistura de tipos inteiros e de cadeia de caracteres.

> [!NOTE]
> Se o repositório analítico de Azure Cosmos DB seguir a representação de esquema bem definida e a especificação acima for violada por determinados itens, esses itens não serão incluídos no repositório analítico.

* Espere um comportamento diferente em relação a tipos diferentes no esquema bem definido:
  * Os pools do Spark no Azure Synapse representarão esses valores como `undefined` .
  * Os pools sem SQL Server no Azure Synapse irão representar esses valores como `NULL` .


**Representação de esquema de fidelidade total**

A representação de esquema de fidelidade total foi projetada para lidar com toda a amplitude de esquemas polimórficos nos dados operacionais independentes de esquema. Nessa representação de esquema, nenhum item é removido do repositório analítico, mesmo que as restrições de esquema bem definidas (que não sejam campos de tipo de dados mistos nem matrizes de tipos de dados misturadas) sejam violadas.

Isso é obtido pela tradução das propriedades de folha dos dados operacionais para o repositório analítico com colunas distintas com base no tipo de dados dos valores na propriedade. Os nomes de propriedade de folha são estendidos com tipos de dados como um sufixo no esquema de armazenamento analítico, de forma que eles possam ser consultas sem ambigüidade.

Por exemplo, vamos pegar o seguinte documento de exemplo no repositório transacional:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

A propriedade folha `streetNo` dentro do objeto aninhado `address` será representada no esquema de repositório analítico como uma coluna `address.object.streetNo.int32` . O tipo de dados é adicionado como um sufixo à coluna. Dessa forma, se outro documento for adicionado ao armazenamento transacional onde o valor da propriedade folha `streetNo` for "123" (Observe que é uma cadeia de caracteres), o esquema do repositório analítico evoluirá automaticamente sem alterar o tipo de uma coluna gravada anteriormente. Uma nova coluna adicionada ao repositório analítico como `address.object.streetNo.string` onde esse valor de "123" é armazenado.

**Tipo de dados para mapa de sufixo**

Aqui está um mapa de todos os tipos de dados de propriedade e suas representações de sufixo no repositório analítico:

|Tipo de dados original  |Sufixo  |Exemplo  |
|---------|---------|---------|
| Double |  ". float64" |    24,99|
| Array | ". array" |    ["a", "b"]|
|Binário | ". Binary" |0|
|Boolean    | ". bool"   |Verdadeiro|
|Int32  | ". Int32"  |123|
|Int64  | ". Int64"  |255486129307|
|Nulo   | ". NULL"   | nulo|
|String|    ". String" | "ABC"|
|Timestamp |    ". Timestamp" |  Carimbo de data/hora (0, 0)|
|DateTime   |". Date"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". objectId"    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Documento   |". Object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Arquivamento econômico de dados históricos

Uma camada de dados se refere à separação de dados entre as infraestruturas de armazenamento otimizadas para cenários diferentes. Dessa forma, melhora o desempenho geral e a economia da pilha de dados de ponta a ponta. Com o repositório analítico, agora o Azure Cosmos DB tem suporte para camadas automáticas de dados do repositório transacional para o repositório analítico com diferentes layouts de dados. Com o repositório analítico otimizado em termos de custo de armazenamento em comparação ao repositório transacional, você pode reter horizontes muito maiores de dados operacionais para análise histórica.

Depois que o repositório analítico estiver habilitado, com base nas necessidades de retenção de dados das cargas de trabalho transacionais, você poderá configurar a propriedade “vida útil do repositório transacional (TTL transacional)” para que os registros sejam excluídos automaticamente do repositório transacional após um determinado período de tempo. De modo semelhante, a “vida útil do repositório analítico (TTL analítica)” permite que você gerencie o ciclo de vida dos dados retidos no repositório analítico independentemente do repositório transacional. Ao habilitar o repositório analítico e configurar as propriedades de TTL, você pode criar camadas e definir o período de retenção de dados para os dois repositórios perfeitamente.

### <a name="global-distribution"></a>Distribuição Global

Se você tiver uma conta do Azure Cosmos DB distribuída globalmente, depois de habilitar o repositório analítico para um contêiner, ele ficará disponível em todas as regiões dessa conta.  Quaisquer alterações nos dados operacionais são replicadas globalmente em todas as regiões. Você pode executar consultas analíticas efetivamente na cópia regional mais próxima dos seus dados no Azure Cosmos DB.

### <a name="security"></a>Segurança

A autenticação com o repositório analítico é igual a de um repositório transacional para um determinado banco de dados. Você pode usar chaves primárias ou somente leitura para autenticação. Você pode usar o serviço vinculado no Synapse Studio para evitar colar as chaves do Azure Cosmos DB nos notebooks do Spark. O acesso a esse serviço vinculado está disponível para todos que tenham acesso ao espaço de trabalho.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Suporte para vários runtimes do Azure Synapse Analytics

O repositório analítico é otimizado para fornecer escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de execução de computação. A tecnologia de armazenamento é autogerenciada para otimizar suas cargas de trabalho de análise se esforços manuais.

Ao desacoplar o sistema de repositório analítico do sistema de computação analítica, os dados no repositório analítico do Azure Cosmos DB podem ser consultados simultaneamente em diferentes runtimes de análise com suporte do Azure Synapse Analytics. A partir de hoje, o Azure Synapse Analytics dá suporte a Apache Spark e a pool de SQL sem servidor com Azure Cosmos DB repositório analítico.

> [!NOTE]
> Você só pode ler do repositório analítico usando o tempo de execução da análise de Synapse do Azure. Você pode gravar os dados de volta no seu repositório transacional como uma camada de serviço.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Preços

O repositório analítico segue um modelo de preço baseado em consumo no qual você é cobrado por:

* Armazenamento: o volume dos dados retidos no repositório analítico todos os meses, incluindo os dados históricos, conforme definido pela TTL analítica.

* Operações de gravação analítica: a sincronização totalmente gerenciada de atualizações de dados operacionais para o repositório analítico a partir do repositório transacional (sincronização automática).

* Operações de leitura analítica: as operações de leitura executadas no armazenamento analítico do pool Spark do Azure Synapse Analytics e tempos de execução do pool SQL sem servidor.

O preço do repositório analítico é separado do modelo de preços do repositório transacional. Não há nenhum conceito de RUs provisionadas no repositório analítico. Consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter detalhes completos sobre o modelo de preços do repositório analítico.

Para obter uma estimativa de custo de alto nível para habilitar o repositório analítico em um contêiner do Azure Cosmos DB, você pode usar o [planejador de capacidade do Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) e obter uma estimativa dos seus custos de armazenamento analítico e de operações de gravação. Os custos de operações de leitura analítica dependem das características da carga de trabalho analítica, mas como uma estimativa de alto nível, o exame de 1 TB de dados no repositório analítico geralmente resulta em 130.000 operações de leitura analítica e em um custo de US$ 0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> TTL (vida útil) analítica

A TTL analítica indica por quanto tempo os dados devem ser retidos em seu repositório analítico, para um contêiner. 

Se o repositório analítico estiver habilitado, as inserções, as atualizações, as exclusões nos dados operacionais serão sincronizadas automaticamente do repositório transacional para o repositório analítico, independentemente da configuração de TTL transacional. A retenção desses dados operacionais no repositório analítico pode ser controlada pelo valor da TTL Analítico no nível de contêiner, conforme especificado abaixo:

A TTL Analítico em um contêiner é definida usando a propriedade `AnalyticalStoreTimeToLiveInSeconds`:

* Se o valor for definido como "0", ausente (ou definido como nulo): o repositório analítico será desabilitado, e nenhum dado será replicado do repositório transacional para o repositório analítico

* Se presente e o valor for definido como "-1": o repositório analítico manterá todos os dados históricos, independentemente da retenção dos dados no repositório transacional. Essa configuração indica que o repositório analítico tem retenção infinita dos seus dados operacionais

* Se presente e o valor for definido como algum número positivo “n”: os itens expirarão do repositório analítico “n” segundos após a hora da última modificação no repositório transacional. Essa configuração poderá ser utilizada se você quiser manter seus dados operacionais por um período de tempo limitado no repositório analítico, independentemente da retenção dos dados no armazenamento transacional

Considere o seguinte:

*   Depois que o repositório analítico for habilitado com um determinado valor de TTL analítico, é possível atualizá-lo posteriormente com um valor válido diferente. 
*   Embora a TTL transacional possa ser definida no nível de contêiner ou de item, atualmente, a TTL analítica só pode ser definida no nível de contêiner.
*   Você pode obter uma retenção mais longa de seus dados operacionais no repositório analítico ao definir a TTL analítica > = TTL transacional no nível de contêiner.
*   O repositório analítico pode ser feito para espelhar o armazenamento transacional, definindo o TTL analítico = transacional TTL.

Quando você habilita o repositório analítico em um contêiner:

* No portal do Azure, a opção de TTL analítico é definida como o valor padrão de-1. Você pode alterar esse valor para ' n' segundos, navegando para configurações de contêiner em Data Explorer. 
 
* No SDK do Azure ou no PowerShell ou na CLI, a opção de TTL analítico pode ser habilitada definindo-a como-1 ou ' n'. 

Para saber mais, confira [Como configurar a TTL analítica em um contêiner](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Link do Azure Synapse para Azure Cosmos DB](synapse-link.md)

* [Introdução ao Link do Azure Synapse para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casos de uso do Link do Azure Synapse para Azure Cosmos DB](synapse-link-use-cases.md)
