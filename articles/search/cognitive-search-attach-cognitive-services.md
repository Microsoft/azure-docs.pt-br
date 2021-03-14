---
title: Anexar serviços cognitivas a um conconhecimento
titleSuffix: Azure Cognitive Search
description: Saiba como anexar uma assinatura All-in-One de serviços cognitivas a um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577937"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anexar um recurso de serviços cognitivas a um conconhecimento no Azure Pesquisa Cognitiva

Ao configurar um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) no Azure pesquisa cognitiva, você pode enriquecer um número limitado de documentos gratuitamente. Para cargas de trabalho maiores e mais frequentes, você deve anexar um recurso de serviços cognitivas "tudo em um" faturável. Uma assinatura "All-in-One" faz referência a "serviços cognitivas" como a oferta, em vez de serviços individuais, com acesso concedido por meio de uma única chave de API.

Um recurso de serviços cognitivas "All-in-One" orienta as [habilidades predefinidas](cognitive-search-predefined-skills.md) que você pode incluir em um conceda de habilidades:

+ [Pesquisa Visual computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento óptico de caracteres (OCR)
+ [Análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para detecção de idioma, reconhecimento de entidade, análise de sentimentos e extração de frases-chave
+ [Tradução de texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Uma chave de serviços cognitivas "All-in-One" é opcional em uma definição de contratação. Quando o número de transações diárias abaixo de 20 por dia, o custo é absorvido. No entanto, quando as transações excedem esse número, uma chave de recurso válida é necessária para que o processamento continue.

Qualquer chave de recurso "All-in-One" é válida. Internamente, um serviço de pesquisa usará o recurso que está colocalizado na mesma região física, mesmo que a chave "tudo em um" seja para um recurso em uma região diferente. A página [disponibilidade do produto](https://azure.microsoft.com/global-infrastructure/services/?products=search) mostra a disponibilidade regional lado a lado.

> [!NOTE]
> Se você omitir habilidades predefinidas em um contratado, os serviços cognitivas não serão acessados, e você não será cobrado, mesmo que o contratador especifique uma chave.

## <a name="how-billing-works"></a>Como funciona a cobrança

+ O Azure Pesquisa Cognitiva usa a chave de recurso de serviços cognitivas que você fornece em um contextset para cobrar da imagem e do enriquecimento de texto. A execução de habilidades faturáveis é o [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ A extração de imagem é uma operação de Pesquisa Cognitiva do Azure que ocorre quando os documentos são rachados antes do enriquecimento. A extração de imagem é faturável. Para obter os preços de extração de imagem, consulte a [página de preços do Azure pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/).

+ A extração de texto também ocorre durante a frase de quebra de documento. Não é faturável.

+ As habilidades que não chamam serviços cognitivas, incluindo habilidades condicionais, de forma condicional, de mesclagem de texto e de divisão de texto, não são faturáveis.

## <a name="same-region-requirement"></a>Requisito de mesma região

Os serviços Pesquisa Cognitiva e cognitivas devem existir na mesma região física, conforme indicado na página de [disponibilidade do produto](https://azure.microsoft.com/global-infrastructure/services/?products=search) . A maioria das regiões que oferecem Pesquisa Cognitiva também oferece serviços cognitivas.

Se você tentar enriquecer o ia na região que não tem os dois serviços, verá esta mensagem: "a chave fornecida não é uma chave de tipo de Cognitivaservices válida para a região do seu serviço de pesquisa."

> [!NOTE]
> Algumas habilidades internas se baseiam em serviços cognitivas não regionais (por exemplo, a [habilidade de tradução de texto](cognitive-search-skill-text-translation.md)). Usar uma habilidade não regional significa que sua solicitação pode ser atendida em uma região diferente da região de Pesquisa Cognitiva do Azure. Para obter mais informações sobre serviços não regionais, consulte a página [produtos de serviços cognitivas por região](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Usar recursos gratuitos

Você pode usar uma opção de processamento gratuito e limitada para concluir o tutorial de enriquecimento de ia e os exercícios de início rápido.

Recursos gratuitos (aprimoramentos limitados) são restritos a 20 documentos por dia, por indexador. Você pode [redefinir o indexador](search-howto-run-reset-indexers.md) para redefinir o contador.

Se você estiver usando o assistente de **importação de dados** para enriquecimento de ia, encontrará as opções "anexar serviços cognitivas" na página **Adicionar enriquecimento de ia (opcional)** .

![Seção de serviços cognitivas de anexo expandida](./media/cognitive-search-attach-cognitive-services/attach1.png "Seção de serviços cognitivas de anexo expandida")

## <a name="use-billable-resources"></a>Usar recursos faturáveis

Para cargas de trabalho que criam mais de 20 aprimoramentos por dia, lembre-se de anexar um recurso de serviços cognitivas cobráveis. É recomendável que você sempre anexe um recurso de serviços de cognitiva Faturável, mesmo que você nunca pretenda chamar API de Serviços Cognitivos. A anexação de um recurso substitui o limite diário.

Você é cobrado apenas por habilidades que chamam o API de Serviços Cognitivos. Você não é cobrado por [habilidades personalizadas](cognitive-search-create-custom-skill-example.md)ou habilidades como [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md)e [modelador](cognitive-search-skill-shaper.md), que não são baseados em API.

Se você estiver usando o assistente de **importação de dados** , poderá configurar um recurso Faturável na página Adicionar o **enriquecimento de ia (opcional)** .

1. Expanda **anexar serviços cognitivas** e, em seguida, selecione **criar novo recurso de serviços cognitivas**. Uma nova guia é aberta para que você possa criar o recurso:

   ![Criar um recurso dos Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Criar um recurso dos Serviços Cognitivos")

1. Na lista **local** , selecione a mesma região que tem o serviço de pesquisa.

1. Na lista **tipo de preço** , selecione **S0** para obter a coleção All-in-One de recursos de serviços cognitivas, incluindo os recursos de visão e linguagem que retornam as habilidades internas fornecidas pelo pesquisa cognitiva do Azure.

   Para a camada S0, você pode encontrar taxas para cargas de trabalho específicas na [página de preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na lista **selecionar oferta** , verifique se **Serviços cognitivas** está selecionado.
   + Em recursos de **linguagem** , as taxas para **análise de texto Standard** se aplicam à indexação de ia.
   + Em recursos de **visão** , as taxas para **Pesquisa Visual computacional S1** se aplicam.

1. Selecione **criar** para provisionar o novo recurso de serviços cognitivas.

1. Retorne à guia anterior. Selecione **Atualizar** para mostrar o recurso serviços cognitivas e, em seguida, selecione o recurso:

   ![Selecione o recurso serviços cognitivas](./media/cognitive-search-attach-cognitive-services/attach2.png "Selecione o recurso serviços cognitivas")

1. Expanda a seção **Adicionar habilidades cognitivas** para selecionar as habilidades cognitivas específicas que você deseja executar em seus dados. Conclua o restante do assistente.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de habilidades existente a um recurso de Serviços Cognitivos

Se você tiver um conjunto de habilidades existente, poderá anexá-lo a um recurso de Serviços Cognitivos novo ou diferente.

1. Na página Visão geral do serviço de pesquisa, selecione **habilidades**:

   ![Guia habilidades](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Guia habilidades")

1. Selecione o nome do conconhecedor e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as alterações.

   ![Lista de recursos do skillset](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos do skillset")

   Lembre-se de que a opção **gratuito (aprimoramentos limitados)** limita-se a 20 documentos diariamente, e que você pode usar **criar novos recursos de serviços cognitivas** para provisionar um novo recurso faturável. Se você criar um novo recurso, selecione **Atualizar** para atualizar a lista de recursos dos Serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar Serviços Cognitivos de modo programático

Ao definir o conjunto de habilidades de forma programática, adicione uma seção `cognitiveServices` ao conjunto de habilidades. Nessa seção, inclua a chave do recurso de serviços cognitivas que você deseja associar ao skillset. Lembre-se de que o recurso deve estar na mesma região que o recurso de Pesquisa Cognitiva do Azure. Também inclua `@odata.type`e defina como `#Microsoft.Azure.Search.CognitiveServicesByKey`.

O exemplo a seguir mostra esse padrão. Observe a `cognitiveServices` seção no final da definição.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
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

## <a name="example-estimate-costs"></a>Exemplo: estimar custos

Para estimar os custos associados à indexação de pesquisa cognitiva, comece com uma ideia da aparência de um documento médio para que você possa executar alguns números. Por exemplo, você pode aproximar:

+ 1.000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (imagens de 6.000).
+ 3.000 caracteres por página.

Suponha um pipeline que consiste na quebra de documento de cada PDF, extração de imagem e texto, OCR (reconhecimento óptico de caracteres) de imagens e reconhecimento de entidades de organizações.

Os preços mostrados neste artigo são hipotéticos. Eles são usados para ilustrar o processo de estimativa. Seus custos podem ser menores. Para obter os preços reais das transações, consulte Confira [preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para decifração de documento com conteúdo de texto e imagem, a extração de texto atualmente é gratuita. Para imagens 6.000, suponha $1 para cada 1.000 imagens extraídas. Esse é um custo de $6 para esta etapa.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva do OCR usa o melhor algoritmo (DescribeText). Supondo um custo de US$ 2,50 por 1.000 imagens a serem analisadas, pagaríamos US$ 15,00 para essa etapa.

3. Para extração de entidade, você teria um total de três registros de texto por página. Cada registro tem 1.000 caracteres. Três registros de texto por página multiplicados por 6.000 páginas são iguais a 18.000 registros de texto. Supondo US$ 2,00 por 1.000 registros de texto, essa etapa custaria US$36,00.

Juntando tudo isso, você pagaria cerca de $57 para ingerir documentos PDF de 1.000 desse tipo com o conjunto de qualificações descrito.

## <a name="next-steps"></a>Próximas etapas

+ [Página de preços do Azure Pesquisa Cognitiva](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)