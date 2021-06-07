---
title: Conceitos de enriquecimento de ia
titleSuffix: Azure Cognitive Search
description: Extração de conteúdo, NLP (processamento de idioma natural) e processamento de imagem são usados para criar conteúdo pesquisável em índices de Pesquisa Cognitiva do Azure com habilidades cognitivas predefinidas e algoritmos de ia personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037244"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Enriquecimento de IA no Azure Cognitive Search

O enriquecimento de ia é uma extensão de [indexadores](search-indexer-overview.md) que podem ser usados para extrair texto de imagens, BLOBs e outras fontes de dados não estruturados. Enriquecimento e extração tornam seu conteúdo mais pesquisável nos objetos de saída do indexador, um [índice de pesquisa](search-what-is-an-index.md) ou uma [loja de conhecimento](knowledge-store-concept-intro.md). 

Extração e enriquecimento são implementados usando *habilidades cognitivas* anexadas ao pipeline controlado pelo indexador. Você pode usar habilidades internas da Microsoft ou inserir o processamento externo em uma [*habilidade personalizada*](cognitive-search-create-custom-skill-example.md) que você criar. Exemplos de uma habilidade personalizada podem ser um módulo de entidade personalizado ou classificador de documento direcionado a um domínio específico, como finanças, publicações científicas ou medicina.

As habilidades internas se enquadram nessas categorias: 

