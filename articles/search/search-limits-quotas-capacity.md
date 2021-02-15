---
title: Limites de serviço para camadas e SKUs
titleSuffix: Azure Cognitive Search
description: Limites do serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para o Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509360"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limites de serviço no Azure Cognitive Search

Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices e outros objetos dependem de você [provisionar o Azure Cognitive Search](search-create-service-portal.md) nos tipos de preço **Gratuito**, **Básico**, **Standard** ou **Otimizado para Armazenamento**.

+ **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. 

+ **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em menor escala, mas compartilha alguma infraestrutura de rede com outros locatários.

+ **Standard** é executado em computadores dedicados, com mais capacidade de armazenamento e processamento em cada nível. Standard é fornecido em quatro níveis: S1, S2, S3 e S3 HD. O S3 HD (S3 de Alta Densidade) foi projetado para [multilocatário](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequenos (três mil índices por serviço). O S3 HD não fornece o [recurso de indexador](search-indexer-overview.md) e a ingestão de dados deve alavancar APIs que enviam dados da origem para o índice. 

+ **Otimizado para Armazenamento** é executado em computadores dedicados com mais armazenamento total, largura de banda de armazenamento e memória do o **Standard**. Essa camada tem como alvo índices grandes e de alteração lenta. Otimizado para Armazenamento vem em dois níveis: L1 e L2.

## <a name="subscription-limits"></a>Limites de assinatura
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Grátis | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simples por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Máximo de campos de coleção complexos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Máximo de elementos em todas as coletas complexas por documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima de campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| Número máximo de [encarregados da sugestão](/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Número máximo de [perfis de pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Serviços básicos criados antes de dezembro de 2017 têm menos limites (5 em vez de 15) em índices. A camada tipo Básico é a única SKU com um limite inferior de 100 campos por índice.

<sup>2</sup> há um limite superior para os elementos porque ter um grande número deles aumenta significativamente o armazenamento necessário para o índice. Um elemento de uma coleção complexa é definido como um membro dessa coleção. Por exemplo, suponha um [documento de Hotel com uma coleção complexa de salas](search-howto-complex-data-types.md#indexing-complex-types), cada sala na coleção de salas é considerada um elemento. Durante a indexação, o mecanismo de indexação pode processar com segurança um máximo de 3000 elementos no documento como um todo. [Esse limite](search-api-migration.md#upgrade-to-2019-05-06) foi introduzido em `api-version=2019-05-06` e se aplica apenas a coleções complexas e não a coleções de cadeias de caracteres ou a campos complexos.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites do documento 

A partir de outubro de 2018, não haverá mais limites de contagem de documentos para novos serviços criados em qualquer camada faturável (Básica, S1, S2, S3, S3 HD), em qualquer região. Os serviços mais antigos criados antes de outubro de 2018 ainda podem estar sujeitos a limites de contagem de documentos.

Para determinar se o serviço tem limites de documentos, use [OBTER API REST de Estatísticas de Serviço](/rest/api/searchservice/get-service-statistics). Os limites de documento são refletidos na resposta com `null` indicando que não há limites.

> [!NOTE]
> Embora não haja limites de documentos impostos pelo serviço, há um limite de fragmentos de aproximadamente 24 bilhões de documentos por índice nos serviços de pesquisa Básico, S1, S2 e S3. Para o S3 HD, o limite de fragmentos é de 2 bilhões de documentos por índice. Cada elemento de uma coleção complexa conta como um documento separado em termos de limites de fragmentos.

### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada à API

O tamanho máximo do documento ao chamar uma API de Índice é de aproximadamente 16 megabytes.

O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como é possível transmitir um lote de vários documentos para a API de Índice de uma só vez, o limite de tamanho depende praticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos é de 16 MB de JSON.

Lembre-se de considerar apenas os campos que podem ser consumidos por um serviço de pesquisa ao estimar o tamanho do documento. Todos os dados binários ou de imagem em documentos de origem devem ser omitidos dos seus cálculos.  

## <a name="indexer-limits"></a>Limites de indexador

Os tempos máximos de execução existem para fornecer balanceamento e estabilidade ao serviço como um todo, mas conjuntos de dados maiores podem precisar de mais tempo de indexação do que o máximo permitido. Se um trabalho de indexação não puder ser concluído no tempo máximo permitido, tente executá-lo de forma agendada. O agendador mantém monitora o status da indexação. Se um trabalho de indexação agendado for interrompido por algum motivo, o indexador poderá continuar de onde parou na próxima execução agendada.


| Recurso | Gratuito&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |10 |10 |
| Máximo de fontes de dados |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Número máximo de conjuntos de habilidades <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |N/D |Sem limite |Sem limite |
| Agendamento mínimo | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo de execução máximo| 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tempo máximo de execução para indexadores com o Skills <sup>5</sup> | 3 a 10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob |32.000 |64.000 |4&nbsp;milhões |8&nbsp;milhões |16&nbsp;milhões |N/D |4&nbsp;milhões |4&nbsp;milhões |

<sup>1</sup> Os serviços gratuitos têm um tempo máximo de execução do indexador de 3 minutos para fontes do blob e 1 minuto para todas as outras fontes de dados. Para a indexação de IA que chama os Serviços Cognitivos, os serviços gratuitos são limitados a 20 transações gratuitas por dia, em que uma transação é definida como um documento que passa com sucesso pelo pipeline de enriquecimento.

<sup>2</sup> Serviços básicos criados antes de dezembro de 2017 têm menos limites (5 em vez de 15) em indexadores, fontes de dados e conjuntos de habilidades.

<sup>3</sup> Os serviços do S3 HD não incluem suporte ao indexador.

<sup>4</sup> Máximo de 30 habilidades por conjunto de habilidades.

<sup>5</sup> O enriquecimento da IA e a análise de imagem fazem uso intensivo dos recursos de computação e consomem uma quantidade desproporcional da capacidade de processamento disponível. O tempo de execução dessas cargas de trabalho foi reduzido para dar a outros trabalhos na fila mais oportunidades de execução.

> [!NOTE]
> Conforme indicado nos [Limites de índice](#index-limits), os indexadores também impõem o limite superior de 3.000 elementos em todas as coleções complexas por documento, começando com a versão mais recente da API GA que dá suporte a tipos complexos (`2019-05-06`) em diante. Isso significa que, se criou o indexador com uma versão de API anterior, você não estará sujeito a esse limite. Para preservar a compatibilidade máxima, um indexador criado com uma versão de API anterior e, em seguida, atualizado com uma versão de API `2019-05-06` ou posterior, será **excluído** dos limites mesmo assim. Os clientes devem estar cientes do impacto adverso de ter coleções complexas muito grandes (como declarado anteriormente) e é altamente recomendável criar novos indexadores com a versão mais recente da API GA.

## <a name="shared-private-link-resource-limits"></a>Limites de recurso de link privado compartilhado

Os indexadores podem acessar outros recursos do Azure por meio de [pontos de extremidade privados](search-indexer-howto-access-private.md) gerenciados por meio da [API de recurso de link privado compartilhado](/rest/api/searchmanagement/sharedprivatelinkresources). Esta seção descreve os limites associados a esse recurso.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Suporte do indexador de ponto de extremidade privado | Não | Sim | Sim | Sim | Sim | Não | Sim | Sim |
| Suporte de ponto de extremidade privado para indexadores com o Skills<sup>1</sup> | Não | Não | Não | Sim | Sim | Não | Sim | Sim |
| Pontos de extremidade privados máximos | N/D | 10 ou 30 | 100 | 400 | 400 | N/D | 20 | 20 |
| Tipos de recursos distintos máximos<sup>2</sup> | N/D | 4 | 7 | 15 | 15 | N/D | 4 | 4 |

<sup>1</sup> a enriquecimento de ia e a análise de imagem são computacionalmente intensivas e consomem quantidades desproporcionais de capacidade de processamento disponível. Por esse motivo, as conexões privadas são desabilitadas em camadas inferiores para evitar um impacto adverso no desempenho e na estabilidade do próprio serviço de pesquisa.

<sup>2</sup> o número de tipos de recursos distintos é calculado como o número de `groupId` valores exclusivos usados em todos os recursos de link privado compartilhado para um determinado serviço de pesquisa, independentemente do status do recurso.

## <a name="synonym-limits"></a>Limites de sinônimos

O número máximo de mapas de sinônimos varia por camada. Cada regra pode ter até 20 expansões, onde uma expansão é um termo equivalente. Por exemplo, dado “gato”, associação com “gatinho”, “felino” e “felis” (o gênero para gatos) contaria como 3 expansões.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mapas máximos de sinônimos |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5.000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Limites de dados (enriquecimento de IA)

Um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) que faz chamadas a um recurso de Análise de Texto para [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), [análise de sentimento](cognitive-search-skill-sentiment.md), [detecção de idioma](cognitive-search-skill-language-detection.md) e [detecção de informações pessoais](cognitive-search-skill-pii-detection.md) está sujeito a limites de dados. O tamanho máximo de um registro deve ser 50.000 caracteres conforme medido por [`String.Length`](/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-lo ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limitações

As solicitações de API são limitadas conforme o sistema se aproxima da capacidade de pico. A limitação se comporta de maneira diferente para diferentes APIs. As APIs de consulta (pesquisa/sugestão/preenchimento automático) e as APIs de indexação são limitadas dinamicamente com base na carga no serviço. As APIs de índice e a API de operações de serviço têm limites de taxa de solicitação estáticos. 

Limites de solicitação de taxa estática para operações relacionadas a um índice:

+ Listar índices (GET/indexes): 3 por segundo por unidade de pesquisa
+ Obter índice (GET/índices/myindex): 10 por segundo por unidade de pesquisa
+ Criar índice (POST/índices): 12 por minuto por unidade de pesquisa
+ Criar ou atualizar índice (PUT/índices/myindex): 6 por segundo por unidade de pesquisa
+ Excluir índice (DELETE/índices/myindex): 12 por minuto por unidade de pesquisa 

Limites de solicitação de taxa estática para operações relacionadas a um serviço:

+ Estatísticas de serviço (GET/servicestats): 4 por segundo por unidade de pesquisa

## <a name="api-request-limits"></a>Limites de solicitação de API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> No Azure Cognitive Search, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](/rest/api/searchservice/supported-data-types) para obter mais informações sobre restrições e composição de campos).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

* Máximo de duas chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
