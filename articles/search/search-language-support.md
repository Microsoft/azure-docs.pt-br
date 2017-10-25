---
title: "Vários idiomas do Azure Search | Microsoft Docs"
description: "A Pesquisa do Azure dá suporte a 56 idiomas, aproveitando os analisadores de linguagem da Lucene e a tecnologia Processamento de Linguagem Natural da Microsoft."
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Crie um índice para documentos em vários idiomas na Pesquisa do Azure
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Ampliar o poder dos analisadores de linguagem é tão fácil quanto definir uma propriedade em um campo pesquisável na definição de índice. Agora você pode realizar esta etapa no portal.

Veja abaixo as capturas de tela das folhas do Portal do Azure para a Pesquisa do Azure que permitem que os usuários definam um esquema de índice. Nesta folha, os usuários podem criar todos os campos e definir a propriedade do analisador de cada um deles.

> [!IMPORTANT]
> Você pode definir um analisador de linguagem apenas durante a definição de campo, como ao criar um novo índice do zero ou ao adicionar um novo campo a um índice existente. Lembre-se de especificar por completo todos os atributos, incluindo o analisador, ao criar o campo. Você não poderá editar os atributos ou alterar o tipo de analisador depois de salvar suas alterações.
>
>

## <a name="define-a-new-field-definition"></a>Definir uma nova definição de campo
1. Entre no [Portal do Azure](https://portal.azure.com) e abra a folha de serviço do seu serviço de pesquisa.
2. Clique em **Adicionar índice** na barra de comandos localizada na parte superior do painel do serviço para iniciar um novo índice, ou abra um índice existente para definir um analisador nos novos campos que você está adicionando a um índice existente.
3. A folha Campos é exibida, oferecendo opções para definir o esquema do índice, incluindo a guia Analisador, usada para escolher um analisador de linguagem.
4. Em Campos, inicie uma definição de campo fornecendo um nome, escolhendo o tipo de dados e definindo atributos para marcar o campo como texto completo pesquisável, recuperável nos resultados da pesquisa, usável em estruturas de navegação facetada, classificável e assim por diante.
5. Antes de passar para o próximo campo, abra a guia **Analisador** .

![][1]
*Para selecionar um analisador, clique na guia Analisador na folha Campos*

## <a name="choose-an-analyzer"></a>Escolha um analisador
1. Role para encontrar o campo que você está definindo.
2. Se ainda não tiver marcado o campo como pesquisável, clique na caixa de seleção agora para marcá-la como **Pesquisável**.
3. Clique na área do Analisador para exibir a lista de analisadores disponíveis.
4. Escolha o analisador que deseja usar.

![][2]
*Selecione um dos analisadores com suporte para cada campo*

Por padrão, todos os campos pesquisáveis usam o [analisador padrão da Lucene](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é independente de linguagem. Para exibir a lista completa de analisadores com suporte, veja [Suporte de idioma na Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Quando o analisador de linguagem for selecionado para um campo, ele será usado com cada solicitação de indexação e pesquisa para esse campo. Quando uma consulta é executada em vários campos usando analisadores diferentes, a consulta será processada de modo independente pelos analisadores certos para cada campo.

Muitos aplicativos web e móveis atendem usuários em todo o mundo usando diferentes idiomas. É possível definir um índice para um cenário como esse criando um campo para cada idioma com suporte.

![][3]
*Definição de índice com um campo de descrição para cada idioma com suporte*

Se o idioma do agente emissor de uma consulta for conhecido, uma solicitação de pesquisa pode ser definida como escopo para um campo específico usando o parâmetro de consulta **searchFields** . A seguinte consulta será emitida apenas com a descrição em polonês:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Você pode consultar o índice do portal, usando o **Search Explorer** para colar uma consulta semelhante à mostrado acima. O Search Explorer está disponível na barra de comandos na folha de serviço. Confira [Consultar seu Índice de Pesquisa do Azure no portal](search-explorer.md) para obter detalhes.

Às vezes, o idioma do agente emissor de uma consulta não é conhecido; nesse caso, a consulta pode ser emitida em todos os campos simultaneamente. Se necessário, a preferência de resultados em um determinado idioma pode ser definida usando os [perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx). No exemplo abaixo, as correspondências encontradas na descrição em inglês terão uma pontuação superior em relação às correspondências em polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Se você é um desenvolvedor do .NET, é importante lembrar que você pode configurar os analisadores de linguagem usando o [SDK do .NET da Pesquisa do Azure](http://www.nuget.org/packages/Microsoft.Azure.Search). A versão mais recente também inclui o suporte para os analisadores de linguagem da Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
