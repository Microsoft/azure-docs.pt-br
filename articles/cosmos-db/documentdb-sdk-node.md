---
title: API de Node.js do Azure Cosmos DB, SDK & recursos | Microsoft Docs
description: "Saiba tudo sobre o SDK e a API do Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Azure Cosmos DB."
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/14/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4376a5c07b5f00311ce0fe3c0056efdf79c273f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-nodejs-sdk-release-notes-and-resources"></a>SDK de Node.js do Azure Cosmos DB: Notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed de alterações do .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Baixar o SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência da API do Node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instruções de instalação do SDK**</td><td>[Instruções de instalação](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Exemplos**</td><td>[Exemplos de código do Node.js](documentdb-nodejs-samples.md)</td></tr>

<tr><td>**Tutorial de introdução**</td><td>[Introdução ao SDK do Node.js](documentdb-nodejs-get-started.md)</td></tr>

<tr><td>**Tutorial do aplicativo Web**</td><td>[Criar um aplicativo web Node.js usando o Azure Cosmos DB](documentdb-nodejs-application.md)</td></tr>

<tr><td>**Plataforma atual com suporte**</td><td> 
[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="1.12.2"/>1.12.2</a>
*   documentação do npm corrigida.

### <a name="1.12.1"/>1.12.1</a>
* Correção de um bug no executeStoredProcedure, em que documentos envolvidos tinham caracteres especiais Unicode (LS, PS).
* Correção de um bug no tratamento de documentos com caracteres Unicode na chave de partição.
* Suporte corrigido para criar coleções com a mídia de nome. Problema nº 114 do Github.
* Suporte fixo para o token de autorização de permissão. Problema nº 178 do Github.

### <a name="1.12.0"/>1.12.0</a>
* Foi adicionado suporte a um novo [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Foi adicionado suporte para UriFactory.
* Correção de um bug de suporte ao Unicode. Problema nº 171 do Github.

### <a name="1.11.0"/>1.11.0</a>
* Adição do suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adição da opção para controlar o grau de paralelismo em consultas de partição cruzada.
* Adição da opção para desabilitar a verificação do SSL quando executada no Emulador do Azure Cosmos DB.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Correção do bug de token de continuação para a coleta de partição única. Problema nº 107 do Github.
* Correção do bug executeStoredProcedure no tratamento de 0 como parâmetro único. Problema nº 155 do Github.

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
* hashPartitionResolver resolveForRead() corrigido: quando nenhuma chave de partição fornecida estava emitindo exceção, em vez de retornar uma lista de todos os links registrados.

### <a name="1.5.4"/>1.5.4</a>
* Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) — Agente HTTPS Dedicado: evite modificar o agente global para os fins do Azure Cosmos DB. Use um agente dedicado para todas as solicitações da biblioteca.

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

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |--- |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |--- |
| [1.12.0](#1.12.0) |10 de maio de 2017 |--- |
| [1.11.0](#1.11.0) |16 de março de 2017 |--- |
| [1.10.2](#1.10.2) |27 de janeiro de 2017 |--- |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |3 de outubro de 2016 |--- |
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

