---
title: "Tutorial: Criar seu primeiro índice do Azure Search no portal | Microsoft Docs"
description: "No portal do Azure, use dados de exemplo predefinidos para gerar um índice. Explore a pesquisa de texto completo, filtros, facetas, pesquisa difusa, pesquisa geográfica e muito mais."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: heidist
ms.openlocfilehash: c49989058fdd98d623c5517060f725e5f7e436d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-create-your-first-azure-search-index-in-the-portal"></a>Tutorial: Criar seu primeiro índice do Azure Search no portal

No portal do Azure, comece com um conjunto de dados de exemplo predefinido para gerar um índice rapidamente usando o assistente **Importar dados**. Explore a pesquisa de texto completo, filtros, facetas, pesquisa difusa e pesquisa geográfica com o **Search Explorer**.  

Esta introdução sem código apresenta dados predefinidos para que você possa escrever consultas interessantes imediatamente. Embora as ferramentas de portal não sejam um bom substituto para o código, elas são úteis para essas tarefas:

+ Aprendizado prático com curva de aprendizagem mínima
+ Crie um protótipo de índice antes de escrever código em **Importar dados**
+ Teste consultas e sintaxe de analisador no **Search Explorer**
+ Exiba um índice existente publicado no serviço e verifique os atributos

**Tempo estimado:** cerca de 15 minutos, mas poderá ser maior se houver a necessidade de fazer inscrição na conta ou no serviço. 

