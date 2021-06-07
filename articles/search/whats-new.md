---
title: Novidades no Azure Cognitive Search
description: Comunicados de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: b9ae576a2d0ab8aee00be5b755c348806b412bef
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108482"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades no Azure Cognitive Search

Conheça o que há de novo no serviço. Marque esta página para manter-se atualizado quanto ao serviço. Confira a [Lista de versão prévia dos recursos](search-api-preview.md) com todas as funcionalidades que ainda não estão em disponibilidade geral.

## <a name="march-2021"></a>Março de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrição | Disponibilidade  |
|------------------------------|---------------|---------------|
| [Pesquisa semântica](semantic-search-overview.md) | Uma coleção de recursos relacionados a consulta que aprimoram significativamente a relevância dos resultados da pesquisa por meio de ajustes mínimos a uma solicitação de consulta. </br></br>A [classificação semântica](semantic-ranking.md) calcula pontuações de relevância usando o significado semântico por trás das palavras e do conteúdo. </br></br>As [legendas semânticas](semantic-how-to-query-request.md) retornam passagens relevantes do documento que melhor o resumem, com destaques sobre os termos ou as frases mais importantes. </br></br>As [respostas semânticas](semantic-answers.md) retornam passagens importantes, extraídas de um documento de pesquisa, que são formuladas como uma resposta direta a uma consulta que se parece com uma pergunta. | Versão prévia pública ([por solicitação](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Use a api-version=2020-06-30-Preview de [Pesquisar Documentos (REST)](/rest/api/searchservice/preview-api/search-documents) ou o [Gerenciador de pesquisa](search-explorer.md) no portal do Azure. </br></br>Aplicam-se restrições de região e nível. |
| [Verificação ortográfica dos termos da consulta](speller-how-to-add.md) | Antes que os termos da consulta cheguem ao mecanismo de pesquisa, você pode solicitar a verificação de erros ortográficos. A opção `speller` funciona com qualquer tipo de consulta (simples, completa ou semântica). |  Visualização pública, somente REST, api-version=2020-06-30-Preview|
| [Indexador do SharePoint Online](search-howto-index-sharepoint-online.md) | Este indexador conecta você a um site do SharePoint Online para que possa indexar o conteúdo de uma biblioteca de documentos. | Visualização pública, somente REST, api-version=2020-06-30-Preview |
| [Normalizadores](search-normalizers.md) | Os normalizadores fornecem pré-processamento de texto simples como maiúsculas e minúsculas, remoção de acentos, asciifolding e assim por diante, sem passar por toda a cadeia de análise.| Visualização pública, somente REST, api-version=2020-06-30-Preview |
[**Habilidade de Pesquisa de Entidade Personalizada**](cognitive-search-skill-custom-entity-lookup.md ) |  Uma habilidade cognitiva que procura o texto de uma lista personalizada e definida pelo usuário de palavras e frases. Usando essa lista, ela rotula todos os documentos com entidades correspondentes. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências semelhantes, mas não exatas. | Em disponibilidade geral. |
|

## <a name="february-2021"></a>Fevereiro de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrição | Disponibilidade  |
|------------------------------|---------------|---------------|
| [Redefinir Documentos (versão prévia)](search-howto-run-reset-indexers.md) |  Reprocessa os documentos de pesquisa selecionados individualmente nas cargas de trabalho do indexador. | [API REST de Pesquisa 2020-06-30-Versão prévia](/rest/api/searchservice/index-preview) |
| [Zonas de Disponibilidade](search-performance-optimization.md#availability-zones)| Pesquise serviços com duas ou mais réplicas em determinadas regiões, conforme listado em [Escala para desempenho](search-performance-optimization.md#availability-zones) e obtenha resiliência tendo réplicas em duas ou mais localizações físicas distintas.  | A região e a data da criação do serviço de pesquisa determinam a disponibilidade. Confira o artigo Escala para desempenho para obter detalhes. |
| [CLI do Azure](/cli/azure/search) </br>[PowerShell do Azure](/powershell/module/az.search/) | Novas revisões agora fornecem a gama completa de operações na API REST de gerenciamento 2020-08-01, incluindo suporte para regras de firewall de IP e ponto de extremidade privado. | Em disponibilidade geral. |

## <a name="january-2021"></a>Janeiro de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Descrição | Disponibilidade  |
|------------------------------|-------------|---------------|
| [Acelerador de solução para o Azure Cognitive Search e o QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Extrai perguntas e respostas do documento e sugere as respostas mais relevantes. Encontre um aplicativo de demonstração ao vivo em [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo).  | Projeto de software livre (sem SLA) |

## <a name="2020-archive"></a>Arquivos de 2020

| Mês | Recurso | Descrição |
|-------|---------|-------------|
| Novembro | [Criptografia de chave gerenciada pelo cliente (estendida)](search-security-manage-encryption-keys.md) | Estende a criptografia gerenciada pelo cliente para toda a gama de ativos criados e gerenciados por um serviço de pesquisa. Em disponibilidade geral.|
| Setembro | [Extensão do Visual Studio Code para o Azure Cognitive Search](search-get-started-vs-code.md) | Adiciona um workspace, um painel de navegação, o IntelliSense e modelos para criar índices, indexadores, fontes de dados e conjuntos de habilidades. Esse recurso está atualmente em visualização pública.| 
| Setembro | [Identidade do serviço gerenciado (indexadores)](search-howto-managed-identities-data-sources.md) | Em disponibilidade geral.  |
| Setembro | [Solicitações de saída usando um link privado](search-indexer-howto-access-private.md) | Em disponibilidade geral.  |
| Setembro | [API REST de Gerenciamento (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Em disponibilidade geral. |
| Setembro | [API REST de Gerenciamento (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Adiciona o recurso de link privado compartilhado ao Azure Functions e ao Azure SQL para Bancos de Dados MySQL. |
| Setembro | [SDK do .NET de Gerenciamento 4.0](/dotnet/api/overview/azure/search/management) |  Atualização do SDK do Azure para o SDK de gerenciamento, versão de API REST direcionada 2020-08-01. Em disponibilidade geral.|
| Agosto | [criptografia dupla](search-security-overview.md#encryption) | Em disponibilidade geral em todos os serviços de pesquisa criados após 1º de agosto de 2020 nestas regiões: Oeste dos EUA 2, Leste dos EUA, Centro-Sul dos EUA, US Gov – Virgínia e US Gov – Arizona. |
| Julho | [Biblioteca de clientes Azure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) | SDK do Azure para .NET, em disponibilidade geral. |
| Julho | [Biblioteca de clientes azure.search.documents](/python/api/overview/azure/search-documents-readme)  | SDK do Azure para Python, em disponibilidade geral. |
| Julho | [Biblioteca de clientes@azure/search-documents](/javascript/api/overview/azure/search-documents-readme)  | SDK do Azure para JavaScript, em disponibilidade geral. |
| Junho | [Repositório de conhecimento](knowledge-store-concept-intro.md) | Em disponibilidade geral. |
| Junho | [API REST de Pesquisa de 2020-06-30](/rest/api/searchservice/) | Em disponibilidade geral. |
| Junho | [API REST de Pesquisa 2020-06-30-Versão prévia](/rest/api/searchservice/) | Adiciona a opção Redefinir Conjunto de Habilidades para reprocessar habilidades de maneira seletiva e o enriquecimento incremental. |
| Junho | [Algoritmo de relevância Okapi BM25](index-ranking-similarity.md) | Em disponibilidade geral. |
| Junho |  **executionEnvironment** (aplica-se aos serviços de pesquisa que usam o Link Privado do Azure.) | Em disponibilidade geral. |
| Junho | [Habilidade do AML (versão prévia)](cognitive-search-aml-skill.md) | Uma habilidade cognitiva que estende o enriquecimento de IA com um modelo personalizado do AML (Azure Machine Learning). |
| Mai | [Sessões de depuração (versão prévia)](cognitive-search-debug-session.md) | Depurador do conjunto de habilidades no portal.  |
| Mai | [Regras de IP para suporte de firewall de entrada](service-configure-firewall.md) | Em disponibilidade geral.  |
| Mai | [Link Privado do Azure para um ponto de extremidade de pesquisa privado](service-create-private-endpoint.md) | Em disponibilidade geral.  |
| Mai | [Identidade do serviço gerenciado (indexadores) – (versão prévia)](search-howto-managed-identities-data-sources.md) | Conecte-se às fontes de dados do Azure usando uma identidade gerenciada.  |
| Mai | [parâmetro de consulta sessionId](index-similarity-and-scoring.md), [parâmetro scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics)  | Estatísticas de pesquisa global, úteis para [modelos de machine learning (LearnToRank) para relevância de pesquisa](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Mai | [Expansão da pontuação de relevância de featuresMode (versão prévia)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Março  | [Exclusão reversível de blob nativo (versão prévia)](search-howto-index-changed-deleted-blobs.md) | Exclui documentos de pesquisa se o blob de origem é excluído de maneira reversível no Armazenamento de Blobs. |
|Março  | [API REST de gerenciamento (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Em disponibilidade geral. |
|Fevereiro | [Habilidade de Detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md)  | Uma habilidade cognitiva que extrai e mascara informações pessoais. |
|Fevereiro | [Habilidades de Pesquisa de Entidade Personalizada (versão prévia)](cognitive-search-skill-custom-entity-lookup.md) | Uma habilidade cognitiva que localiza palavras e frases em uma lista e rotula todos os documentos com entidades correspondentes.  |
|Janeiro | [Criptografia de chave gerenciada pelo cliente](search-security-manage-encryption-keys.md) | Disponível para o público geral  |
|Janeiro | [Regras de IP para suporte de firewall de entrada (versão prévia)](service-configure-firewall.md) | Novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|Janeiro | [Criar um ponto de extremidade privado (versão prévia)](service-create-private-endpoint.md) | Configure um Link Privado para conexões seguras com o serviço de pesquisa. Essa versão prévia do recurso tem uma dependência do [Link Privado do Azure](../private-link/private-link-overview.md) e da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) como parte da solução. |

## <a name="2019-archive"></a>Arquivos de 2019

| Mês | Recurso | Descrição |
|-------|---------|-------------|
|Dezembro | [Criar um aplicativo de demonstração (versão prévia)](search-create-app-portal.md) | Um assistente que gera um arquivo HTML para download com acesso de consulta (somente leitura) a um índice, destinado a uma ferramenta de validação e teste, em vez de um atalho para um aplicativo cliente completo.|
|Novembro | [Enriquecimento incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md) | Armazena em cache o processamento do conjunto de habilidades para reutilização futura.  |
|Novembro | [Habilidade de Extração de Documentos (versão prévia)](cognitive-search-skill-document-extraction.md) | Uma habilidade cognitiva usada para extrair o conteúdo de um arquivo de dentro de um conjunto de habilidades.|
|Novembro | [Habilidade de Tradução de Texto](cognitive-search-skill-text-translation.md) | Uma habilidade cognitiva usada durante a indexação que avalia e traduz um texto. Em disponibilidade geral.|
|Novembro | [Modelos do Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Modelo usado para visualizar o conteúdo do repositório de conhecimento |
|Novembro | [Azure Data Lake Storage Gen2 (versão prévia)](search-howto-index-azure-data-lake-storage.md), [API do Gremlin do Cosmos DB (versão prévia)](search-howto-index-cosmosdb.md) e [API do Cassandra do Cosmos DB (versão prévia)](search-howto-index-cosmosdb.md) | Novas fontes de dados do indexador em versão prévia pública. |
|Julho | [Suporte da nuvem do Azure Government](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Em disponibilidade geral.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nome do novo serviço

Em outubro de 2019, o Azure Search foi renomeado para **Azure Cognitive Search** para refletir o uso expandido (ainda opcional) de habilidades cognitivas e processamento de IA nas operações básicas. As versões de API, pacotes NuGet, namespaces e pontos de extremidade não são alteradas. As soluções de pesquisa novas e existentes não são afetadas pela alteração do nome do serviço.

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) para o Azure Cognitive Search podem ser encontrados no site do Azure.