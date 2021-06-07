---
title: O que é uma ontologia?
titleSuffix: Azure Digital Twins
description: Saiba mais sobre o ontologies do setor DTDL para modelagem em um determinado domínio
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034686"
---
# <a name="what-is-an-ontology"></a>O que é uma ontologia? 

O vocabulário de uma solução de gêmeos digital do Azure é definido usando [modelos](concepts-models.md), que descrevem os tipos de entidade que existem em seu ambiente.

Às vezes, quando sua solução está vinculada a um setor específico, pode ser mais fácil e mais eficaz começar com um conjunto de modelos para aquele setor que já existe, em vez de criar seu próprio conjunto de modelos do zero. Esses conjuntos de modelos preexistentes são chamados de **ontologies**. 

Em geral, um ontologia é um conjunto de modelos para um determinado domínio, como uma estrutura de construção, um sistema IoT, uma cidade inteligente, a grade de energia, o conteúdo da Web, etc. Os ontologies geralmente são usados como esquemas para grafos de conhecimento, pois podem habilitar:
* Harmonização de componentes de software, documentação, bibliotecas de consultas, etc.
* Investimento reduzido em modelagem conceitual e desenvolvimento de sistema
* Interoperabilidade de dados mais fácil em um nível semântico
* Práticas recomendadas de reutilização, em vez de começar do zero ou "reinventando a roda"

Este artigo explica por que e como usar o ontologies para seus modelos de gêmeos digitais do Azure, bem como quais ontologies e ferramentas para eles estão disponíveis hoje.

## <a name="using-ontologies-for-azure-digital-twins"></a>Usando o ontologies para o gêmeos digital do Azure

O ontologies fornece um ótimo ponto de partida para soluções de alta qualidade digital. Eles abrangem um conjunto de modelos e relações específicas de domínio entre entidades para criar, criar e analisar um gráfico de alta bits digital. Ontologies permitem que os desenvolvedores de soluções comecem uma solução de gêmeos digital a partir de um ponto de partida comprovado e se concentrem em resolver problemas de negócios. O ontologies fornecido pela Microsoft também foi projetado para ser facilmente extensível, para que você possa personalizá-los para sua solução. 

Além disso, o uso desses ontologies em suas soluções pode configurá-los para uma integração mais direta entre diferentes parceiros e fornecedores, pois o ontologies pode fornecer um vocabulário comum entre soluções.

Como os modelos no Azure digital gêmeos são representados na [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), o ontologies para uso com o gêmeos digital do Azure também é escrito em DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Estratégias para a integração do ontologies

Há três estratégias possíveis para a integração do ontologies padrão do setor com o DTDL. Você pode escolher aquela que funciona melhor para você, dependendo de suas necessidades:

| Estratégia | Descrição | Recursos |
| --- | --- | --- |
| **Adotar** | Você pode iniciar sua solução com um ontologia de DTDL de software livre criado com base em padrões de indústria amplamente adotados. Você pode usar esses conjuntos de modelos prontos para uso ou estendê-los com suas próprias adições para uma solução personalizada. | [*Conceitos: &nbsp; adotando &nbsp; &nbsp; ontologies padrão do setor*](concepts-ontologies-adopt.md)<br><br>[*Conceitos: &nbsp; estendendo &nbsp; ontologies*](concepts-ontologies-extend.md) |
| **Converter** | Se você já tiver modelos existentes representados em outro formato padrão, poderá convertê-los em DTDL para usá-los com o gêmeos digital do Azure. | [*Conceitos: &nbsp; convertendo &nbsp; ontologies*](concepts-ontologies-convert.md)<br><br>[*Conceitos: &nbsp; estendendo &nbsp; ontologies*](concepts-ontologies-extend.md) |
| **Autor** | Você sempre pode desenvolver seus próprios modelos de DTDL personalizados do zero, usando quaisquer padrões do setor aplicáveis como inspiração. | [*Conceitos: modelos de DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Usando estratégias de ontologia em um caminho de desenvolvimento de modelo

Não importa qual estratégia você escolher para integrar um ontologia no Azure digital gêmeos, você pode seguir o caminho completo abaixo para orientá-lo na criação e no carregamento de seus ontologia como modelos de DTDL.

1. Comece revisando e compreendendo a [modelagem do DTDL no Azure digital gêmeos](concepts-models.md).
1. Continue com a estratégia de integração ontologia escolhida acima: [**adote**](concepts-ontologies-adopt.md), [**converta**](concepts-ontologies-convert.md)ou [**crie**](concepts-models.md) seus modelos com base em seu ontologia.
    1. Se necessário, [estenda](concepts-ontologies-extend.md) seu ontologia para personalizá-lo para suas necessidades.
1. [Valide](how-to-parse-models.md) seus modelos para verificar se eles estão trabalhando em documentos DTDL.
1. Carregue seus modelos concluídos no Azure digital gêmeos, usando as [APIs](how-to-manage-model.md#upload-models) ou um exemplo como o [carregador de modelo de gêmeos digital do Azure](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Depois disso, você deve ser capaz de usar seus modelos em sua instância de gêmeos digital do Azure. 

Você pode visualizá-los com exemplos como o [Azure digital gêmeos Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) ou o [Visualizador de modelo do Azure digital gêmeos](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer), ou então passar a usá-los para criar [gêmeos digital](concepts-twins-graph.md).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre as estratégias de adoção, conversão e criação de ontologies:
* [*Conceitos: adotando ontologies padrão do setor*](concepts-ontologies-adopt.md)
* [*Conceitos: convertendo ontologies*](concepts-ontologies-convert.md)
* [*Como: gerenciar modelos de DTDL*](how-to-manage-model.md)

Ou então, saiba mais sobre como os modelos são usados para criar gêmeos digital: [*Concepts: digital gêmeos e o gráfico de entrelaçamento*](concepts-twins-graph.md).