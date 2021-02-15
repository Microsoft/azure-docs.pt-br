---
title: Filtrar por idioma em um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Critérios de filtro para dar suporte à pesquisa em vários idiomas, escopo da execução da consulta em campos específicos do idioma.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 120c30803d1423dc8b1255c65703e84d5938f235
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002514"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Como filtrar por idioma no Azure Pesquisa Cognitiva 

Um requisito importante em um aplicativo de pesquisa multilíngue é a capacidade de pesquisar e recuperar os resultados no próprio idioma do usuário. No Azure Pesquisa Cognitiva, uma maneira de atender aos requisitos de idioma de um aplicativo multilíngue é criar uma série de campos dedicados ao armazenamento de cadeias de caracteres em um idioma específico e, em seguida, restringir a pesquisa de texto completo apenas a esses campos no momento da consulta.

Os parâmetros de consulta na solicitação são usados para definir o escopo da operação de pesquisa e, em seguida, para cortar os resultados de todos os campos que não fornecem conteúdo compatível com a experiência de pesquisa que você deseja entregar.

| Parâmetros | Finalidade |
|-----------|--------------|
| **searchFields** | Limita a pesquisa de texto completo à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos especificados. Por padrão, todos os campos recuperáveis são retornados. O parâmetro **$select** permite que você escolha quais deseja retornar. |

O sucesso dessa técnica depende da integridade do conteúdo do campo. O Azure Pesquisa Cognitiva não converte cadeias de caracteres nem executa a detecção de idioma. Cabe a você certificar-se de que esses campos contêm as cadeias de caracteres esperadas.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para o conteúdo em idiomas diferentes

No Azure Pesquisa Cognitiva, as consultas visam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas a um idioma em uma experiência de pesquisa única normalmente definem campos dedicados para armazenar os valores: um campo para cadeias de caracteres em inglês, um para francês e assim por diante. 

O exemplo a seguir é do [exemplo de espaço real](search-get-started-portal.md) que tem vários campos de cadeia de caracteres contendo conteúdo em idiomas diferentes. Observe as atribuições do analisador de idioma para os campos neste índice. Os campos que contêm cadeias de caracteres têm melhor desempenho na pesquisa de texto completo quando emparelhados com um analisador criado para lidar com as regras linguísticas do idioma de destino.

  ![Captura de tela mostrando o campo campos do exemplo de espaço real. Um grupo de campos é realçado para indicar como as atribuições do analisador de idioma correspondem aos idiomas dos campos realçados.](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para obter exemplos de código que mostram as definições de campo com analisadores de idiomas, consulte [Define an index (.NET)](./search-get-started-dotnet.md) (Definir um índice (.NET)) e [Define an index (REST)](./search-get-started-powershell.md) (Definir um índice (REST)).

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](./search-get-started-dotnet.md) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

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
> Embora não haja nenhum $filter argumento na consulta, esse caso de uso é altamente afiliado com conceitos de filtro e, portanto, é apresentado como um cenário de filtragem.

## <a name="see-also"></a>Confira também

+ [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
+ [Analisadores de linguagem](/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](/rest/api/searchservice/search-documents)