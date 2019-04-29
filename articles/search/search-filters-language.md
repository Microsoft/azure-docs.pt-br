---
title: Filtros de idioma para conteúdo em vários idiomas em um índice de pesquisa – Azure Search
description: Critérios de filtragem para dar suporte à pesquisa em vários idiomas, definindo o escopo da execução de consulta para campos específicos a um idioma.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 695fdfba1573ff97b05f8e8b50a05bef9dbf48de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289597"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Como filtrar por idioma no Azure Search 

Um requisito importante em um aplicativo de pesquisa multilíngue é a capacidade de pesquisar e recuperar os resultados no próprio idioma do usuário. No Azure Search, uma forma de atender aos requisitos de idioma de um aplicativo multilíngue é criar uma série de campos dedicados para armazenar cadeias de caracteres em um idioma específico e, em seguida, limitar a pesquisa de texto completo a apenas esses campos no momento da consulta.

Os parâmetros de consulta na solicitação são usados para definir o escopo da operação de pesquisa e, em seguida, para cortar os resultados de todos os campos que não fornecem conteúdo compatível com a experiência de pesquisa que você deseja entregar.

| parâmetros | Finalidade |
|-----------|--------------|
| **searchFields** | Limita a pesquisa de texto completo à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos especificados. Por padrão, todos os campos recuperáveis são retornados. O parâmetro **$select** permite que você escolha quais deseja retornar. |

O sucesso dessa técnica depende da integridade do conteúdo do campo. O Azure Search não converte cadeias de caracteres nem realiza a detecção de idioma. Cabe a você certificar-se de que esses campos contêm as cadeias de caracteres esperadas.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para o conteúdo em idiomas diferentes

No Azure Search, as consultas direcionam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas a um idioma em uma experiência de pesquisa única normalmente definem campos dedicados para armazenar os valores: um campo para cadeias de caracteres em inglês, um para francês e assim por diante. 

Em nossos exemplos, incluindo o [exemplo de imóveis](search-get-started-portal.md) mostrado abaixo, talvez você tenha visto definições de campo semelhantes à captura de tela a seguir. Observe como este exemplo mostra as atribuições de analisador de idioma para os campos neste índice. Os campos que contêm cadeias de caracteres têm melhor desempenho na pesquisa de texto completo quando emparelhados com um analisador criado para lidar com as regras linguísticas do idioma de destino.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para obter exemplos de código que mostram as definições de campo com analisadores de idiomas, consulte [Define an index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) (Definir um índice (.NET)) e [Define an index (REST)](search-create-index-rest-api.md) (Definir um índice (REST)).

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restringir a consulta e cortar os resultados

Os parâmetros na consulta são usados para limitar a pesquisa em campos específicos e, em seguida, cortar os resultados dos campos não úteis para seu cenário. Considerando uma meta de restringir a pesquisa para campos que contêm cadeias de caracteres em francês, você usaria **searchFields** para direcionar a consulta em campos que contêm cadeias de caracteres nesse idioma. 

Por padrão, uma pesquisa retorna todos os campos marcados como recuperáveis. Sendo assim, talvez convenha excluir campos que não estão em conformidade com a experiência de pesquisa específica a um idioma que você deseja fornecer. Especificamente, se você limitou a pesquisa a um campo com cadeias de caracteres em francês, você provavelmente deseja excluir os campos com cadeias de caracteres em inglês dos seus resultados. O uso do parâmetro de consulta **$select** oferece controle sobre quais campos são retornados para o aplicativo de chamada.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora não haja nenhum argumento $filter na consulta, esse caso de uso é altamente afiliado a conceitos de filtro, portanto, nós o apresentamos como um cenário de filtragem.

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Analisadores de linguagem](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)

