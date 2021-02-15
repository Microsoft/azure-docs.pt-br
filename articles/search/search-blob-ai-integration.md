---
title: Usar o ia para enriquecer o conteúdo do blob
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre os recursos de análise de imagem e linguagem natural no Azure Pesquisa Cognitiva e como esses processos se aplicam ao conteúdo armazenado em BLOBs do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222041"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Usar o ia para processar e analisar o conteúdo do blob no Azure Pesquisa Cognitiva

O conteúdo no armazenamento de BLOBs do Azure composto por imagens ou texto longo não diferenciado pode passar por uma análise de aprendizado profundo para revelar e extrair informações valiosas úteis para aplicativos downstream. Usando o [enriquecimento de ia](cognitive-search-concept-intro.md), você pode:

+ Extrair texto de imagens usando reconhecimento óptico de caracteres (OCR)
+ Produzir uma descrição de cena ou marcas de uma foto
+ Detectar o idioma e traduzir o texto em idiomas diferentes
+ Inferir estrutura por meio do reconhecimento de entidade encontrando referências a pessoas, datas, lugares ou organizações

Embora você possa precisar apenas de um desses recursos de ia, é comum combinar vários deles no mesmo pipeline (por exemplo, extrair texto de uma imagem digitalizada e, em seguida, localizar todas as datas e locais referenciados). Também é comum incluir o processamento personalizado de ia ou de aprendizado de máquina na forma de pacotes externos de ponta ou de modelos internos adaptados aos seus dados e às suas necessidades.

Embora você possa aplicar o enriquecimento de AI a qualquer fonte de dados com suporte de um indexador de pesquisa, os BLOBs são as estruturas usadas com mais frequência em um pipeline de enriquecimento. Os resultados são retirados em um índice de pesquisa para pesquisa de texto completo ou Redirecionado de volta para o armazenamento do Azure para capacitar novas experiências de aplicativo que incluem a exploração de dados para cenários de descoberta ou análise. 

Neste artigo, veremos a sofisticação de AI por meio de uma ampla lente para que você possa entender rapidamente todo o processo, desde transformar dados brutos em BLOBs, até informações consultáveis em um índice de pesquisa ou em um repositório de conhecimento.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>O que significa "enriquecer" os dados de blob com o ia

O *enriquecimento de ia* faz parte da arquitetura de indexação do Azure pesquisa cognitiva que integra modelos de aprendizado de máquina da Microsoft ou de modelos de aprendizado personalizados fornecidos por você. Ele ajuda a implementar cenários de ponta a ponta em que você precisa processar BLOBs (tanto os existentes como os novos quando eles chegam ou são atualizados), desaparecendo abrir todos os formatos de arquivo para extrair imagens e texto, extrair as informações desejadas usando vários recursos de ia e indexá-los em um índice de pesquisa para pesquisa rápida, recuperação e exploração. 

As entradas são seus blobs, em um único contêiner, no armazenamento de blobs do Azure. Os BLOBs podem ser praticamente qualquer tipo de dados de texto ou imagem. 

A saída é sempre um índice de pesquisa, usado para pesquisa rápida de texto, recuperação e exploração em aplicativos cliente. Além disso, a saída também pode ser uma [*loja de conhecimento*](knowledge-store-concept-intro.md) que projeta documentos em BLOBs do Azure ou tabelas do Azure para análise downstream em ferramentas como Power bi ou em cargas de trabalho de ciência de dados.

No, between é a própria arquitetura de pipeline. O pipeline é baseado nos [*indexadores*](search-indexer-overview.md), aos quais você pode atribuir um conceda, que é [*composto por uma*](cognitive-search-working-with-skillsets.md)ou mais *habilidades* que fornecem o ia. A finalidade do pipeline é produzir *documentos aprimorados* que insiram o pipeline como conteúdo bruto, mas obtenham estrutura, contexto e informações adicionais ao passar pelo pipeline. Os documentos aprimorados são consumidos durante a indexação para criar índices invertidos e outras estruturas usadas em pesquisa de texto completo ou exploração e análise.

## <a name="required-resources"></a>Recursos necessários

Além do armazenamento de BLOBs do Azure e do Azure Pesquisa Cognitiva, você precisa de um terceiro serviço ou mecanismo que forneça o ia:

+ Para o ia interno, o Pesquisa Cognitiva integra-se com as APIs de processamento de linguagem natural e visão de serviços cognitivas do Azure. Você pode [anexar um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md) para adicionar OCR (reconhecimento óptico de caracteres), análise de imagem ou processamento de idioma natural (detecção de idioma, tradução de texto, reconhecimento de entidade, extração de frases-chave). 

