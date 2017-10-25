---
title: "Criar um índice (portal – Azure Search) | Microsoft Docs"
description: "Criar um índice usando o Portal do Azure."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Criar um índice do Azure Search usando o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Use o designer de índices interno no portal do Azure para criar um protótipo ou criar um [índice de pesquisa](search-what-is-an-index.md) para ser executado no serviço do Azure Search. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e um [serviço do Azure Search](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Encontrar o serviço de pesquisa
1. Entre na página do portal do Azure e examine os [serviços de pesquisa para sua assinatura](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o serviço Azure Search.

## <a name="name-the-index"></a>Nomear o índice

1. Clique no botão **Adicionar índice** na barra de comandos na parte superior da página.
2. Dê um nome ao seu índice do Azure Search 
   * Comece com uma letra.
   * Use apenas letras minúsculas, dígitos ou traços (“-”).
   * Limite o nome a 60 caracteres.

  O nome do índice se torna parte da URL do ponto de extremidade usada nas conexões com o índice e para enviar solicitações HTTP na API REST do Azure Search.

## <a name="define-the-fields-of-your-index"></a>Definir os campos do índice

A composição de índice inclui uma *coleção Campos* que define os dados pesquisáveis no índice. Mais especificamente, ela especifica a estrutura de documentos carregada separadamente. A coleção Campos inclui campos obrigatórios e opcionais, nomeados e digitados, com os atributos de índice para determinar como o campo pode ser usado.

1. Na folha **Adicionar Índice**, clique em **Campos >** para abrir a folha de definição de campo. 

2. Aceite o campo de *chave* gerado do tipo Edm.String. Por padrão, o campo é chamado *ID*, mas você pode renomeá-lo, desde que a cadeia de caracteres atenda às [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Um campo de chave é obrigatório para cada índice do Azure Search e deve ser uma cadeia de caracteres.

3. Adicione campos para especificar totalmente os documentos que serão carregados. Se os documentos consistirem em uma *ID*, um *nome do hotel*, um *endereço*, uma *cidade* e uma *região*, crie um campo correspondente para cada um no índice. Examine as [diretrizes de design na seção abaixo](#design) para obter ajuda na configuração de atributos.

4. Opcionalmente, adicione campos que são usados internamente em expressões de filtro. Os atributos no campo podem ser definidos para excluir campos das operações de pesquisa.

5. Quando terminar, clique em **OK** para salvar e criar o índice.

## <a name="tips-for-adding-fields"></a>Dicas para adicionar campos

A criação de um índice no portal faz uso intensivo do teclado. Minimize as etapas seguindo este fluxo de trabalho:

1. Primeiro, crie a lista de campo inserindo nomes e definindo tipos de dados.

2. Em seguida, use as caixas de seleção na parte superior de cada atributo para habilitar em massa a configuração de todos os campos e, depois, desmarque as caixas seletivamente para os poucos campos que não precisarão dela. Por exemplo, os campos de cadeia de caracteres normalmente são pesquisáveis. Dessa forma, você poderá clicar em **Recuperável** e **Pesquisável** para retornar os valores do campo nos resultados da pesquisa, bem como para permitir a pesquisa de texto completo no campo. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Diretrizes de design para configuração de atributos

Embora seja possível adicionar novos campos a qualquer momento, as definições de campo existentes são bloqueadas durante o tempo de vida do índice. Por esse motivo, os desenvolvedores geralmente usam o portal para criar índices simples, testar ideias ou usar as páginas do portal para pesquisar uma configuração. A iteração frequente em um design de índice será mais eficiente se você seguir uma abordagem baseada em código, de modo que você possa recriar o índice com facilidade.

Analisadores e sugestores são associados aos campos antes que o índice seja salvo. Lembre-se de clicar em cada página com guias para adicionar os analisadores ou sugestores de idioma à definição de índice.

Geralmente, os campos de cadeia de caracteres são marcados como **Pesquisáveis** e **Recuperáveis**.

Os campos usados para restringir os resultados da pesquisa incluem **Classificável**, **Filtrável** e **Com Faceta**.

Os atributos de campo determinam como um campo é usado, por exemplo, se ele é usado na pesquisa de texto completo, na navegação facetada, nas operações de classificação e assim por diante. A tabela a seguir descreve cada atributo.

|Atributo|Descrição|  
|---------------|-----------------|  
|**searchable**|Pesquisável com texto completo, sujeito à análise lexical como separação de palavras durante a indexação. Se você definir um campo pesquisável com um valor como “dia ensolarado”, internamente, ele será dividido nos tokens individuais “dia” e “ensolarado”. Para obter detalhes, consulte [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md).|  
|**filterable**|Referenciado nas consultas **$filter**. Campos filtráveis dos tipos `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como “dia ensolarado”, `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. |  
|**sortable**|Por padrão, o sistema classifica os resultados pela pontuação, mas você pode configurar a classificação com base nos campos dos documentos. Campos do tipo `Collection(Edm.String)` não podem ser **sortable**. |  
|**facetable**|Normalmente, usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de ocorrências por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Campos do tipo `Edm.String` que são **filterable**, **sortable** ou **facetable** podem ter um tamanho de, no máximo, 32 quilobytes. Para obter detalhes, consulte [Criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**chave**|Identificador exclusivo para documentos no índice. Exatamente um campo deve ser escolhido como o campo chave e deve ser do tipo `Edm.String`.|  
|**retrievable**|Determina se o campo pode ser retornado em um resultado da pesquisa. Isso é útil quando você deseja usar um campo (por exemplo, *margem de lucro*) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Criar o índice de hotéis usado nas seções de API de exemplo

A documentação da API do Azure Search inclui exemplos de código que apresentam um índice de *hotéis* simples. Nas capturas de tela abaixo, você pode ver a definição de índice, incluindo o analisador de idioma francês especificado durante a definição de índice, que pode ser recriado como um exercício no portal.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um índice do Azure Search, vá para a próxima etapa: [Carregar dados pesquisáveis no índice](search-what-is-data-import.md).

Como alternativa, você também poderá examinar os índices mais detalhadamente. Além da coleção Campos, um índice também especifica analisadores, sugestores, perfis de pontuação e configurações do CORS. O portal fornece páginas com guias para definir os elementos mais comuns: Campos, analisadores e sugestores. Para criar ou modificar outros elementos, use a API REST ou o SDK do .NET.

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço Search](https://docs.microsoft.com/rest/api/searchservice/) [SDK do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

