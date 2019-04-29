---
title: 'O Azure Cosmos DB: SQL Node.js API, SDK e recursos'
description: Saiba tudo sobre o SDK e a API do SQL Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626733"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Node.js do Azure Cosmos DB para API do SQL: Notas sobre a versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Link  |
|---------|---------|
|Baixar o SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência de SDK do JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruções de instalação do SDK  |  [Instruções de instalação](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exemplos | [Exemplos de código do Node.js](sql-api-nodejs-samples.md)
| Tutorial de introdução | [Introdução ao SDK do JavaScript](sql-api-nodejs-get-started.md)
| Tutorial do aplicativo Web | [Criar um aplicativo web Node.js usando o Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma atual com suporte | [Node. js v6](https://nodejs.org/en/blog/release/v6.10.3/) – obrigatório para a versão SDK 2.0.0 e superior.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Notas de versão

### <a name="2.0.5"/>2.0.5</a>
* Adiciona a interface para o tipo de agente do nó. Os usuários de typescript não precisam mais instalar @types/node como uma dependência
* Os locais preferenciais agora são considerados corretamente
* Melhorias para contribuir com a documentação do desenvolvedor
* Diversas correções de erro de digitação

### <a name="2.0.4"/>2.0.4</a>
* Correções de tipo de problema de definição, introduzido no 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Remover a dependência `big-integer`
* Alterne para diretivas de referência para o tipo AsyncIterable. Os usuários de typescript não precisam mais personalizar a configuração "lib".
* Correções de erro de digitação

### <a name="2.0.2"/>2.0.2</a>
* Corrigir links do leiame

### <a name="2.0.1"/>2.0.1</a>
* Corrigir a implementação de interface de repetição

### <a name="2.0.0"/>2.0.0</a>
* Disponibilidade geral da versão 2.0.0 do SDK do JavaScript
* Suporte adicionado para gravações de várias regiões.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 da Versão 2.0.0 do SDK do JavaScript para a visualização pública.
* Novo modelo de objeto, com CosmosClient de nível superior e os métodos são divididos em classes relevantes de banco de dados, contêiner e classes de item. 
* Suporte para [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertido para o TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* documentação do npm corrigida.

### <a name="1.14.3"/>1.14.3</a>
* Adicionado suporte para repetições padrão em problemas de conexão.
* Adicionado suporte para o feed de alteração de coleção de leitura.
* Bug de consistência de sessão fixa que intermitentemente causou “sessão de leitura não disponível”
* Adicionado suporte para métricas de consulta.
* Modificado o número máximo de conexões do Agente de http.

### <a name="1.14.2"/>1.14.2</a>
* Documentação atualizada para fazer referência ao Azure Cosmos DB em vez do Azure DocumentDB.
* Adicionado suporte para configuração de proxyUrl em ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Correção secundária para sistemas de arquivos que diferenciam maiúsculas de minúsculas.

### <a name="1.14.0"/>1.14.0</a>
* Adiciona suporte à Consistência de Sessão.
* Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Divide consultas entre partições aprovadas.
* Adiciona suporte para o link de recurso com barras à esquerda e à direita (e os testes correspondentes).

### <a name="1.12.2"/>1.12.2</a>
*   documentação do npm corrigida.

### <a name="1.12.1"/>1.12.1</a>
* Correção de um bug no executeStoredProcedure, em que documentos envolvidos tinham caracteres especiais Unicode (LS, PS).
* Correção de um bug no tratamento de documentos com caracteres Unicode na chave de partição.
* Suporte corrigido para criar coleções com a mídia de nome. Problema nº 114 do GitHub.
* Suporte fixo para o token de autorização de permissão. Problema nº 178 do GitHub.

### <a name="1.12.0"/>1.12.0</a>
* Foi adicionado suporte a um novo [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Foi adicionado suporte para UriFactory.
* Correção de um bug de suporte ao Unicode. Problema nº 171 do Github.

### <a name="1.11.0"/>1.11.0</a>
* Adição do suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adição da opção para controlar o grau de paralelismo em consultas de partição cruzada.
* Adição da opção para desabilitar a verificação do SSL quando executada no Emulador do Azure Cosmos DB.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Correção do bug de token de continuação para a coleta de partição única. Problema nº 107 do GitHub.
* Correção do bug executeStoredProcedure no tratamento de 0 como parâmetro único. Problema nº 155 do GitHub.

### <a name="1.10.2"/>1.10.2</a>
* Cabeçalho de agente do usuário fixo para incluir a versão do SDK.
* Limpeza de código secundária.

### <a name="1.10.1"/>1.10.1</a>
* Desabilitar verificação de SSL ao usar o SDK visando o emulator(hostname=localhost).
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.

### <a name="1.10.0"/>1.10.0</a>
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.

### <a name="1.9.0"/>1.9.0</a>
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção muito grande de taxa de solicitação, código de erro 429.) Por padrão, o Azure Cosmos DB tenta cada solicitação novamente nove vezes quando o código de erro 429 é encontrado, respeitando o tempo retryAfter no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
* O Cosmos DB agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a contagem de repetições restritas e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="1.8.0"/>1.8.0</a>
* Suporte adicionado para contas de banco de dados de várias regiões.

### <a name="1.7.0"/>1.7.0</a>
* Adicionado o suporte para o recurso TTL (tempo de vida) para documentos.

### <a name="1.6.0"/>1.6.0</a>
* Implementação de [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* O bug RangePartitionResolver.resolveForRead foi corrigido, pois ele não estava retornando links devido a uma concatenação incorreta dos resultados.

### <a name="1.5.5"/>1.5.5</a>
* Consertado hashPartitionResolver resolveForRead(): Quando nenhuma chave de partição fornecida estava gerando exceção, em vez de retornar uma lista de todos os links registrados.

### <a name="1.5.4"/>1.5.4</a>
* Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) – Agente HTTPS Dedicado: evite modificar o agente global para fins do Azure Cosmos DB. Use um agente dedicado para todas as solicitações da biblioteca.

### <a name="1.5.3"/>1.5.3</a>
* Corrige o problema [nº 81](https://github.com/Azure/azure-documentdb-node/issues/81) — trate corretamente os traços em IDs de mídia.

### <a name="1.5.2"/>1.5.2</a>
* Corrige o problema [nº 95](https://github.com/Azure/azure-documentdb-node/issues/95) — aviso de perda do ouvinte EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Corrige o problema [nº 92](https://github.com/Azure/azure-documentdb-node/issues/90) — renomeie a pasta Hash para hash nos sistemas que diferenciam maiúsculas de minúsculas.

### <a name="1.5.0"/>1.5.0</a>
* Implemente o suporte a fragmentação ao adicionar os resolvedores de partição de hash e de intervalo.

### <a name="1.4.0"/>1.4.0</a>
* Implementar o Upsert. Novos métodos upsertXXX em documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Ignorado para alinhar os números de versão com outros SDKs.

### <a name="1.2.2"/>1.2.2</a>
* Slipt Q promete wrapper para o novo repositório.
* Atualização para o arquivo de pacote do registro npm.

### <a name="1.2.1"/>1.2.1</a>
* Implementa o roteamento com base em ID.
* Corrige o problema [nº 49](https://github.com/Azure/azure-documentdb-node/issues/49) - a propriedade atual está em conflito com o método atual().

### <a name="1.2.0"/>1.2.0</a>
* Suporte adicionado para índice geoespaciais.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, &#47;&#47; ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementa a política de indexação V2.

### <a name="1.0.3"/>1.0.3</a>
* Problema [nº 40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint e grunt implementadas no núcleo e SDK de promessa.

### <a name="1.0.2"/>1.0.2</a>
* Problema [nº 45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promessa de wrapper não inclui o cabeçalho com erro.

### <a name="1.0.1"/>1.0.1</a>
* Habilidade implementada de consultar conflitos por meio da adição de readConflicts, readConflictAsync e queryConflicts.
* Documentação da API atualizada.
* Problema [nº 41](https://github.com/Azure/azure-documentdb-node/issues/41) - Erro client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* SDK DO GA.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, é recomendável atualizar sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado é rejeitada pelo serviço.

<br/>

| Version | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de agosto de 2018 |--- |
| [1.14.4](#1.14.4) |03 de maio de 2018 |--- |
| [1.14.3](#1.14.3) |03 de maio de 2018 |--- |
| [1.14.2](#1.14.2) |21 de dezembro de 2017 |--- |
| [1.14.1](#1.14.1) |10 de novembro, 2017 |--- |
| [1.14.0](#1.14.0) |9 de novembro de 2017 |--- |
| [1.13.0](#1.13.0) |11 de outubro de 2017 |--- |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |--- |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |--- |
| [1.12.0](#1.12.0) |10 de maio de 2017 |--- |
| [1.11.0](#1.11.0) |16 de março de 2017 |--- |
| [1.10.2](#1.10.2) |27 de janeiro de 2017 |--- |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |03 de outubro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de março de 2016 |--- |
| [1.5.6](#1.5.6) |08 de março de 2016 |--- |
| [1.5.5](#1.5.5) |02 de fevereiro de 2016 |--- |
| [1.5.4](#1.5.4) |1 de fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |26 de janeiro de 2016 |--- |
| [1.5.2](#1.5.2) |22 de janeiro de 2016 |--- |
| [1.5.1](#1.5.1) |4 de janeiro de 2016 |--- |
| [1.5.0](#1.5.0) |31 de dezembro de 2015 |--- |
| [1.4.0](#1.4.0) |06 de outubro de 2015 |--- |
| [1.3.0](#1.3.0) |06 de outubro de 2015 |--- |
| [1.2.2](#1.2.2) |10 de setembro de 2015 |--- |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |9 de julho de 2015 |--- |
| [1.0.3](#1.0.3) |04 de junho de 2015 |--- |
| [1.0.2](#1.0.2) |23 de maio de 2015 |--- |
| [1.0.1](#1.0.1) |15 de maio de 2015 |--- |
| [1.0.0](#1.0.0) |8 de abril de 2015 |--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

