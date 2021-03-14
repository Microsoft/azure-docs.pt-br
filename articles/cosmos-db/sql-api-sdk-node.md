---
title: 'O Azure Cosmos DB: SQL Node.js API, SDK e recursos'
description: Saiba tudo sobre o SDK e a API do SQL Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 8408cdada40bd3595f9bf7cf2c6a2de90040ec0b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217254"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Node.js do Azure Cosmos DB para API do SQL: Notas sobre a versão e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK do Feed de Alterações do .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

|Recurso  |Link  |
|---------|---------|
|Baixar o SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência de SDK do JavaScript](/javascript/api/%40azure/cosmos/)
|Instruções de instalação do SDK  |  [Instruções de instalação](https://github.com/Azure/azure-sdk-for-js)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exemplos | [Exemplos de código do Node.js](sql-api-nodejs-samples.md)
| Tutorial de introdução | [Introdução ao SDK do JavaScript](sql-api-nodejs-get-started.md)
| Tutorial do aplicativo Web | [Criar um aplicativo web Node.js usando o Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma atual com suporte | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK Versão 3.x.x<br/>[Node. js v10. x](https://nodejs.org/en/blog/release/v10.6.0/)-SDK versão 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/)-SDK versão 3. x. x<br/>[Node. js v6. x](https://nodejs.org/en/blog/release/v6.10.3/)-SDK versão 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK versão 1. x. x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK versão 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK versão 1.x.x

## <a name="release-notes"></a>Notas de versão

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Defina ResponseContinuationTokenLimitInKB padrão como 1 KB. Por padrão, estamos limitando esse valor a 1 KB para evitar cabeçalhos longos (o Node.js tem um limite de tamanho de cabeçalho global). Um usuário pode definir esse campo para permitir cabeçalhos mais longos, o que pode ajudar o back-end a otimizar a execução da consulta.
* Remova disableSSLVerification. Essa opção tem novas alternativas descritas em [nº 388](https://github.com/Azure/azure-cosmos-js/pull/388).

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Permitir que initialHeaders defina explicitamente o cabeçalho de chave de partição
* Usar package.json#files para impedir a publicação de arquivos incorretos
* Corrigir o erro de classificação do mapa de roteamento na versão mais antiga do node+v8
* Corrige o bug quando o usuário fornece opções de repetição parciais.

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Impedir que o webpack resolva os módulos chamados com require

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Corrige um bug pendente em que RUs sempre eram relatadas como 0 nas consultas de agregação.

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 Versão v3! 🎉 Muitos recursos novos, correções de bugs e algumas alterações de falhas. Metas principais desta versão:

* Implementar novos recursos importantes
  * Consultas DISTINCT
  * Consultas LIMIT/OFFSET
  * Solicitações canceláveis pelo usuário
* Atualizar para a versão mais recente da API REST do Cosmos, na qual todos os contêineres têm escala ilimitada
* Facilitar o uso do Cosmos no navegador
* Melhor alinhamento com as novas diretrizes de SDK de JS do Azure

#### <a name="migration-guide-for-breaking-changes"></a>Guia de migração para alterações de falhas
##### <a name="improved-client-constructor-options"></a>Opções aprimoradas de construtor de cliente

As opções do construtor foram simplificadas:

* masterKey foi renomeado como chave e movido para o nível superior.
* As propriedades anteriormente em options.auth foram movidas para o nível superior.

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>API simplificada do QueryIterator
Na v2, havia várias maneiras de iterar ou recuperar resultados de uma consulta. Tentamos simplificar a API da v3 e remover APIs semelhantes ou duplicadas:

* Remover iterator.next() e iterator.current(). Usar fetchNext() para obter páginas de resultados.
* Remover iterator.forEach(). Em vez disso, usar iteradores assíncronos.
* iterator.executeNext() renomeado para iterator.fetchNext().
* iterator.toArray() renomeado para iterator.fetchAll().
* As páginas agora são objetos de resposta adequados, em vez de objetos JS simples.
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Os contêineres fixos agora estão particionados.
O serviço do Cosmos agora é compatível com chaves de partição em todos os contêineres, incluindo aqueles que foram criados anteriormente como contêineres fixos. O SDK da v3 é atualizado para a versão mais recente da API que implementa essa alteração, mas está sem interrupção. Se você não fornecer uma chave de partição para operações, usaremos como padrão uma chave do sistema que funcione com todos os seus contêineres e documentos existentes.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert removido dos procedimentos armazenados
Anteriormente, upsert era permitido em coleções não particionadas. Mas, com a atualização de versão da API, todas as coleções são particionadas e nós o removemos por completo.

##### <a name="item-reads-will-not-throw-on-404"></a>As leituras de item não serão lançadas em 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Gravação padrão de várias regiões
Por padrão, o SDK agora gravará em várias regiões em que a configuração do Cosmos for compatível. Esse era um comportamento opcional anteriormente.

##### <a name="proper-error-objects"></a>Objetos de erro apropriados
As solicitações com falha agora geram erro ou subclasses de erro apropriados. Anteriormente, eles lançavam objetos JS simples.

#### <a name="new-features"></a>Novos recursos
##### <a name="user-cancelable-requests"></a>Solicitações canceláveis pelo usuário
A mudança para efetuar fetch internamente nos permite usar a API AbortController do navegador para compatibilidade com operações canceláveis pelo usuário. No caso de operações em que várias solicitações estão potencialmente em andamento (como consultas entre partições), todas as solicitações da operação serão canceladas. Os usuários de navegadores modernos já terão AbortController. Os usuários de node.js precisarão usar uma biblioteca polyfill.

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Definir a taxa de transferência como parte da operação de criação de BD/contêiner
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A geração de tokens de cabeçalho foi dividida em uma nova biblioteca, @azure/cosmos-sign. Qualquer pessoa que chamar a API REST do Cosmos diretamente poderá usar isso para assinar cabeçalhos utilizando o mesmo código que chamamos dentro de @azure/cosmos.

##### <a name="uuid-for-generated-ids"></a>UUID para IDs geradas
A v2 tinha código personalizado para gerar IDs de item. Mudamos para a biblioteca de comunidade uuid, bem conhecida e mantida.

##### <a name="connection-strings"></a>Cadeias de conexão
Agora é possível passar uma cadeia de conexão copiada do portal do Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Experiência de navegador aprimorada
Embora fosse possível usar o SDK da v2 no navegador, não era uma experiência ideal. Você precisava embutir várias node.js bibliotecas internas e usar um Agrupador como webpack ou remessa. O SDK da v3 torna a experiência imediata e muito melhor para os usuários do navegador.

* Substituir os elementos internos da solicitação por fetch (nº 245)
* Remover uso do buffer (nº 330)
* Remover o uso interno do nó em favor de pacotes/APIs universais (nº 328)
* Mudar para node-abort-controller (nº 294)

#### <a name="bug-fixes"></a>Correções de bug
* Corrigir leitura de oferta e trazer de volta testes de oferta (nº 224)
* Corrigir EnableEndpointDiscovery (nº 207)
* Corrigir RUs ausente nos resultados paginados (nº 360)
* Expandir tipo de parâmetro de consulta SQL (nº 346)
* Adicionar ttl a ItemDefinition (nº 341)
* Corrigir métricas de consulta de CP (nº 311)
* Adicionar activityId a FeedResponse (nº 293)
* Mudar _ts type de cadeia de caracteres para número (nº 252)(nº 295)
* Corrigir agregação de solicitação de encargo (nº 289)
* Permitir chaves de partição com cadeia de caracteres em branco (nº 277)
* Adicionar cadeia de caracteres ao tipo de consulta de conflito (nº 237)
* Adicionar uniqueKeyPolicy ao contêiner (nº 234)

#### <a name="engineering-systems"></a>Sistemas de engenharia
Nem sempre são as alterações mais visíveis, mas ajudam nossa equipe a fornecer um código melhor, mais rápido.

* Usar rollup nas compilações de produção (nº 104)
* Atualizar para Typescript 3.5 (nº 327)
* Converter em referências de projeto TS. Extrair pasta de teste (nº 270)
* Habilitar noUnusedLocals e noUnusedParameters (nº 275)
* YAML do Azure Pipelines para compilações de CI (nº 298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Sem alterações de código. Corrige um problema em que alguns arquivos extras foram incluídos no pacote 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Corrigir failover regional na política de repetição
* Corrigir a propriedade ChangeFeed hasMoreResults
* Atualizações de dependência de desenvolvimento
* Adicionar PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Mudar _ts type de cadeia de caracteres para número
* Corrigir testes de indexação padrão
* Backport uniqueKeyPolicy para v2
* Correções de demonstração e da depuração das demonstrações

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Correções de oferta de backport do branch da v3
* Corrigir erro na assinatura tipo executeNext()
* Correções de erro de digitação

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Reestruturação da compilação. Permite a extração da versão do SDK no momento da compilação.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Novos recursos
* Adicionado suporte a ChangeFeed (nº 196)
* Adicionado datatype MultiPolygon para indexação (nº 191)
* Adicionar a propriedade "key" ao construtor como alias para masterKey (nº 202)

#### <a name="fixes"></a>Correções
* Correção do bug em que next() estava retornando valor incorreto no iterador

#### <a name="engineering-improvements"></a>Aprimoramentos de engenharia
* Adicionar teste de integração para consumo de typescript (nº 199)
* Habilitar a instalação diretamente do GitHub (nº 194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Adiciona a interface para o tipo de agente do nó. Os usuários de typescript não precisam mais instalar @types/node como uma dependência
* Os locais preferenciais agora são considerados corretamente
* Melhorias para contribuir com a documentação do desenvolvedor
* Diversas correções de erro de digitação

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Correções de tipo de problema de definição, introduzido no 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Remover a dependência `big-integer`
* Alterne para diretivas de referência para o tipo AsyncIterable. Os usuários de typescript não precisam mais personalizar a configuração "lib".
* Correções de erro de digitação

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Corrigir links do leiame

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Corrigir a implementação de interface de repetição

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Disponibilidade geral da versão 2.0.0 do SDK do JavaScript
* Suporte adicionado para gravações de várias regiões.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 da Versão 2.0.0 do SDK do JavaScript para a visualização pública.
* Novo modelo de objeto, com CosmosClient de nível superior e os métodos são divididos em classes relevantes de banco de dados, contêiner e classes de item. 
* Suporte para [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertido para o TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* documentação do npm corrigida.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Adicionado suporte para repetições padrão em problemas de conexão.
* Adicionado suporte para o feed de alteração de coleção de leitura.
* Bug de consistência de sessão fixa que intermitentemente causou “sessão de leitura não disponível”
* Adicionado suporte para métricas de consulta.
* Modificado o número máximo de conexões do Agente de http.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Documentação atualizada para fazer referência ao Azure Cosmos DB em vez do Azure DocumentDB.
* Adicionado suporte para configuração de proxyUrl em ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Correção secundária para sistemas de arquivos que diferenciam maiúsculas de minúsculas.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Adiciona suporte à Consistência de Sessão.
* Esta versão do SDK requer a versão mais recente do [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Divide consultas entre partições aprovadas.
* Adiciona suporte para o link de recurso com barras à esquerda e à direita (e os testes correspondentes).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    documentação do npm corrigida.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Correção de um bug no executeStoredProcedure, em que documentos envolvidos tinham caracteres especiais Unicode (LS, PS).
* Correção de um bug no tratamento de documentos com caracteres Unicode na chave de partição.
* Suporte corrigido para criar coleções com a mídia de nome. Problema nº 114 do GitHub.
* Suporte fixo para o token de autorização de permissão. Problema nº 178 do GitHub.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Foi adicionado suporte a um novo [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Foi adicionado suporte para UriFactory.
* Correção de um bug de suporte ao Unicode. Problema nº 171 do Github.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Adição do suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adição da opção para controlar o grau de paralelismo em consultas de partição cruzada.
* Adição da opção para desabilitar a verificação de TSL quando executada no Emulador do Azure Cosmos DB.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Correção do bug de token de continuação para a coleta de partição única. Problema nº 107 do GitHub.
* Correção do bug executeStoredProcedure no tratamento de 0 como parâmetro único. Problema nº 155 do GitHub.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Cabeçalho de agente do usuário fixo para incluir a versão do SDK.
* Limpeza de código secundária.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Desabilitar verificação de TSL ao usar o SDK visando o emulator(hostname=localhost).
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção muito grande de taxa de solicitação, código de erro 429.) Por padrão, o Azure Cosmos DB tenta cada solicitação novamente nove vezes quando o código de erro 429 é encontrado, respeitando o tempo retryAfter no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
* O Cosmos DB agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a contagem de repetições restritas e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Suporte adicionado para contas de banco de dados de várias regiões.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Adicionado o suporte para o recurso TTL (tempo de vida) para documentos.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementação de [coleções particionadas](partitioning-overview.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* O bug RangePartitionResolver.resolveForRead foi corrigido, pois ele não estava retornando links devido a uma concatenação incorreta dos resultados.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Consertado hashPartitionResolver resolveForRead(): Quando nenhuma chave de partição fornecida estava gerando exceção, em vez de retornar uma lista de todos os links registrados.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) – Agente HTTPS Dedicado: evite modificar o agente global para fins do Azure Cosmos DB. Usar um agente dedicado para todas as solicitações da biblioteca.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Corrige o problema [nº 81](https://github.com/Azure/azure-documentdb-node/issues/81) — trate corretamente os traços em IDs de mídia.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Corrige o problema [nº 95](https://github.com/Azure/azure-documentdb-node/issues/95) — aviso de perda do ouvinte EventEmitter.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Corrige o problema [nº 92](https://github.com/Azure/azure-documentdb-node/issues/90) — renomeie a pasta Hash para hash nos sistemas que diferenciam maiúsculas de minúsculas.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implemente o suporte a fragmentação ao adicionar os resolvedores de partição de hash e de intervalo.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementar o Upsert. Novos métodos upsertXXX em documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Ignorado para alinhar os números de versão com outros SDKs.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Slipt Q promete wrapper para o novo repositório.
* Atualização para o arquivo de pacote do registro npm.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementa o roteamento com base em ID.
* Corrige o problema [nº 49](https://github.com/Azure/azure-documentdb-node/issues/49) - a propriedade atual está em conflito com o método atual().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Suporte adicionado para índice geoespaciais.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, &#47;&#47; nem terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementa a política de indexação V2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problema [nº 40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint e grunt implementadas no núcleo e SDK de promessa.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problema [nº 45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promessa de wrapper não inclui o cabeçalho com erro.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Habilidade implementada de consultar conflitos por meio da adição de readConflicts, readConflictAsync e queryConflicts.
* Documentação da API atualizada.
* Problema [nº 41](https://github.com/Azure/azure-documentdb-node/issues/41) - Erro client.createDocumentAsync.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* SDK DO GA.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação

A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte. Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 de julho de 2019 |--- |
| [3.0.4](#3.0.4) |22 de julho de 2019 |--- |
| [3.0.3](#3.0.3) |17 de julho de 2019 |--- |
| [3.0.2](#3.0.2) |9 de julho de 2019 |--- |
| [3.0.0](#3.0.0) |28 de junho de 2019 |--- |
| [2.1.5](#2.1.5) |20 de março de 2019 |--- |
| [2.1.4](#2.1.4) |15 de março de 2019 |--- |
| [2.1.3](#2.1.3) |8 de março de 2019 |--- |
| [2.1.2](#2.1.2) |28 de janeiro de 2019 |--- |
| [2.1.1](#2.1.1) |5 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |4 de dezembro de 2018 |--- |
| [2.0.5](#2.0.5) |7 de novembro de 2018 |--- |
| [2.0.4](#2.0.4) |30 de outubro de 2018 |--- |
| [2.0.3](#2.0.3) |30 de outubro de 2018 |--- |
| [2.0.2](#2.0.2) |10 de outubro de 2018 |--- |
| [2.0.1](#2.0.1) |25 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |24 de setembro de 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de agosto de 2018 |--- |
| [1.14.4](#1.14.4) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.3](#1.14.3) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.2](#1.14.2) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [1.14.1](#1.14.1) |10 de novembro, 2017 |30 de agosto de 2020 |
| [1.14.0](#1.14.0) |9 de novembro de 2017 |30 de agosto de 2020 |
| [1.13.0](#1.13.0) |11 de outubro de 2017 |30 de agosto de 2020 |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.0](#1.12.0) |10 de maio de 2017 |30 de agosto de 2020 |
| [1.11.0](#1.11.0) |16 de março de 2017 |30 de agosto de 2020 |
| [1.10.2](#1.10.2) |27 de janeiro de 2017 |30 de agosto de 2020 |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |30 de agosto de 2020 |
| [1.10.0](#1.10.0) |03 de outubro de 2016 |30 de agosto de 2020 |
| [1.9.0](#1.9.0) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#1.7.0) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.6](#1.5.6) |08 de março de 2016 |30 de agosto de 2020 |
| [1.5.5](#1.5.5) |02 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.4](#1.5.4) |1 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |26 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |22 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.1](#1.5.1) |4 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.0](#1.5.0) |31 de dezembro de 2015 |30 de agosto de 2020 |
| [1.4.0](#1.4.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.3.0](#1.3.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.2](#1.2.2) |10 de setembro de 2015 |30 de agosto de 2020 |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |30 de agosto de 2020 |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#1.1.0) |9 de julho de 2015 |30 de agosto de 2020 |
| [1.0.3](#1.0.3) |04 de junho de 2015 |30 de agosto de 2020 |
| [1.0.2](#1.0.2) |23 de maio de 2015 |30 de agosto de 2020 |
| [1.0.1](#1.0.1) |15 de maio de 2015 |30 de agosto de 2020 |
| [1.0.0](#1.0.0) |8 de abril de 2015 |30 de agosto de 2020 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).