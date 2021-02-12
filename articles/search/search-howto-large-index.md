---
title: Indexar um conjunto de dados grandes usando indexadores internos
titleSuffix: Azure Cognitive Search
description: Estratégias para indexação de dados grandes ou indexação computacionalmente intensiva por meio do modo de lote, de origem e de técnicas para indexação agendada, paralela e distribuída.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: b4f54aff78526ba52e56ed9f4cf1feddf40fa69b
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358385"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Como indexar grandes conjuntos de dados no Azure Pesquisa Cognitiva

O Azure Cognitive Search é compatível com [duas abordagens básicas](search-what-is-data-import.md) para importar dados para um índice de pesquisa: *enviar por push* os dados para o índice de maneira programática ou apontar um [indexador do Azure Cognitive Search](search-indexer-overview.md) em uma fonte de dados compatível para efetuar o *pull* nos dados.

À medida que os volumes de dados crescem ou as necessidades de processamento mudam, você pode descobrir que as estratégias de indexação simples ou padrão não são mais práticas. Para o Azure Pesquisa Cognitiva, há várias abordagens para acomodar conjuntos de dados maiores, variando de como você estrutura uma solicitação de carregamento de dados, para usar um indexador específico de origem para cargas de trabalho agendadas e distribuídas.

As mesmas técnicas também se aplicam a processos de execução longa. Em particular, as etapas descritas em [indexação paralela](#parallel-indexing) são úteis para indexação computacionalmente intensiva, como análise de imagem ou processamento de linguagem natural em um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md).

As seções a seguir exploram técnicas para indexar grandes quantidades de dados usando a API de push e os indexadores.

## <a name="use-the-push-api"></a>Usar a API de envio por push

Ao enviar dados por push a um índice usando a [API REST de adicionar documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou o [método IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments), há várias considerações importantes que afetam a velocidade de indexação. Esses fatores são descritos na seção abaixo e vão desde a configuração da capacidade de serviço até as otimizações de código.

Para obter mais informações e exemplos de código que ilustram a indexação do modelo de push, consulte [tutorial: otimizar velocidades de indexação](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>Capacidade do seu serviço

Como uma primeira etapa, examine as características e os [limites](search-limits-quotas-capacity.md) da camada em que você provisionou o serviço. Um dos principais fatores diferenciais entre os tipos de preço é o tamanho e a velocidade das partições, que tem um impacto direto na velocidade de indexação. Se você provisionou seu serviço de pesquisa em uma camada insuficiente para a carga de trabalho, a atualização para uma nova camada pode ser a solução mais fácil e eficaz para aumentar a taxa de transferência de indexação.

Quando estiver satisfeito com a camada, a próxima etapa poderá ser aumentar o número de partições. A alocação de partição pode ser reajustada verticalmente após a execução de uma indexação inicial para reduzir o custo geral da execução do serviço.

> [!NOTE]
> Adicionar réplicas adicionais também pode aumentar as velocidades de indexação, mas não é garantido. Por outro lado, as réplicas adicionais aumentarão o volume de consulta que o serviço de pesquisa pode manipular. As réplicas também são um componente fundamental para obter um [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
>
> Antes de adicionar partições/réplicas ou atualizar para uma camada superior, considere o tempo de alocação e o custo monetário. A adição de partições pode aumentar significativamente a velocidade de indexação, mas adicioná-las/removê-las pode levar de 15 minutos a várias horas. Para obter mais informações, consulte a documentação sobre [ajuste da capacidade](search-capacity-planning.md).
>

### <a name="review-index-schema"></a>Examinar esquema de índice

O esquema do índice desempenha um papel importante na indexação de dados. Quanto mais campos você tiver, e quanto mais propriedades você definir (como *pesquisável* , *facetable* ou *filtráveis* ), todos contribuem para o maior tempo de indexação. Em geral, você só deve criar e especificar os campos que realmente precisa em um índice de pesquisa.

> [!NOTE]
> Para manter o tamanho do documento inativo, evite adicionar dados não consultáveis a um índice. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

### <a name="check-the-batch-size"></a>Verificar o tamanho do lote

Um dos mecanismos mais simples para indexação de um conjunto de dados maior é enviar vários documentos ou registros em uma única solicitação. Como a carga inteira é menor que 16 MB, uma solicitação pode manipular até 1000 documentos em uma operação de upload em massa. Esses limites se aplicam se você estiver usando a [API REST de adicionar documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou o [método IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) no SDK do .net. Para qualquer uma das APIs, você deve empacotar 1000 documentos no corpo de cada solicitação.

O uso de lotes para indexar documentos melhorará significativamente o desempenho da indexação. Determinar o tamanho de lote ideal para seus dados é um componente-chave de otimização de velocidades de indexação. Os dois fatores principais que influenciam o tamanho de lote ideal são:

+ O esquema do índice
+ O tamanho dos seus dados

Como o tamanho de lote ideal depende do índice e dos dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar o que resulta nas velocidades de indexação mais rápidas para seu cenário. Este [tutorial](tutorial-optimize-indexing-push-api.md) fornece um código de exemplo para o teste de tamanhos de lote usando o SDK do .net. 

### <a name="number-of-threadsworkers"></a>Número de threads/trabalhadores

Para aproveitar ao máximo as velocidades de indexação do Azure Cognitive Search, você provavelmente precisará usar vários threads para enviar solicitações de indexação do lote simultaneamente para o serviço.  

O número ideal de threads é determinado por:

+ A camada do serviço de pesquisa
+ O número de partições
+ O tamanho de seus lotes
+ O esquema do índice

Você pode modificar este exemplo e testar com contagens de threads diferentes para determinar a contagem de threads ideal para seu cenário. No entanto, desde que haja vários threads em execução simultânea, você poderá aproveitar a maioria dos ganhos de eficiência. 

> [!NOTE]
> À medida que você aumenta a camada do serviço de pesquisa ou aumenta as partições, também deve aumentar o número de threads simultâneos.

Conforme você aumenta as solicitações que chegam ao serviço de pesquisa, pode encontrar [códigos de status HTTP](/rest/api/searchservice/http-status-codes) indicando que a solicitação não foi totalmente concluída com sucesso. Durante a indexação, dois códigos de status HTTP comuns são:

+ **503 Serviço Não Disponível** – Esse erro significa que o sistema está sob carga pesada e sua solicitação não pode ser processada no momento.
+ **207 Status Múltiplo** – esse erro significa que alguns documentos foram bem-sucedidos, mas pelo menos um deles falhou.

### <a name="retry-strategy"></a>Estratégia de repetição

Se ocorrer uma falha, as solicitações deverão ser repetidas usando uma [estratégia de repetição de retirada exponencial](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

O SDK do .NET do Azure Cognitive Search tenta automaticamente 503s e outras solicitações com falha, mas você precisará implementar sua lógica para tentar novamente o 207s. Ferramentas de software livre, como [Polly](https://github.com/App-vNext/Polly), também podem ser usadas para implementar uma estratégia de repetição.

### <a name="network-data-transfer-speeds"></a>Velocidades de transferência de dados de rede

As velocidades de transferência de dados de rede podem ser um fator limitante ao indexar dados. A indexação de dados de dentro de seu ambiente do Azure é uma maneira fácil de acelerar a indexação.

## <a name="use-indexers-pull-api"></a>Usar indexadores (API pull)

[Indexadores](search-indexer-overview.md) são usados para rastrear fontes de dados do Azure com suporte para conteúdo pesquisável. Enquanto não são especificamente destinados a indexação em larga escala, vários recursos do indexador são particularmente úteis para acomodar grandes conjuntos de dados:

+ Agendadores permitem dividir a indexação em intervalos regulares para que se possa distribuí-la ao longo do tempo.
+ A indexação agendada pode retomar no último ponto de interrupção conhecido. Se uma fonte de dados não é rastreada totalmente dentro de uma janela de 24 horas, o indexador retomará a indexação no dia dois e em onde ela parou.
+ A partição de dados em fontes de dados individuais menores habilita o processamento paralelo. Você pode dividir os dados de origem em componentes menores, como em vários contêineres no armazenamento de BLOBs do Azure e, em seguida, criar vários [objetos de fonte de dados](/rest/api/searchservice/create-data-source) correspondentes no Azure pesquisa cognitiva que podem ser indexados em paralelo.

> [!NOTE]
> Os indexadores são específicos da fonte de dados, portanto usar uma abordagem do indexador só é viável para fontes de dados selecionadas no Azure: banco de dados [SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md), [armazenamento de tabelas](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>Verifique o argumento batchSize em criar indexador

Assim como acontece com a API de envio por push, os indexadores permitem que você configure o número de itens por lote. Para indexadores com base em [Criar o indexador do API REST](/rest/api/searchservice/Create-Indexer), pode-se definir o `batchSize` argumento para personalizar essa configuração para corresponder melhor as características de seus dados. 

Os tamanhos de lote padrão são específicos da fonte de dados. O banco de dados SQL do Azure e Azure Cosmos DB têm um tamanho de lote padrão de 1000. Por outro lado, a indexação de blob do Azure define o tamanho do lote em 10 documentos no reconhecimento do tamanho médio do documento maior. 

### <a name="scheduled-indexing"></a>Indexação agendada em

O agendando indexado é um mecanismo importante para processar grandes conjuntos de dados, assim como processos de execução lenta como a análise de imagens em um pipeline de pesquisa cognitiva. O processamento do indexador opera em uma janela de 24 horas. Se o processamento não for concluído em até 24 horas, o agendamento de comportamentos do indexador poderá entrar em ação. 

Por design, a indexação agendada começa em intervalos específicos, normalmente com o trabalho concluído antes da retomada no próximo intervalo agendado. No entanto, se o processamento não for concluído dentro do intervalo, o indexador será interrompido (por esgotar o tempo). No próximo intervalo, o processamento será retomado de onde parou, com o sistema controlando onde isso ocorreu. 

Em termos práticos, para cargas de índice que abrangem vários dias, você pode colocar o indexador em um agendamento de 24 horas. Quando a indexação é retomada para o próximo ciclo de 24 horas, ela é reiniciada no último documento válido conhecido. Dessa forma, um indexador pode percorrer uma lista de pendências de documentos em uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre como definir agendas em geral, consulte [criar API REST do indexador](/rest/api/searchservice/Create-Indexer) ou veja [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Um estratégia de indexação paralela baseia-se na indexação de várias fontes de dados de forma sincronizada, onde cada definição de fonte de dados especifica um subconjunto dos dados. 

Para requisitos de indexação computacionalmente intensivos e não rotineiros - como o OCR em documentos digitalizados em um pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - um estratégia de indexação de paralelo geralmente é a abordagem correta para a conclusão de um processo de execução longa no menor tempo. Para eliminar ou reduzir solicitações de consulta, a indexação paralela em um serviço que não esteja tratando simultaneamente de solicitações de consulta pode ser a melhor opção de estratégia para trabalhar em um grande corpo de conteúdo de processamento lento. 

O processamento paralelo tem estes elementos:

+ Subdividir a fonte de dados entre vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapeie cada conjunto de dados para sua própria [fonte de dados](/rest/api/searchservice/create-data-source), emparelhado com seu próprio [indexador](/rest/api/searchservice/create-indexer).
+ Para pesquisa cognitiva, referencie o mesmo [conjunto de habilidades](/rest/api/searchservice/create-skillset) em cada definição de indexador.
+ Grave no mesmo índice de pesquisa de destino. 
+ Agende todos os indexadores para serem executados ao mesmo tempo.

> [!NOTE]
> No Azure Pesquisa Cognitiva, você não pode atribuir réplicas ou partições individuais para indexação ou processamento de consulta. O sistema determina como os recursos são usados. Para entender o impacto no desempenho da consulta, você pode tentar a indexação paralela em um ambiente de teste antes de distribuí-lo para produção.  

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para os indexadores, a capacidade de processamento geralmente é baseada em um subsistema de indexador para cada SU (unidade de serviço) usada pelo serviço de pesquisa. Vários indexadores simultâneos são possíveis nos serviços de Pesquisa Cognitiva do Azure provisionados nas camadas básica ou Standard com pelo menos duas réplicas. 

1. No [portal do Azure](https://portal.azure.com), na página **Visão geral** do painel do serviço de pesquisa, verifique o **Tipo de preço** para confirmar se ele permite a indexação paralela. Os níveis Básico e Standard oferecem várias réplicas.

2. Você pode executar tantos indexadores em paralelo como o número de unidades de pesquisa em seu serviço. Em **configurações**  >  **escala** , [aumente réplicas](search-capacity-planning.md) ou partições para processamento paralelo: uma réplica ou partição adicional para cada carga de trabalho do indexador. Deixe um número suficiente para o volume de consulta existente. Não é vantajoso sacrificar as cargas de trabalho de consulta para indexação.

3. Distribua dados em vários contêineres em um nível que os indexadores do Azure Pesquisa Cognitiva podem alcançar. Podem ser várias tabelas no Banco de Dados SQL do Azure, vários contêineres no Armazenamento de Blobs do Azure ou várias coleções. Defina um objeto de fonte de dados para cada tabela ou contêiner.

4. Crie e agende vários indexadores a serem executados em paralelo:

   + Considere um serviço com seis réplicas. Configure seis indexadores, cada um mapeado para uma fonte de dados que contenha um sexto do conjunto de dados para uma divisão de 6 partes de todo o conjunto de dados. 

   + Aponte cada indexador para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo conjunto de habilidades.

   + Dentro de cada definição de indexador, agende o mesmo padrão de execução de tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria uma agenda em 15/05/2018 em todos os indexadores, em execução em intervalos de oito horas.

No horário agendado, todos os indexadores começam a execução, o carregamento de dados, a aplicação de enriquecimentos (se você tiver configurado um pipeline de pesquisa cognitiva) e a gravação no índice. O Azure Pesquisa Cognitiva não bloqueia o índice para atualizações. As gravações simultâneas são gerenciadas, com repetição quando uma gravação específica não é bem-sucedida na primeira tentativa.

> [!Note]
> Ao aumentar réplicas, considere aumentar a contagem de partições se o tamanho do índice for projetado para aumentar significativamente. As partições armazenam fatias de conteúdo indexado. Quanto mais partições houver, menor será a fatia que cada uma precisará armazenar.

## <a name="see-also"></a>Veja também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador de armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador de armazenamento de tabela do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)