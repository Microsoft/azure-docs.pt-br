---
title: Exemplos de REST
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código REST de demonstração do Azure Pesquisa Cognitiva que usam as APIs REST de pesquisa ou gerenciamento.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956453"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Exemplos de código REST para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos da API REST que demonstram a funcionalidade e o fluxo de trabalho de uma solução de Pesquisa Cognitiva do Azure. Esses exemplos usam as [**APIs REST de pesquisa**](/rest/api/searchservice).

O REST é a interface de programação definitiva para o Azure Pesquisa Cognitiva, e todas as operações que podem ser invocadas programaticamente estão disponíveis primeiro no REST e, em seguida, em SDKs. Por esse motivo, a maioria dos exemplos na documentação aproveita as APIs REST para demonstrar ou explicar conceitos importantes.

Exemplos REST normalmente são desenvolvidos e testados no postmaster, mas você pode usar qualquer cliente que ofereça suporte a chamadas HTTP:

+ Comece com [o início rápido: criar um índice de pesquisa cognitiva do Azure usando APIs REST](search-get-started-rest.md) para obter ajuda em chamadas http formular.
+ Experimente [a extensão Visual Studio Code para o pesquisa cognitiva do Azure](search-get-started-vs-code.md), atualmente em visualização, se você trabalhar em Visual Studio Code.

## <a name="doc-samples"></a>Exemplos de documento

Exemplos de código da equipe de Pesquisa Cognitiva demonstram recursos e fluxos de trabalho. Muitos desses exemplos são referenciados em tutoriais, guias de início rápido e artigos de instruções. Você pode encontrar esses exemplos em [**Azure-Samples/Azure-Search-postmaster-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) no github.

| Exemplos | Artigo |
|---------|---------|
| [Início rápido](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa usando APIs REST](search-get-started-rest.md). Este artigo aborda o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de exemplo. |
| [Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Código-fonte para [tutorial: Use REST e ia para gerar conteúdo pesquisável de BLOBs do Azure](cognitive-search-tutorial-blob.md). Este artigo mostra como criar um Skill que itera em BLOBs do Azure para extrair informações e inferir a estrutura.|
| [Depurar sessões](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Código-fonte para [tutorial: diagnosticar, reparar e confirmar alterações no seu Skill](cognitive-search-tutorial-debug-sessions.md). Este artigo mostra como usar uma sessão de depuração do Skills no portal do Azure. O REST é usado para criar os objetos usados durante a depuração.|
| [personalizados-analisadores](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Código-fonte para [tutorial: Crie um analisador personalizado para números de telefone](tutorial-create-custom-analyzer.md). Este artigo explica como usar os analisadores para preservar padrões e caracteres especiais em conteúdo pesquisável.|
| [armazenamento de conhecimento](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Código-fonte para [criar uma loja de conhecimento usando REST e postmaster](knowledge-store-create-rest.md). Este artigo explica as etapas necessárias para popular uma loja de conhecimento usada para fluxos de trabalho de mineração de conhecimento. |
| [projeções](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Código-fonte de [como formatar e exportar aprimoramentos](knowledge-store-projections-examples.md). Este artigo explica como especificar as estruturas de dados físicos em uma loja de conhecimento.|
| [índice-criptografado-BLOBs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Código-fonte para [saber como indexar BLOBs criptografados usando indexadores de BLOB e habilidades](search-howto-index-encrypted-blobs.md). Este artigo mostra como indexar documentos no armazenamento de BLOBs do Azure que foram criptografados anteriormente usando Azure Key Vault. |

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

Os exemplos a seguir também são publicados pela equipe de Pesquisa Cognitiva, mas não são referenciados na documentação do. Os arquivos Leiame associados fornecem instruções de uso.

| Exemplos | Descrição |
|---------|-------------|
| [Consulta-exemplos](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Coleções do postmaster demonstrando as várias técnicas de consulta, incluindo pesquisa difusa, pesquisa de RegEx e curinga, preenchimento automático e assim por diante. |