Como alternativa, incremente usando uma [introdução baseada em código sobre a programação do Azure Search no .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que você tem uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e [o serviço Azure Search](search-create-service-portal.md). 

Se não quiser provisionar um serviço imediatamente, você pode assistir a uma demonstração de 6 minutos com as etapas neste tutorial que começa em aproximadamente três minutos neste [Vídeo de visão geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Localizar o serviço
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. Se você não fixar o bloco do serviço ao seu painel, poderá encontrar o serviço desta maneira: 
   
   * Na Barra de Navegação, clique em **Mais serviços** na parte inferior do painel de navegação à esquerda.
   * Na caixa de pesquisa, digite *pesquisa* para obter uma lista de serviços de pesquisa da sua assinatura. Seu serviço deve aparecer na lista. 

## <a name="check-for-space"></a>Verificar o espaço
Muitos clientes começam com o serviço gratuito. Essa versão é limitada a três índices, três fontes de dados e três indexadores. Verifique se há espaço para itens extras antes de começar. Este tutorial cria uma unidade de cada objeto. 

> [!TIP] 
> Os blocos no painel de serviço mostram quantos índices, indexadores e fontes de dados você já tem. O bloco Indexador mostra indicadores de sucesso e falha. Clique no bloco para exibir a contagem do indexador. 
>
> ![Blocos de indexadores e fontes de dados][1]
>

## <a name="create-index"></a> Criar um índice e carregar dados
Consultas de pesquisa são iteradas em um *índice* que contém os dados pesquisáveis, metadados e construções usados para otimizar certos comportamentos de pesquisa.

Para mantermos essa tarefa baseada no portal, utilizamos um conjunto de dados interno de exemplo que pode ser rastreado com um indexador via assistente **Importar dados**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Etapa 1: Iniciar o assistente Importar dados
1. No painel de serviço de Pesquisa do Azure, clique em **Importar dados** na barra de comandos para iniciar um assistente que cria e preenche um índice.
   
    ![Comando Importar de dados][2]

2. No assistente, clique em **Fonte de Dados** > **Exemplos** > **realestate-us-sample**. Essa fonte de dados é pré-configurada com um nome, um tipo e as informações de conexão. Depois de criada, ela se torna uma "fonte de dados existente" que pode ser reutilizada em outras operações de importação.

    ![Selecionar o conjunto de dados de exemplo][9]

3. Clique em **OK** para usá-lo.

#### <a name="step-2-define-the-index"></a>Etapa 2: definir o índice
A criação de um índice é geralmente manual e baseada em código, mas o assistente pode gerar um índice para qualquer fonte de dados que ele possa rastrear. No mínimo, um índice requer um nome e uma coleção de campos, com um campo marcado como a chave do documento para identificar cada documento de forma exclusiva.

Campos têm atributos e tipos de dados. As caixas de seleção na parte superior são *atributos de índice* que controlam como o campo é usado. 

* **Recuperável** significa que ele aparece na lista de resultados da pesquisa. Você pode marcar campos individuais como fora dos limites para os resultados de pesquisa ao desmarcar essa caixa de seleção, por exemplo, quando os campos forem usados somente em expressões de filtro. 
* **Filtrável**, **Classificável** e **Com faceta** determinam se um campo pode ser usado em um filtro, em uma classificação ou em uma estrutura de navegação com facetas. 
* **Pesquisável** significa que um campo é incluído na pesquisa de texto completo. As cadeias de caracteres são pesquisáveis. Campos numéricos e boolianos geralmente são marcados como não pesquisáveis. 

Por padrão, o assistente procura na fonte de dados identificadores exclusivos como base para o campo de chave. As cadeias de caracteres são atribuídas como recuperáveis e pesquisáveis. Os números inteiros são atribuídos como recuperáveis, pesquisáveis, classificáveis e facetáveis.

  ![Índice realestate gerado][3]

Clique em **OK** para criar o índice.

#### <a name="step-3-define-the-indexer"></a>Etapa 3: definir o indexador
Ainda no assistente **Importar dados**, clique em **Indexador** > **Nome**e digite um nome para o indexador. 

Esse objeto define um processo executável. Você poderá colocá-lo na agenda de recorrência, mas, por enquanto, use a opção padrão para executar o indexador imediatamente logo que clicar em **OK**.  

  ![indexador realestate][8]

## <a name="check-progress"></a>Verificar o andamento
Para monitorar a importação dos dados, volte para o painel do serviço, role para baixo e clique duas vezes no bloco **Indexadores** para abrir a lista de indexadores. Você deve ver o indexador recém-criado na lista, com status indicando "em andamento" ou sucesso, juntamente com o número de documentos indexados.

   ![Mensagem de andamento do indexador][4]

## <a name="query-index"></a> Consultar o índice
Agora você tem um índice de pesquisa que está pronto para consulta. **Gerenciador de pesquisa** é uma ferramenta de consulta interna do portal. Ele fornece uma caixa de pesquisa para que você possa verificar se os resultados da pesquisa são os esperados. 

> [!TIP]
> No [vídeo Visão geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138), as etapas a seguir são demonstradas em 6m08s no vídeo.
>

1. Clique em **Gerenciador de pesquisa** na barra de comandos.

   ![Comando Search Explorer][5]

2. Clique em **Alterar índice** na barra de comando para alternar para *realestate-us-sample*.

   ![Comandos de índice e API][6]

3. Clique em **Definir versão de API** na barra de comandos para ver quais APIs REST estão disponíveis. As APIs de Visualização dão acesso aos novos recursos que ainda não foram lançados de maneira geral. Para as consultas abaixo, use a versão disponível (2016-09-01), a menos que haja indicação em contrário. 

    > [!NOTE]
    > A [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) e a [biblioteca .NET](search-howto-dotnet-sdk.md#core-scenarios) são totalmente equivalentes, mas o **Search Explorer** só é capaz de lidar com chamadas REST. Ele aceita a sintaxe para a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e para o [analisador de consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), além de todos os parâmetros de pesquisa disponíveis nas operações [Pesquisar Documento](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Na barra de pesquisa, insira as cadeias de caracteres de consulta abaixo e clique em **Pesquisar**.

  ![Exemplo de consulta de pesquisa][7]

**`search=seattle`**

+ O parâmetro `search` é usado para inserir uma palavra-chave de pesquisa de texto completo, neste caso, retornando listagens no estado de King County, estado de Washington, contendo *Seattle* em qualquer campo pesquisável no documento. 

+ O **Search Explorer** retorna os resultados em JSON, que é detalhado e difícil de ler quando os documentos têm uma estrutura densa. Dependendo de seus documentos, você precisará escrever código que lide com os resultados da para extrair elementos importantes. 

+ Os documentos são compostos de todos os campos marcados como recuperáveis no índice. Para exibir os atributos de índice no portal, clique em *realestate-us-sample* no bloco **Índices**.

**`search=seattle&$count=true&$top=100`**

+ O símbolo `&` é usado para acrescentar os parâmetros de pesquisa, que podem ser especificados em qualquer ordem. 

+  O parâmetro `$count=true` retorna uma contagem da soma de todos os documentos retornados. Você pode verificar consultas de filtro monitorando alterações relatadas por `$count=true`. 

+ O `$top=100` retorna o 100 documentos com maior classificação do total. Por padrão, o Azure Search retorna as primeiras 50 melhores correspondências. Você pode aumentar ou diminuir a quantidade via `$top`.

**`search=*&facet=city&$top=2`**

+ `search=*` é uma pesquisa vazia. Pesquisas vazias pesquisam tudo. Um motivo de envio de uma consulta vazia é fazer a filtragem ou faceta no conjunto completo de documentos. Por exemplo, você deseja que uma estrutura de navegação de facetas contenha todas as cidades no índice.

+  `facet` retorna uma estrutura de navegação que você pode passar para um controle de interface do usuário. Ela retorna categorias e uma contagem. Nesse caso, categorias se baseiam na quantidade de cidades. Não há nenhuma agregação no Azure Search, mas você pode aproximar agregação com `facet`, que retorna uma contagem de documentos em cada categoria.

+ `$top=2` recupera dois documentos, ilustrando a que você pode usar `top` para reduzir ou aumentar os resultados.

**`search=seattle&facet=beds`**

+ Essa consulta é faceta para camas em uma pesquisa de texto para *Seattle*. `"beds"` pode ser especificado como uma faceta porque o campo é marcado como recuperável, filtrável e facetável no índice e valores que ele contém (numérico, 1 a 5) são adequados para categorizar as listagens em grupos (listagens com 3 quartos, 4 quartos). 

+ Somente campos filtráveis podem ser facetados. Somente os campos recuperáveis podem ser retornados nos resultados.

**`search=seattle&$filter=beds gt 3`**

+ O parâmetro `filter` retorna resultados que correspondem aos critérios fornecidos. Nesse caso, quartos maiores que 3. 

+ A sintaxe de filtro é uma construção de OData. Para saber mais, confira [Sintaxe de filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

**`search=granite countertops&highlight=description`**

+ Realce de ocorrências refere-se à formatação de texto correspondentes à palavra-chave, considerando que existam correspondências em um campo específico. Se o termo de pesquisa estiver escondido em uma descrição, você poderá adicionar o realce de ocorrências para facilitar a localização. Nesse caso, a frase formatada `"granite countertops"` é mais fácil de se ver no campo de descrição.

**`search=mice&highlight=description`**

+ A pesquisa de texto completo localiza formas com semântica semelhante. Nesse caso, os resultados da pesquisa contêm texto realçado para "rato", para residências que têm a infestação de ratos, em resposta a uma pesquisa de palavra-chave "ratos". Formas diferentes da mesma palavra podem aparecer nos resultados devido a análise linguística. 

+ O Azure Search dá suporte a 56 analisadores da Lucene e da Microsoft. O padrão usado pelo Azure Search é o analisador Lucene standard. 

**`search=samamish`**

+ Palavras com grafia incorreta, como 'samamish' para o planalto Sammamish na área de Seattle, não retornam correspondências em uma pesquisa típica. Para lidar com erros de ortografia, você pode usar a pesquisa difusa, descrita no exemplo a seguir.

**`search=samamish~&queryType=full`**

+ A pesquisa difusa é habilitada quando você especifica o símbolo `~` e usa o analisador de consulta completo, que interpreta e analisa corretamente a sintaxe `~`. 

+ A pesquisa difusa está disponível quando você escolhe o analisador de consulta completo, que ocorrerá ao definir `queryType=full`. Para saber mais sobre cenários de consulta habilitados pelo analisador de consulta completo, confira [Sintaxe de consulta Lucene no Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

+ Quando `queryType` for especificado, o analisador de consulta simples padrão será usado. O analisador de consulta simples é mais rápido, mas se você precisar de pesquisa difusa, expressões regulares, pesquisa por proximidade ou outros tipos de consulta avançada, será necessário obter a sintaxe completa. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ A pesquisa geográfica tem suporte pelo [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em um campo que contém coordenadas. A pesquisa geográfica é um tipo de filtro, especificado na [sintaxe do filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

+ O exemplo de consulta filtra os dados posicionais de todos os resultados e os resultados ficam a menos de 5 km de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar `$count`, você poderá ver quantos resultados são retornados quando se altera a distância ou as coordenadas. 

+ A pesquisa geográfica é útil se seu aplicativo de pesquisa tem um recurso de “Encontrar nas proximidades” ou usa a navegação de mapa. Entretanto, ela não é uma pesquisa de texto completo. Se você tiver requisitos de usuário para pesquisar em uma cidade ou país por nome, adicione campos que contêm nomes de cidade ou países, além de coordenadas.

## <a name="next-steps"></a>Próximas etapas

+ Modificar um objeto que você acabou de criar. Depois de executar o assistente uma vez, você pode voltar e exibir ou modificar componentes individuais: índice, indexador ou fonte de dados. Algumas edições, como a alteração do tipo de dados do campo, não são permitidas no índice, mas a maioria das propriedades e configurações pode ser modificada.

  Para exibir os componentes individuais, clique no bloco **Índice**, **Indexador** ou **Fontes de Dados** no painel para exibir uma lista dos objetos existentes. Para saber mais sobre as edições do índice que não exigem recriação, confira [Atualizar índice (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/update-index).

+ Experimente as ferramentas e etapas com outras fontes de dados. O conjunto de dados de exemplo, `realestate-us-sample`, é de um banco de dados SQL que pode ser rastreado pelo Azure Search. Além do Banco de Dados SQL do Azure, o Azure Search pode rastrear e inferir um índice de estruturas de dados simples no Armazenamento de Tabelas do Azure, no Armazenamento de Blobs, no SQL Server em uma VM do Azure e no Azure Cosmos DB. Todas essas fontes de dados têm suporte no assistente. No código, você pode preencher um índice facilmente usando um *indexador*.

+ Todas as outras fontes de dados que não são do indexador têm suporte por meio de um modelo push, em que seu código envia conjuntos de linhas novos e alterados em JSON para seu índice. Para saber mais, confira [Adicionar, atualizar ou excluir documentos no Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Para saber mais sobre outros recursos mencionados neste artigo, acesse estes links:

* [Visão geral dos indexadores](search-indexer-overview.md)
* [Criar índice (inclui uma explicação detalhada sobre os atributos de índice)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Gerenciador de Pesquisa](search-explorer.md)
* [Pesquisar documentos (inclui exemplos de sintaxe de consulta)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png