+ Para o ia personalizado usando recursos do Azure, você pode definir uma habilidade personalizada que encapsula a função ou o modelo externo que você deseja usar. [Habilidades personalizadas](cognitive-search-custom-skill-interface.md) podem usar o código fornecido por Azure Functions, Azure Machine Learning, reconhecedor de formulário do Azure ou outro recurso que pode ser acessado por HTTPS.

+ Para o ia personalizado não Azure, seu modelo ou módulo precisa ser acessível a um indexador sobre HTTP.

Se você não tiver todos os serviços prontamente disponíveis, comece diretamente na página do seu portal de conta de armazenamento. Na página de navegação à esquerda, em **Serviço Blob** clique em **Adicionar Azure Cognitive Search** para criar um novo serviço ou selecione um existente. 

Depois de adicionar o Azure Pesquisa Cognitiva à sua conta de armazenamento, você pode seguir o processo padrão para enriquecer dados em qualquer fonte de dados do Azure. Recomendamos o assistente de **importação de dados** no Azure pesquisa cognitiva para uma introdução inicial fácil ao enriquecimento de ia. Você pode anexar um recurso de serviços cognitivas durante o fluxo de trabalho. Este guia de início rápido orienta você pelas etapas: [criar um pipeline de enriquecimento de ia no portal](cognitive-search-quickstart-blob.md). 

As seções a seguir examinam mais detalhadamente os componentes e o fluxo de trabalho.

## <a name="use-a-blob-indexer"></a>Configurar um indexador de Blob

O enriquecimento de ia é um complemento em um pipeline de indexação e, no Azure Pesquisa Cognitiva, esses pipelines são criados sobre um *indexador*. Um indexador é um subserviço com reconhecimento de fonte de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. Os indexadores geralmente são usados por si próprios para importação, separados do ia, mas se você quiser criar um pipeline de enriquecimento de ia, precisará de um indexador e de um configurador para seguir. Esta seção destaca o indexador; a próxima seção se concentra em habilidades.

Os BLOBs no armazenamento do Azure são indexados usando o [indexador de blob](search-howto-indexing-azure-blob-storage.md). Você pode invocar esse indexador usando o assistente de **importação de dados** , uma API REST ou um SDK. Um indexador de blob é invocado quando a fonte de dados usada pelo indexador é um contêiner de blob do Azure. Você pode indexar um subconjunto de seus BLOBs criando um diretório virtual, que você pode então passar como um parâmetro ou filtrando uma extensão de tipo de arquivo.

