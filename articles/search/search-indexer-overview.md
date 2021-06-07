---
title: Visão geral do indexador
titleSuffix: Azure Cognitive Search
description: Rastreie o banco de dados SQL do Azure, o SQL Instância Gerenciada, o Azure Cosmos DB ou o armazenamento do Azure para extrair dado pesquisáveis e preencher um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100098088"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores na Pesquisa Cognitiva do Azure

Um *indexador* no Azure pesquisa cognitiva é um rastreador que extrai o texto pesquisável e os metadados de uma fonte de dados externa do Azure e popula um índice de pesquisa usando mapeamentos de campo para campo entre os dados de origem e o índice. Essa abordagem, às vezes, é chamada de "modelo de pull" porque o serviço recebe dados sem a necessidade de escrever nenhum código que adicione dados a um índice. Os indexadores também orientam os recursos de [enriquecimento](cognitive-search-concept-intro.md) de pesquisa cognitiva, integrando o processamento externo de conteúdo e rota para um índice.

Os indexadores são somente no Azure, com indexadores individuais para [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md) e [armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md). Ao configurar um indexador, você especificará uma fonte de dados (origem), bem como um índice (destino). Várias fontes, como o armazenamento de BLOBs, têm propriedades de configuração adicionais específicas para esse tipo de conteúdo.

Você pode executar indexadores sob demanda ou em uma agenda de atualização de dados recorrente que é executada sempre que a cada cinco minutos. Atualizações mais frequentes exigem um ["modelo de push"](search-what-is-data-import.md) que atualiza simultaneamente os dados no Azure pesquisa cognitiva e sua fonte de dados externa.

## <a name="usage-scenarios"></a>Cenários de uso

Você pode usar um indexador como o único meio de ingestão de dados ou como parte de uma combinação de técnicas que carregam e, opcionalmente, transformam ou enriquecem o conteúdo ao longo do caminho. A tabela a seguir resume os principais cenários.

