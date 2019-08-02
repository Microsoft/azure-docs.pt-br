---
title: Anexar um recurso dos Serviços Cognitivos a um conjunto de habilidades – Azure Search
description: Instruções para anexar uma assinatura do all-in-one de serviços Cognitivos para um pipeline de enriquecimento cognitivos no Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ffce8a2bd8a04e73acdeac037be0b10ba1a9a887
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672379"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Anexar um recurso dos Serviços Cognitivos a um conjunto de habilidades no Azure Search 

Unidade de algoritmos de inteligência Artificial a [pipelines de indexação cognitivas](cognitive-search-concept-intro.md) usado para aprimoramento de documento no Azure Search. Esses algoritmos são com base nos recursos de serviços Cognitivos do Azure, incluindo [computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento óptico de caracteres (OCR) e [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para o reconhecimento de entidade extração de frases-chave e outros aprimoramentos. Conforme usado pelo Azure Search para fins de aprimoramento de documento, os algoritmos são encapsulados dentro de um *habilidade*, colocado em um *conjunto de qualificações*e referenciado por uma *indexador* durante a indexação.

Você pode aprimorar gratuitamente um número limitado de documentos. Ou, você pode anexar a um recurso de serviços Cognitivos faturável para uma *conjunto de qualificações* para cargas de trabalho maiores e mais frequentes. Neste artigo, você aprenderá como anexar um recurso dos serviços Cognitivos faturável para enriquecer documentos durante o Azure Search [indexação](search-what-is-an-index.md).

> [!NOTE]
> Eventos passíveis de cobrança incluem chamadas para APIs de serviços Cognitivos e imagem extração como parte do estágio de decodificação de documentos no Azure Search. Não há nenhum custo para extração de texto de documentos ou para as habilidades que não chamam serviços Cognitivos.
>
> A execução de habilidades faturáveis é na [dos serviços Cognitivos pagamento medida que vá preços](https://azure.microsoft.com/pricing/details/cognitive-services/). Para obter os preços de extração de imagem, consulte o [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Requisito de mesma região

É necessário que o Azure Search e serviços Cognitivos do Azure existem na mesma região. Caso contrário, você receberá esta mensagem em tempo de execução: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Não há nenhuma maneira de mover um serviço entre regiões. Se você receber esse erro, você deve criar um novo recurso de serviços Cognitivos na mesma região do Azure Search.

## <a name="use-free-resources"></a>Usar recursos gratuitos

Você pode usar uma opção de processamento limitado e gratuito para concluir os exercícios do início rápido e o tutorial de pesquisa cognitiva.

Recursos gratuitos (aprimoramentos limitado) são restritos a 20 documentos por dia, por assinatura.

1. Abra o Assistente de importação de dados:

   ![Abra o Assistente de importação de dados](media/search-get-started-portal/import-data-cmd.png "abrir o Assistente de importação de dados")

1. Escolha uma fonte de dados e continuar **adicionar de pesquisa cognitiva (opcional)** . Para obter uma explicação passo a passo deste assistente, consulte [importação, índice e consulta usando as ferramentas de portal](search-get-started-portal.md).

1. Expandir **anexar serviços Cognitivos** e, em seguida, selecione **gratuito (aprimoramentos de limitado)** :

   ![Expandido da seção de anexar os serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/attach1.png "seção expandida anexar serviços Cognitivos")

1. Continuar para a próxima etapa, **aprimoramentos de adicionar**. Para obter uma descrição das habilidades disponíveis no portal, consulte [etapa 2: Adicionar habilidades cognitivas](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no início rápido de pesquisa cognitiva.

## <a name="use-billable-resources"></a>Usar recursos faturáveis

Para cargas de trabalho que cria os aprimoramentos de mais de 20 por dia, certifique-se de anexar a um recurso de serviços Cognitivos faturável. É recomendável que você sempre pode anexar um recurso dos serviços Cognitivos faturável, mesmo se você pretende nunca chamar APIs de serviços Cognitivos. Anexar um recurso substitui o limite diário.

Você será cobrado somente pelos habilidades que chamam as APIs de serviços Cognitivos. Você não será cobrado por [habilidades personalizadas](cognitive-search-create-custom-skill-example.md), ou, como as habilidades [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md), e [shaper](cognitive-search-skill-shaper.md), que não são baseadas em API.

1. Abra o Assistente de importação de dados, escolha uma fonte de dados e continuar **adicionar de pesquisa cognitiva (opcional)** .

1. Expandir **anexar serviços Cognitivos** e, em seguida, selecione **criar novo recurso de serviços Cognitivos**. Uma nova guia é aberta para que você possa criar o recurso:

   ![Criar um recurso de serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "criar um recurso de serviços Cognitivos")

1. No **local** , selecione a região onde se encontra o serviço Azure Search. Certifique-se de usar esta região por motivos de desempenho. Também usar esta região anula os encargos de largura de banda de saída entre regiões.

1. No **tipo de preço** lista, selecione **S0** para obter a coleção em um dos recursos de serviços Cognitivos, incluindo os recursos de visão e a linguagem que faça as habilidades predefinidas usadas pelo Azure Search.

   Para a camada S0, você pode encontrar as taxas para cargas de trabalho específicas na [página de preços dos serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + No **selecionar oferecem** lista, certifique-se **dos serviços Cognitivos** está selecionado.
   + Sob **linguagem** apresenta as taxas **padrão de análise de texto** se aplicam a indexação de inteligência Artificial.
   + Sob **Vision** apresenta as taxas **S1 de visão do computador** se aplicam.

1. Selecione **criar** para provisionar o novo recurso de serviços Cognitivos.

1. Retornar à guia anterior, que contém o Assistente de importação de dados. Selecione **Refresh** para mostrar o recurso de serviços Cognitivos e, em seguida, selecione o recurso:

   ![Selecione o recurso de serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "selecione o recurso de serviços Cognitivos")

1. Expanda o **aprimoramentos de adicionar** seção para selecionar as habilidades cognitivas específicas que você deseja executar em seus dados. Conclua o restante do assistente. Para obter uma descrição das habilidades disponíveis no portal, consulte [etapa 2: Adicionar habilidades cognitivas](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no início rápido de pesquisa cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de habilidades existente a um recurso de Serviços Cognitivos

Se você tiver um conjunto de habilidades existente, poderá anexá-lo a um recurso de Serviços Cognitivos novo ou diferente.

1. Sobre o **visão geral do serviço** página, selecione **conjuntos de habilidades**:

   ![Guia Conjuntos de Habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Guia Conjuntos de Habilidades")

1. Selecione o nome do conjunto de qualificações e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as alterações.

   ![Lista de recursos do conjunto de habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos do conjunto de habilidades")

   Lembre-se de que o **gratuito (aprimoramentos de limitado)** opção limita você a 20 documentos diariamente, e que você pode usar **criar novo recurso de serviços Cognitivos** para provisionar um novo recurso faturável. Se você criar um novo recurso, selecione **Atualizar** para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar Serviços Cognitivos de modo programático

Ao definir o conjunto de habilidades de forma programática, adicione uma seção `cognitiveServices` ao conjunto de habilidades. Nesta seção, inclua a chave do recurso dos serviços Cognitivos que você deseja associar o conjunto de habilidades. Lembre-se de que o recurso deve ser na mesma região que seu recurso de Azure Search. Também inclua `@odata.type`e defina como `#Microsoft.Azure.Search.CognitiveServicesByKey`.

O exemplo a seguir mostra esse padrão. Observe que o `cognitiveServices` seção no final da definição.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exemplo: Estimar custos

Para estimar os custos associados ao cognitiva indexação de pesquisa, comece com uma ideia de um documento médio semelhante ao seguinte para que você possa executar alguns números. Por exemplo, você pode aproximar:

+ 1\.000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (6.000 imagens).
+ 3\.000 caracteres por página.

Suponha que um pipeline que consiste de decodificação de documentos de cada extração PDF, imagem e texto, reconhecimento óptico de caracteres (OCR) de imagens e reconhecimento de entidade de organizações.

Os preços mostrados neste artigo são hipotéticos. Eles são usados para ilustrar o processo de estimativa. Seus custos pode ser inferiores. Para obter os preços reais das transações, consulte [preços dos serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para decifração de documento com conteúdo de texto e imagem, a extração de texto atualmente é gratuita. Para imagens de 6.000, suponha que US $1 para cada 1.000 imagens extraídas. Isso é um custo de US $6.00 para esta etapa.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva do OCR usa o melhor algoritmo (DescribeText). Supondo um custo de US$ 2,50 por 1.000 imagens a serem analisadas, pagaríamos US$ 15,00 para essa etapa.

3. Para extração de entidade, você teria um total de três registros de texto por página. Cada registro tem 1.000 caracteres. Três registros de texto por página multiplicado por páginas de 6.000 é igual a 18.000 registros de texto. Supondo US$ 2,00 por 1.000 registros de texto, essa etapa custaria US$36,00.

Juntando as peças, você pagaria cerca de US $57,00 a ingestão de 1.000 documentos em PDF deste tipo com o conjunto de qualificações descrito.

## <a name="next-steps"></a>Próximas etapas
+ [Página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