Um indexador faz a "quebra de documento", abrindo um blob para inspecionar o conteúdo. Depois de se conectar à fonte de dados, é a primeira etapa no pipeline. Para dados de BLOB, é o local em que os documentos PDF, Office docs, imagem e outros tipos de conteúdo são detectados. A quebra de documento com a extração de texto não é cobrada. A quebra de documento com extração de imagem é cobrada a taxas que você pode encontrar na [página de preços](https://azure.microsoft.com/pricing/details/search/).

Embora todos os documentos sejam rachados, o enriquecimento só ocorrerá se você fornecer explicitamente as habilidades para fazer isso. Por exemplo, se o pipeline consistir exclusivamente na análise de imagem, o texto em seu contêiner ou documentos será ignorado.

O indexador de blob vem com parâmetros de configuração e oferece suporte ao controle de alterações se os dados subjacentes fornecerem informações suficientes. Você pode aprender mais em [como configurar um indexador de blob](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Adicionar componentes de ia

O enriquecimento de ia se refere a módulos que procuram padrões ou características e, em seguida, executa uma operação de acordo. O reconhecimento facial em fotos, descrições de texto de fotos, detecção de frases-chave em um documento e OCR (ou reconhecimento de texto impresso ou manuscrito em arquivos binários) são exemplos ilustrativos.

No Azure Pesquisa Cognitiva, as *habilidades* são os componentes individuais do processamento de ia que você pode usar autônomos ou em combinação com outras habilidades. 

+ As habilidades internas são apoiadas por serviços cognitivas, com análise de imagem baseada em Pesquisa Visual Computacional e processamento de linguagem natural com base em Análise de Texto. Para obter a lista completa, consulte [habilidades internas para o enriquecimento de conteúdo](cognitive-search-predefined-skills.md).

+ Habilidades personalizadas são código personalizado, encapsulado em uma [definição de interface](cognitive-search-custom-skill-interface.md) que permite a integração no pipeline. Nas soluções de clientes, é uma prática comum usar ambos, com habilidades personalizadas que fornecem módulos de ia de software livre, de terceiros ou de terceiros.

Um conjunto *de qualificações é a* coleção de habilidades usadas em um pipeline e é invocado depois que a fase de quebra de documento disponibiliza o conteúdo. Um indexador pode consumir exatamente um qualificable, mas esse qualificable existe independentemente de um indexador para que você possa reutilizá-lo em outros cenários.

As habilidades personalizadas podem parecer complexas, mas podem ser simples e diretas em termos de implementação. Se você tiver pacotes existentes que fornecem modelos de classificação ou correspondência de padrões, o conteúdo extraído dos BLOBs poderá ser passado para esses modelos para processamento. Como a enriquecimento do ia é baseada no Azure, seu modelo também deve estar no Azure. Algumas metodologias de hospedagem comuns incluem o uso de [Azure Functions](cognitive-search-create-custom-skill-example.md) ou [contêineres](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

As habilidades internas apoiadas por serviços cognitivas exigem uma chave de assinatura All-in-One de [Serviços cognitivas anexada](cognitive-search-attach-cognitive-services.md) que fornece acesso ao recurso. Uma chave All-in-One fornece análise de imagem, detecção de idioma, tradução de texto e análise de texto. Outras habilidades internas são recursos do Azure Pesquisa Cognitiva e não exigem nenhum serviço ou chave adicional. A forma de texto, o divisor e a fusão são exemplos de habilidades auxiliares que às vezes são necessárias ao projetar o pipeline.

Se você usar apenas habilidades personalizadas e habilidades internas do utilitário, não haverá dependência ou custos relacionados a serviços cognitivas.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumir saída aprimorada do ia em soluções downstream

A saída do enriquecimento de AI é um índice de pesquisa no Azure Pesquisa Cognitiva ou uma loja de [conhecimento](knowledge-store-concept-intro.md) no armazenamento do Azure.

No Azure Pesquisa Cognitiva, um índice de pesquisa é usado para exploração interativa usando texto livre e consultas filtradas em um aplicativo cliente. Os documentos aprimorados criados por meio do ia são formatados em JSON e indexados da mesma forma que todos os documentos são indexados no Azure Pesquisa Cognitiva, aproveitando todos os benefícios que um indexador fornece. Por exemplo, durante a indexação, o indexador de blob refere-se aos parâmetros de configuração e configurações para utilizar qualquer mapeamento de campo ou lógica de detecção de alteração. Essas configurações estão totalmente disponíveis para indexação regular e cargas de trabalho de ia aprimoradas. Após a indexação, quando o conteúdo é armazenado no Azure Pesquisa Cognitiva, você pode criar consultas avançadas e expressões de filtro para entender o conteúdo.

No armazenamento do Azure, uma loja de conhecimento tem duas manifestações: um contêiner de BLOB ou tabelas no armazenamento de tabelas. 

+ Um contêiner de blob captura documentos aprimorados em sua totalidade, o que é útil se você quiser alimentar outros processos. 

+ Por outro lado, o armazenamento de tabelas pode acomodar projeções físicas de documentos aprimorados. Você pode criar fatias ou camadas de documentos aprimorados que incluem ou excluem partes específicas. Para análise no Power BI, as tabelas no armazenamento de tabelas do Azure tornam-se a fonte de dados para visualização e exploração adicionais.

Um documento aprimorado no final do pipeline difere de sua versão de entrada original pela presença de campos adicionais que contêm novas informações que foram extraídas ou geradas durante o enriquecimento. Dessa forma, você pode trabalhar com uma combinação de conteúdo original e criado, independentemente da estrutura de saída usada.

## <a name="next-steps"></a>Próximas etapas

Há muito mais que você pode fazer com a enriquecimento de ia para tirar o máximo proveito de seus dados no armazenamento do Azure, incluindo a combinação de serviços cognitivas de maneiras diferentes e a criação de habilidades personalizadas para casos em que não há nenhum serviço de cognitiva existente para o cenário. Você pode aprender mais seguindo os links abaixo.

+ [Carregar, baixar e listar blobs com o portal do Azure (armazenamento de blobs do Azure)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configurar um indexador de blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Visão geral de enriquecimento de ia (Azure Pesquisa Cognitiva)](cognitive-search-concept-intro.md) 
+ [Criar um skillset (Pesquisa Cognitiva do Azure)](cognitive-search-defining-skillset.md)
+ [Mapear nós em uma árvore de anotação (Pesquisa Cognitiva do Azure)](cognitive-search-output-field-mapping.md)