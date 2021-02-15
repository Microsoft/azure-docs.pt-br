---
title: Conceitos da loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Envie documentos enriquecidos ao Armazenamento do Azure, onde você pode exibir, reformatar e consumir documentos enriquecidos no Azure Cognitive Search e em outros aplicativos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924292"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Repositório de conhecimento no Azure Pesquisa Cognitiva

O repositório de conhecimento é um recurso do Azure Cognitive Search que persiste a saída de um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) para análise independente ou processamento downstream. Um *documento enriquecido* é a saída de um pipeline, criado com base no conteúdo que foi extraído, estruturado e analisado usando processos de IA. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Optar por criar uma loja de conhecimento permitirá que você preserve os documentos aprimorados. 

Se você já usou a habilidades cognitivas no passado, sabe que os *conjuntos de habilidades* fazem com que um documento passe por uma sequência de enriquecimentos. O resultado pode ser um índice de pesquisa ou projeções em uma loja de conhecimento. As duas saídas, índice de pesquisa e repositório de conhecimento, são estruturadas, armazenadas e usadas de maneiras muito diferentes, apesar de serem produtos do mesmo pipeline e derivadas das mesmas entradas.

Fisicamente, um repositório de conhecimento é um [Armazenamento do Azure](../storage/common/storage-account-overview.md), podendo ser o Armazenamento de Tabelas do Azure, o Armazenamento de Blobs do Azure ou ambos. Qualquer ferramenta ou processo que pode se conectar ao Armazenamento do Azure pode consumir o conteúdo de um repositório de conhecimento.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Diagrama do repositório de conhecimento no diagrama")

## <a name="benefits-of-knowledge-store"></a>Vantagens do repositório de dados de conhecimento

Um repositório de conhecimento fornece estrutura, contexto e conteúdo real, compilados de arquivos de dados não estruturados e semiestruturados como blobs, arquivos de imagem que passaram por análise ou mesmo dados estruturados reformatados. Em um [passo a passo](knowledge-store-create-rest.md), você pode ver em primeira mão como um documento JSON denso é particionado em subestruturas, reconstituído em novas estruturas e disponibilizado para processos downstream como machine learning e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de enriquecimento de IA pode produzir, o verdadeiro potencial do repositório de conhecimento é a capacidade de reformatar dados. Você pode começar com um conjunto de qualificações básicas e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que podem ser combinados em novas estruturas e consumidos em outros aplicativos além da Pesquisa Cognitiva do Azure.

Se enumeradas, as vantagens do repositório de dados de conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [ferramentas de relatórios e análise](#tools-and-apps) que não sejam de pesquisa. O Power BI com Power Query é uma opção interessante, mas qualquer ferramenta ou aplicativo que consiga se conectar ao Armazenamento do Azure poderá efetuar pull de um repositório de conhecimento criado.

+ Refinar um pipeline de indexação por IA durante as etapas de depuração e definições de conjuntos de qualificações. Um repositório de dados de conhecimento mostra o produto de uma definição de conjuntos de qualificações em um pipeline de indexação por IA. Você pode usar esses resultados para criar um conjunto de qualificações enriquecido já que pode ver exatamente como são os enriquecimentos. Você pode usar o [Gerenciador de Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) no Armazenamento do Azure para exibir o conteúdo de um repositório de conhecimento.

+ Reformatar os dados. A reformatação é codificada em conjuntos de qualificações. No entanto, a questão é que um conjunto de qualificações já pode oferecer essa capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) na Pesquisa Cognitiva do Azure foi estendida para acomodar essa tarefa. A reformatação permite definir uma projeção que se alinha com o uso pretendido dos dados enquanto preserva as relações.