+ As habilidades de **Processamento de linguagem natural** incluem [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [detecção de idioma](cognitive-search-skill-language-detection.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), manipulação de texto, [detecção de sentimento](cognitive-search-skill-sentiment.md) e [detecção de PII](cognitive-search-skill-pii-detection.md). Com essas habilidades, o texto não estruturado é mapeado como campos pesquisáveis e filtráveis em um índice.

+ As habilidades de **processamento de imagem** incluem [OCR (reconhecimento óptico de caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md), como detecção facial, interpretação de imagem, reconhecimento de imagem (famosas pessoas e pontos de referência) ou atributos como a orientação da imagem. Essas habilidades criam representações de texto de conteúdo de imagem, tornando-o pesquisável usando os recursos de consulta do Azure Pesquisa Cognitiva.

![Diagrama do pipeline de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "visão geral do pipeline de enriquecimento")

As habilidades internas no Azure Pesquisa Cognitiva se baseiam em modelos de aprendizado de máquina pré-treinados no API de Serviços Cognitivos: [Pesquisa Visual computacional](../cognitive-services/computer-vision/index.yml) e [análise de texto](../cognitive-services/text-analytics/overview.md). Você pode anexar um recurso de serviços cognitivas se desejar aproveitar esses recursos durante o processamento de conteúdo.

O processamento de imagens e em idioma natural é aplicada durante a fase de ingestão de dados, com os resultados se tornando parte da composição de um documento em um índice pesquisável na Pesquisa Cognitiva do Azure. Os dados são originados como um conjunto de dados do Azure e, em seguida, enviados por meio de um pipeline de indexação usando o que ocorrer [habilidades internas](cognitive-search-predefined-skills.md) que você precisa.  

## <a name="feature-availability"></a>Disponibilidade de recursos

O enriquecimento de ia está disponível em regiões em que os serviços cognitivas do Azure também estão disponíveis.  Você pode verificar a disponibilidade atual do enriquecimento de ia na página [produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=search) .  O enriquecimento de ia está disponível em todas as regiões com suporte, exceto:

+ Sudeste da Austrália
+ Norte da China 2
+ Leste da Noruega
+ Centro-Oeste da Alemanha

Se o serviço de pesquisa estiver localizado em uma dessas regiões, você não poderá criar e usar o habilidades, mas todas as outras funcionalidades do serviço de pesquisa estarão disponíveis e terão suporte total.

## <a name="when-to-use-ai-enrichment"></a>Quando usar o enriquecimento de ia

Você deve considerar o uso de habilidades cognitivas incorporadas se o conteúdo bruto for texto não estruturado, conteúdo de imagem ou conteúdo que precise de detecção e tradução de idioma. A aplicação de IA por meio de habilidades cognitivas incorporadas pode desbloquear esse conteúdo, aumentando seu valor e utilitário em seus aplicativos de pesquisa e ciência de dados. 

Além disso, você pode considerar adicionar uma habilidade personalizada se tiver código de software livre, de terceiros ou de clientes diretos que deseja integrar ao pipeline. Os modelos de classificação que identificam as características de destaque de vários tipos de documento se enquadram nessa categoria, mas qualquer pacote que adiciona valor ao seu conteúdo pode ser usado.

### <a name="more-about-built-in-skills"></a>Mais informações sobre habilidades incorporadas

Um [técnico](cognitive-search-defining-skillset.md) que é montado usando habilidades internas é adequado para os seguintes cenários de aplicativo:

+ Documentos digitalizados (JPEG) que você deseja tornar susceptíveis a pesquisas de texto completo. Você pode anexar uma habilidade de OCR (reconhecimento de caractere óptico) para identificar, extrair e ingerir textos de arquivos JPEG.

+ PDFs com combinações de textos e imagens. Os textos em PDFs podem ser extraídos durante a indexação sem o uso de etapas de enriquecimento, mas a adição do processamento de imagens e em idioma natural pode, muitas vezes, produzir um resultado melhor do que a indexação padrão oferece.

+ Conteúdo multilíngue ao qual você deseja aplicar a detecção de idioma e, possivelmente, a tradução de texto.

+ Documentos não estruturados ou semiestruturados que contêm dados com significado ou contexto inerente, oculto dentro do documento mais amplo. 

  Os BLOBs em particular geralmente contêm um grande corpo de conteúdo que é empacotado em um único "campo". Ao anexar habilidades de processamento de imagem e de linguagem natural a um indexador, você pode criar novas informações que estavam contidas no conteúdo bruto, mas não eram destacadas como campos distintos. Algumas habilidades cognitivas internas prontas para uso que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as habilidades internas podem ser usadas para reestruturar o conteúdo por meio de operações de divisão de texto, mesclagem e modelagem.

### <a name="more-about-custom-skills"></a>Saiba mais sobre habilidades incorporadas

As habilidades personalizadas podem dar suporte a cenários mais complexos, como o reconhecimento de formulários ou a detecção de entidades personalizadas usando um modelo que você fornece e encapsula na [interface da Web de habilidades personalizadas](cognitive-search-custom-skill-interface.md). Vários exemplos de habilidades personalizadas incluem o [Reconhecimento de Formulários](../cognitive-services/form-recognizer/overview.md), a integração da [API de Pesquisa de Entidade do Bing](./cognitive-search-create-custom-skill-example.md) e o [reconhecimento de entidades personalizadas](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Etapas em um pipeline de enriquecimento <a name="enrichment-steps"></a>

Um pipeline de enriquecimento é baseado em [*indexadores*](search-indexer-overview.md). Os indexadores preenchem um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados para decifração de documentos. Habilidades, agora anexadas a indexadores, interceptar e enriquecer documentos de acordo com os conjuntos de qualificações que você definir. Após a indexação, você pode acessar o conteúdo por meio de solicitações de pesquisa em todos os [tipos de consulta compatíveis com a Pesquisa Cognitiva do Azure](search-query-overview.md).  Se você estiver começando a usar indexadores, esta seção o orientará pelas etapas.

### <a name="step-1-connection-and-document-cracking-phase"></a>Etapa 1: fase de violação de conexão e documento

No início do pipeline, você tem texto não estruturado ou conteúdo de não texto (como imagens, documentos digitalizados ou arquivos JPEG). Os dados precisam existir em um serviço de armazenamento de dados do Azure que possa ser acessado por um indexador. Os indexadores podem "desbloquear" documentos de origem para extrair o texto da fonte de dados. A quebra de documentos é o processo de extração ou criação de conteúdo de texto de fontes que não sejam de texto durante a indexação.

![Fase de quebra de documento](./media/cognitive-search-intro/document-cracking-phase-blowup.png "quebra de documento")

 As fontes com suporte incluem o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, o Banco de Dados SQL do Azure e o Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de arquivo: arquivos PDF, do Word, do PowerPoint e CSV. Para obter a lista completa, confira [Formatos com suporte](search-howto-indexing-azure-blob-storage.md#SupportedFormats). A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Etapa 2: fase de enriquecimento e habilidades cognitivas

O enriquecimento é realizado com *habilidades cognitivas* executando operações atômicas. Por exemplo, depois de ter rachado um PDF, você pode aplicar o reconhecimento de entidade, detecção de idioma ou extração de frases-chave para produzir novos campos no índice que não estão disponíveis nativamente na origem. A coleção de habilidades usada em seu pipeline é chamada de *conjunto de habilidades*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um conjunto de habilidades baseia-se em [habilidades cognitivas internas](cognitive-search-predefined-skills.md) ou em [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta ao conjunto de habilidades. Um conjunto de habilidades pode ser mínima ou altamente complexo e determina não apenas o tipo de processamento, mas também a ordem das operações. Um conjunto de habilidades mais os mapeamentos de campo definidos como parte de um indexador definem a especificação completa do pipeline de enriquecimento. Para obter mais informações sobre como reunir todas essas partes, confira [Define a skillset](cognitive-search-defining-skillset.md) (Definir um conjunto de habilidades).

Internamente, o pipeline gera uma coleção de documentos enriquecidos. Você pode decidir quais partes dos documentos enriquecidos devem ser mapeadas para campos indexáveis no índice de pesquisa. Por exemplo, se você aplicou a extração de frases-chave e as habilidades de reconhecimento de entidade, esses novos campos se tornarão parte do documento aprimorado e poderão ser mapeados para campos em seu índice. Consulte [Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicionar um elemento knowledgeStore para salvar os enriquecimentos

[Pesquisar API REST – Version = 2020-06-30](/rest/api/searchservice/) estende habilidades com uma `knowledgeStore` definição que fornece uma conexão de armazenamento do Azure e projeções que descrevem como os aprimoramentos são armazenados. Isso é adicional ao índice. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Com o repositório de conhecimento, os documentos enriquecidos são preservados. Para obter mais informações, consulte [Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Etapa 3: Pesquisar índice e acesso baseado em consulta

Quando o processamento é concluído, você tem um índice de pesquisa que consiste em documentos enriquecidos, com texto totalmente pesquisável na Pesquisa Cognitiva do Azure. [Consultando o índice](search-query-overview.md) é como os desenvolvedores e usuários acessam conteúdo enriquecido gerado pelo pipeline. 

![Índice com o ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com o ícone de pesquisa")

O índice é como qualquer outro item que você possa criar para a Pesquisa Cognitiva do Azure: é possível complementá-lo com analisadores personalizados, invocar consultas de pesquisa difusa, adicionar uma pesquisa filtrada ou fazer experimentos com perfis de pontuação para remodelar os resultados da pesquisa.

Os índice são gerados por meio de um esquema de índice que define os campos, os atributos e outras construções anexadas a um índice específico, como perfis de pontuação e mapas de sinônimo. Depois que um índice é definido e populado, é possível indexá-lo de forma incremental para capturar documentos de origem novos e atualizados. Algumas modificações exigem uma recompilação completa. Você deve usar um conjunto de dados pequeno até que o design de esquema fique estável. Para obter mais informações, confira [Como recompilar um índice](search-howto-reindex.md).

**Lista de verificação: um fluxo de trabalho típico**

1. Subconjunto de dados de origem do Azure em uma amostra representativa. A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

1. Crie um [objeto de fonte de dados](/rest/api/searchservice/create-data-source) na Pesquisa Cognitiva do Azure para fornecer uma cadeia de conexão para a recuperação de dados.

1. Crie um [conjunto de habilidades](/rest/api/searchservice/create-skillset) com etapas de enriquecimento.

1. Defina o [esquema de índice](/rest/api/searchservice/create-index). A coleção *Campos* inclui campos da fonte de dados. Você também deve usar stub de campos adicionais para manter os valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexador](/rest/api/searchservice/create-indexer) referenciando a fonte de dados, o conjunto de habilidades e o índice.

1. Dentro do indexador, adicione *outputFieldMappings*. Esta seção mapeia a saída do conjunto de habilidades (na etapa 3) para os campos de entrada no esquema de índice (na etapa 4).

1. Envie a solicitação *Criar Indexador* recém-criada (uma solicitação POST com uma definição de indexador no corpo da solicitação) para expressar o indexador na Pesquisa Cognitiva do Azure. Esta etapa é como você executa o indexador, invocando o pipeline.

1. Execute consultas para avaliar os resultados e modificar o código para atualizar os conjuntos de habilidades, o esquema ou a configuração do indexador.

1. [Reinicie o indexador](search-howto-reindex.md) antes de recompilar o pipeline.

## <a name="next-steps"></a>Próximas etapas

+ [Links de documentação do enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Exemplo: criando uma habilidade personalizada para o enriquecimento do ia (C#)](cognitive-search-create-custom-skill-example.md)
+ [Início rápido: Experimente o enriquecimento de ia em um passo a passo do portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Saiba mais sobre as APIs de enriquecimento do ia](cognitive-search-tutorial-blob.md)
+ [Repositório de conhecimento](knowledge-store-concept-intro.md)
+ [Criar um repositório de conhecimento no REST](knowledge-store-create-rest.md)
+ [Dicas de solução de problemas](cognitive-search-concept-troubleshooting.md)