| Cenário |Estratégia |
|----------|---------|
| Fonte de dados única | Esse padrão é o mais simples: uma fonte de dados é o único provedor de conteúdo para um índice de pesquisa. Na origem, você identificará um campo que contém valores exclusivos para servir como a chave do documento no índice de pesquisa. O valor exclusivo será usado como um identificador. Todos os outros campos de origem são mapeados implicitamente ou explicitamente para campos correspondentes em um índice. </br></br>Um importante argumento é que o valor de uma chave de documento é proveniente dos dados de origem. Um serviço de pesquisa não gera valores de chave. Em execuções subsequentes, os documentos de entrada com novas chaves são adicionados, enquanto os documentos de entrada com chaves existentes são mesclados ou substituídos, dependendo se os campos de índice são nulos ou preenchidos. |
| Várias fontes de dados | Um índice pode aceitar conteúdo de várias fontes, onde cada execução traz novo conteúdo de uma fonte diferente. </br></br>Um resultado pode ser um índice que obtém documentos após a execução de cada indexador, com documentos inteiros criados com toda a origem. Por exemplo, os documentos 1-100 são do armazenamento de BLOBs, os documentos 101-200 são do Azure SQL e assim por diante. O desafio para esse cenário reside na criação de um esquema de índice que funciona para todos os dados de entrada e uma estrutura de chave de documento uniforme no índice de pesquisa. Nativamente, os valores que identificam exclusivamente um documento são metadata_storage_path em um contêiner de BLOB e uma chave primária em uma tabela SQL. Você pode imaginar que uma ou ambas as origens devem ser corrigidas para fornecer valores de chave em um formato comum, independentemente da origem do conteúdo. Para esse cenário, você deve esperar executar algum nível de pré-processamento para homogenize os dados para que eles possam ser extraídos em um único índice. </br></br>Um resultado alternativo pode ser pesquisar documentos que são parcialmente populados na primeira execução e, em seguida, mais populados por execuções subsequentes para trazer valores de outras fontes. Por exemplo, os campos 1-10 são do armazenamento de BLOBs, 11-20 do Azure SQL e assim por diante. O desafio desse padrão é verificar se cada execução de indexação está direcionando o mesmo documento. A mesclagem de campos em um documento existente requer uma correspondência na chave do documento. Para ver uma demonstração desse cenário, consulte [tutorial: índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
| Vários indexadores | Se você estiver usando várias fontes de dados, também poderá precisar de vários indexadores se precisar variar os parâmetros de tempo de execução, o agendamento ou os mapeamentos de campo. Embora vários conjuntos de fontes de dados de indexador possam ter como destino o mesmo índice, tenha cuidado com as execuções do indexador que podem substituir os valores existentes no índice. Se um segundo indexador-fonte de dados tiver como destino os mesmos documentos e campos, todos os valores da primeira execução serão substituídos. Os valores de campo são substituídos por completo; um indexador não pode mesclar valores de várias execuções no mesmo campo.</br></br>Outro caso de uso de vários indexadores é [a escala entre regiões de pesquisa cognitiva](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Você pode ter cópias do mesmo índice de pesquisa em regiões diferentes. Para sincronizar o conteúdo do índice de pesquisa, você pode ter vários indexadores recebendo da mesma fonte de dados, em que cada indexador tem como alvo um índice de pesquisa diferente.</br></br>A [indexação paralela](search-howto-large-index.md#parallel-indexing) de conjuntos de dados muito grandes também requer uma estratégia de vários indexadores. Cada indexador tem como alvo um subconjunto dos dados. |
| Transformação de conteúdo | O Pesquisa Cognitiva dá suporte a comportamentos opcionais de [enriquecimento de ia](cognitive-search-concept-intro.md) que adicionam análise de imagem e processamento de linguagem natural para criar um novo conteúdo e estrutura pesquisáveis. O enriquecimento de ia é orientado por indexador, por meio de um associador de [qualificações](cognitive-search-working-with-skillsets.md). Para executar o reenriquecimento do ia, o indexador ainda precisa de um índice e uma fonte de dados do Azure, mas nesse cenário, adiciona o processamento do contratador de qualificações à execução do indexador. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

Armazenamentos de dados de rastreamento de indexadores no Azure.

+ [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em versão prévia)
+ [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Instância Gerenciada de SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server em Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Conexões de indexador para fontes de dados remotas podem ser feitas usando conexões de Internet padrão (públicas) ou conexões privadas criptografadas quando você usa redes virtuais do Azure para aplicativos cliente. Você também pode configurar conexões para autenticar usando uma identidade de serviço confiável. Para obter mais informações sobre conexões seguras, consulte [concedendo acesso por meio de pontos de extremidade privados](search-indexer-securing-resources.md#granting-access-via-private-endpoints) e [conectar-se a uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Estágios da indexação

Em uma execução inicial, quando o índice estiver vazio, um indexador lerá todos os dados fornecidos na tabela ou no contêiner. Em execuções subsequentes, o indexador normalmente pode detectar e recuperar apenas os dados que foram alterados. Para dados de BLOB, a detecção de alteração é automática. Para outras fontes de dados como Azure SQL ou Cosmos DB, a detecção de alteração deve ser habilitada.

Para cada documento que recebe, um indexador implementa ou coordena várias etapas, da recuperação de documentos até um mecanismo de pesquisa final "entrega" para indexação. Opcionalmente, um indexador também é fundamental para conduzir a execução e as saídas do contratado, supondo que um contratador seja definido.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Estágios do indexador" border="false":::

### <a name="stage-1-document-cracking"></a>Estágio 1: quebra de documento

A quebra de documentos é o processo de abertura de arquivos e extração de conteúdo. Dependendo do tipo de fonte de dados, o indexador tentará executar operações diferentes para extrair conteúdo potencialmente indexável.  

Exemplos:  

+ Quando o documento for um registro em uma [fonte de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), o indexador extrairá cada um dos campos para o registro.
+ Quando o documento for um arquivo PDF em uma [fonte de dados de armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md), o indexador extrairá texto, imagens e metadados.
+ Quando o documento for um registro em uma [Cosmos DB fonte de dados](search-howto-index-cosmosdb.md), o indexador extrairá os campos e subcampos do documento Cosmos DB.

### <a name="stage-2-field-mappings"></a>Etapa 2: mapeamentos de campo 

Um indexador extrai o texto de um campo de origem e o envia para um campo de destino em um índice ou repositório de conhecimento. Quando os nomes e tipos de campo coincidem, o caminho fica claro. No entanto, talvez você queira nomes ou tipos diferentes na saída; nesse caso, você precisa informar ao indexador como mapear o campo. 

Essa etapa ocorre após a quebra do documento, mas antes das transformações, quando o indexador está lendo dos documentos de origem. Quando você define um [mapeamento de campo](search-indexer-field-mappings.md), o valor do campo de origem é enviado como está para o campo de destino sem modificações. 

### <a name="stage-3-skillset-execution"></a>Estágio 3: execução do Configurador de habilidades

A execução de contratação é uma etapa opcional que invoca o processamento interno ou personalizado de ia. Talvez você precise dele para reconhecimento óptico de caracteres (OCR) na forma de análise de imagem, se os dados de origem forem uma imagem binária, ou se o conteúdo estiver em idiomas diferentes. 

Seja qual for a transformação, a execução do contextset é onde ocorre o aprimoramento. Se um indexador [for um pipeline](cognitive-search-defining-skillset.md) , você poderá considerar um consider como um "pipeline no pipeline".

### <a name="stage-4-output-field-mappings"></a>Estágio 4: mapeamentos de campo de saída

Se você incluir um configurador de qualificações, provavelmente será necessário incluir mapeamentos de campo de saída. A saída de um habilidades é, na verdade, uma árvore de informações chamada de *documento aprimorado*. Os mapeamentos de campo de saída permitem que você Selecione quais partes desta árvore mapear em campos no índice. Saiba como [definir mapeamentos de campo de saída](cognitive-search-output-field-mapping.md).

Enquanto os mapeamentos de campo associam valores textuais da fonte de dados aos campos de destino, os mapeamentos de campo de saída informam ao indexador como associar os valores transformados no documento aprimorado aos campos de destino no índice. Ao contrário dos mapeamentos de campo, que são considerados opcionais, sempre será necessário definir um mapeamento de campo de saída para qualquer conteúdo transformado que precise residir em um índice.

A imagem a seguir mostra uma representação de [sessão de depuração](cognitive-search-debug-session.md) do indexador de exemplo dos estágios do indexador: quebra de documento, mapeamentos de campo, execução de qualificações e mapeamentos de campo de saída.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sessão de depuração de exemplo" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Fluxo de trabalho básico

Os indexadores podem oferecer recursos que são exclusivos da fonte de dados. Nesse sentido, alguns aspectos de configuração da fonte de dados ou do indexador variam de acordo com o tipo de indexador. No entanto, todos os indexadores compartilham a mesma composição básica e os mesmos requisitos. As etapas que são comuns a todos os indexadores são abordadas a seguir.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

Os indexadores exigem um objeto de *fonte de dados* que fornece uma cadeia de conexão e possivelmente credenciais. Chame a classe [criar fonte de dados (REST)](/rest/api/searchservice/create-data-source) ou [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) para criar o recurso.

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice

Um indexador automatizará algumas tarefas relacionadas à ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, você deve ter um índice predefinido com campos iguais aos da sua fonte de dados externa. Os campos precisam corresponder por nome e tipo de dados. Caso contrário, você pode [definir mapeamentos de campo](search-indexer-field-mappings.md) para estabelecer a associação. Para obter mais informações sobre como estruturar um índice, consulte [criar um índice (REST)](/rest/api/searchservice/Create-Index) ou uma [classe SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Embora indexadores não consigam gerar um índice para você, o assistente de **Importar dados** no portal pode ajudar. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir de metadados existentes na fonte, apresentando um esquema de índice preliminar que você pode editar em linha enquanto o assistente estiver ativo. Assim que o índice é criado no serviço, outras edições no portal são, na sua maior parte, limitadas a adicionar novos campos. Leve o assistente em consideração para criar, mas não para revisar um índice. Para o aprendizado prático, percorra o [passo a passo portal](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Etapa 3: criar e executar (ou agendar) o indexador

Um indexador é executado quando você [cria um indexador](/rest/api/searchservice/Create-Indexer) no serviço de pesquisa pela primeira vez. É apenas quando você cria ou executa o indexador que descobrirá se a fonte de dados estiver acessível ou se o consider for válido. Após a primeira execução, você pode executá-la novamente sob demanda usando o [indexador de execução](/rest/api/searchservice/run-indexer)ou pode [definir um agendamento recorrente](search-howto-schedule-indexers.md). 

Você pode monitorar o status do indexador no portal ou por meio da [API obter status do indexador](/rest/api/searchservice/get-indexer-status). Você também deve [executar consultas no índice](search-query-create.md) para verificar se o resultado é o esperado.

## <a name="next-steps"></a>Próximas etapas

Agora que você foi apresentado, uma próxima etapa é examinar as propriedades e os parâmetros do indexador, o agendamento e o monitoramento do indexador. Como alternativa, você pode retornar à lista de [fontes de dados com suporte](#supported-data-sources) para obter mais informações sobre uma fonte específica.

+ [Criar indexadores](search-howto-create-indexers.md)
+ [Redefinir e executar indexadores](search-howto-run-reset-indexers.md)
+ [Indexadores do agendamento](search-howto-schedule-indexers.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Monitorar o status do indexador](search-howto-monitor-indexers.md)