> [!Note]
> Você não está familiarizado com o enriquecimento de IA nem com as habilidades cognitivas? A Pesquisa Cognitiva do Azure integra-se aos recursos de Visão de Computação e Idioma nos Serviços Cognitivos para extrair e enriquecer os dados de origem usando OCR (Reconhecimento Óptico de Caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, confira [Enriquecimento de IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Armazenamento físico


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


A expressão física de um repositório de conhecimento é articulada por meio do elemento `projections` de uma definição `knowledgeStore` em um conjunto de habilidades. A projeção define uma estrutura da saída de modo que ela corresponda ao uso pretendido por você.

As projeções podem ser articuladas como tabelas, objetos ou arquivos.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

O tipo de projeção que você especificar nesta estrutura determinará o tipo de armazenamento usado pelo repositório de conhecimento.

+ O Armazenamento de Tabela é usado quando você define `tables`. Defina uma projeção de tabela quando precisar de estruturas de relatório tabulares para entradas em ferramentas analíticas ou precisar exportar como quadros de dados para outros armazenamentos de dados. Você pode especificar vários `tables` para obter um subconjunto ou uma seção transversal de documentos enriquecidos. No mesmo grupo de projeção, as relações de tabela são preservadas para que você possa trabalhar com todas elas.

+ O Armazenamento de Blobs é usado quando você define `objects` ou `files`. A representação física de um `object` é uma estrutura de JSON hierárquica que representa um documento enriquecido. Uma `file` é uma imagem extraída de um documento, transferida intacta para o Armazenamento de Blobs.

Um único objeto de projeção contém um conjunto de `tables`, `objects`, `files` e, para muitos cenários, a criação de uma projeção pode ser suficiente. 

No entanto, é possível criar vários conjuntos de projeções de `table`-`object`-`file`. Faça isso se você desejar relações de dados diferentes. Dentro de um conjunto, os dados estão relacionados, supondo que essas relações existam e possam ser detectadas. Se você criar conjuntos adicionais, os documentos em cada grupo nunca estarão relacionados. Um exemplo de uso de vários grupos de projeção pode ser se você quiser que os mesmos dados sejam projetados para uso com seu sistema online e ele precisar ser representado de uma forma específica, mas você também quiser que esses dados sejam projetados para uso em um pipeline de ciência de dados que for representado de maneira diferente.

## <a name="requirements"></a>Requisitos 

O [Armazenamento do Azure](../storage/index.yml) é necessário. Ele fornece armazenamento físico. Você pode usar o Armazenamento de Blobs, o Armazenamento de Tabelas ou ambos. O Armazenamento de Blobs é usado para documentos enriquecidos intactos, geralmente quando a saída vai para processos de downstream. O armazenamento de tabela é para fatias de documentos enriquecidos, normalmente usados para análise e relatórios.

O [conjunto de habilidades](cognitive-search-working-with-skillsets.md) é necessário. Ele contém a definição de `knowledgeStore` e determina a estrutura e a composição de um documento enriquecido. Você não pode criar um repositório de conhecimento usando um conjunto de habilidades vazio. Um conjunto de habilidades precisa ter pelo menos uma habilidade.

Um [indexador](search-indexer-overview.md) é necessário. Um conjunto de habilidades é invocado por um indexador, que conduz a execução. Os indexadores vêm com um conjunto próprio de requisitos e atributos. Vários desses atributos têm um impacto direto sobre um repositório de conhecimento:

+ Os indexadores exigem uma [fonte de dados do Azure compatível](search-indexer-overview.md#supported-data-sources) (o pipeline que em última instância cria o repositório de conhecimento começa obtendo dados de uma fonte compatível no Azure). 

+ Os indexadores exigem um índice de pesquisa. Um indexador requer que você forneça um esquema de índice, mesmo que você nunca planeje usá-lo. Um índice mínimo tem um campo de cadeia de caracteres designado como a chave.

+ Os indexadores fornecem mapeamentos de campo opcionais, usados para fornecer o alias de um campo de origem para um campo de destino. Se um mapeamento de campo padrão precisar de modificação (para usar um nome ou tipo diferente), você poderá criar um [mapeamento de campo](search-indexer-field-mappings.md) em um indexador. Para a saída do repositório de conhecimento, o destino pode ser um campo em uma tabela ou objeto de blob.

+ Os indexadores têm agendamentos, e outras propriedades, como mecanismos de detecção de alteração fornecidos por várias fontes de dados, também podem ser aplicados a um repositório de conhecimento. Por exemplo, você pode [agendar](search-howto-schedule-indexers.md) enriquecimento em intervalos regulares para atualizar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar um repositório de conhecimento

Para criar a loja de conhecimento, use o portal ou a API REST ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

O assistente **Importar dados** inclui opções para criar um repositório de conhecimento. Para uma exploração inicial, [crie seu primeiro repositório de conhecimento em quatro etapas](knowledge-store-connect-power-bi.md).

1. Selecione uma fonte de dados compatível.

1. Especifique o enriquecimento: anexe um recurso, selecione habilidades e especifique uma repositório de conhecimento. 

1. Crie um esquema de índice. O assistente requer que você o faça e pode inferir um para você.

1. Execute o assistente. A extração, o enriquecimento e o armazenamento ocorrem nesta última etapa.

### <a name="use-create-skillset-rest-api"></a>Usar CREATE conskillr (API REST)]

Um `knowledgeStore` é definido em um [conjunto de habilidades](cognitive-search-working-with-skillsets.md) que, por sua vez, é invocado por um [indexador](search-indexer-overview.md). Durante o enriquecimento, o Azure Cognitive Search cria um espaço em sua conta do Armazenamento do Azure e projeta os documentos enriquecidos como blobs ou em tabelas, dependendo de sua configuração.

A API REST é um mecanismo pelo qual você pode criar uma loja de conhecimento programaticamente. Uma forma fácil de explorar é [criar seu primeiro repositório de conhecimento usando o Postman e a API REST](knowledge-store-create-rest.md).

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como se conectar com ferramentas e aplicativos

Após os enriquecimentos estarem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de Blobs ou de Tabelas do Azure poderá ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) para exibir o conteúdo e a estrutura do documento enriquecido. Considere isso como sua ferramenta de linha de base para visualizar o conteúdo do repositório de dados de conhecimento.

+ [Power BI](knowledge-store-connect-power-bi.md) para relatórios e análise. 

+ [Azure Data Factory](../data-factory/index.yml) para manipular mais.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Referência de API

A API REST versão `2020-06-30` fornece o repositório de conhecimento por meio de definições adicionais em conjuntos de habilidades. Além da referência, confira [Criar um repositório de conhecimento usando o Postman](knowledge-store-create-rest.md) para obter detalhes sobre como chamar as APIs.

+ [Criar Qualificable (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Atualizar o Qualificable (API-Version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Próximas etapas

O repositório de conhecimento oferece persistência de documentos enriquecidos, que é útil ao criar um conjunto de habilidades ou ao criar novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de Armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é [por meio do portal](knowledge-store-create-portal.md), mas você também pode usar o postmaster e a API REST, que é mais útil se você quiser obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar um repositório de conhecimento usando a REST e o Postman](knowledge-store-create-rest.md)

Para saber mais sobre projeções, as funcionalidades e como você [as define em um conjunto de habilidades](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções em um repositório de conhecimento](knowledge-store-projection-overview.md)

Para obter um tutorial que aborda conceitos de projeções avançadas como divisão, formatação embutida e relações, comece com [definir projeções em um repositório de conhecimento](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definir projeções em um repositório de conhecimento](knowledge-store-projections-examples.md)