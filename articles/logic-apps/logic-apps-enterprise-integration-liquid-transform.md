---
title: Converter JSON e XML com modelos líquidos
description: Transformar JSON e XML usando modelos de Liquid como mapas em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992702"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Transformar JSON e XML usando modelos líquidos como mapas em aplicativos lógicos do Azure

Quando você quiser executar transformações de JSON básicas em seus aplicativos lógicos, poderá usar [operações de dados](../logic-apps/logic-apps-perform-data-operations.md) nativos, como **Compose** ou **Parse JSON**. Para transformações avançadas e complexas de JSON para JSON que têm elementos como iterações, fluxos de controle e variáveis, crie e use modelos que descrevam essas transformações usando a linguagem de modelo de código-fonte aberto [Liquid](https://shopify.github.io/liquid/) . Você também pode [executar outras transformações](#other-transformations), por exemplo, JSON para Text, XML to JSON e XML to Text.

Antes de executar uma transformação Liquid em seu aplicativo lógico, você deve primeiro criar um modelo líquido que defina o mapeamento desejado. Em seguida, você [carrega o modelo como um mapa](../logic-apps/logic-apps-enterprise-integration-maps.md) em sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Ao adicionar o **JSON de transformação à ação JSON-Liquid** ao seu aplicativo lógico, você pode selecionar o modelo Liquid como o mapa para a ação a ser usada.

Este artigo mostra como concluir essas tarefas:

* Crie um modelo líquido.
* Adicione o modelo à sua conta de integração.
* Adicione a ação de transformação Liquid ao seu aplicativo lógico.
* Selecione o modelo como o mapa que você deseja usar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Conhecimento básico sobre o [idioma do modelo líquido](https://shopify.github.io/liquid/)

  > [!NOTE]
  > A ação **transformar JSON em JSON-Liquid** segue a [implementação de DotLiquid para Liquid](https://github.com/dotliquid/dotliquid), que difere em casos específicos da [implementação de Shopify para Liquid](https://shopify.github.io/liquid). Para obter mais informações, consulte [Considerações sobre o modelo líquido](#liquid-template-considerations).

## <a name="create-the-template"></a>Criar o modelo

1. Crie o modelo Liquid que você usa como um mapa para a transformação JSON. Você pode usar qualquer ferramenta de edição que desejar.

   Para este exemplo, crie o modelo Liquid de exemplo, conforme descrito nesta seção:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Salve o modelo usando a `.liquid` extensão. Este exemplo usa o `SimpleJsonToJsonTemplate.liquid`.

## <a name="upload-the-template"></a>Carregar o modelo

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa de pesquisa portal do Azure, insira `integration accounts` e selecione **contas de integração**.

   ![Localizar "contas de integração"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Localize e selecione sua conta de integração.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. No painel **visão geral** , em **componentes**, selecione **mapas**.

    ![Selecione o bloco "mapas"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. No painel **mapas** , selecione **Adicionar** e forneça estes detalhes para o mapa:

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | `JsonToJsonTemplate` | O nome de seu mapa, que é "JsontoJsonTemplate" neste exemplo |
   | **Tipo de mapa** | **liquid** | O tipo do mapa. Para JSON para transformação de JSON, você deve selecionar **Liquid**. |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | Um arquivo de modelo ou mapa Liquid existente para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para localizar este arquivo, você pode usar o seletor de arquivos. Para limites de tamanho do mapa, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Adicionar modelo Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Adicionar a ação de transformação Liquid

1. No portal do Azure, siga estas etapas para [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. No Designer do Aplicativo Lógico, adicione o [Gatilho de solicitação](../connectors/connectors-native-reqres.md#add-request) ao seu aplicativo lógico.

1. No gatilho, escolha **Nova etapa**. Na caixa de pesquisa, insira `liquid` como seu filtro e selecione esta ação: **transformar JSON em JSON-Liquid**

   ![Localizar e selecionar a ação Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Abra a lista **mapa** e selecione seu modelo Liquid, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecionar mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, provavelmente seu aplicativo lógico não está vinculado à sua conta de integração. 
   Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu aplicativo lógica, selecione **Configurações de fluxo de trabalho**.

   1. Na lista **selecionar uma conta de integração** , selecione sua conta de integração e selecione **salvar**.

      ![Vincular o aplicativo lógico a uma conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Agora, adicione a propriedade **Content** a esta ação. Abra a lista **Adicionar novo parâmetro** e selecione **conteúdo**.

   ![Adicionar a propriedade "conteúdo" à ação](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Para definir o valor da propriedade de **conteúdo** , clique dentro da caixa de **conteúdo** para que a lista de conteúdo dinâmico seja exibida. Selecione o token de **corpo** , que representa a saída de conteúdo do corpo do gatilho.

   ![Selecione o token "corpo" para o valor da propriedade "conteúdo"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Após a conclusão, a ação será semelhante a este exemplo:

   ![Ação "transformar JSON em JSON" concluída](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Usando o [postmaster](https://www.getpostman.com/postman) ou uma ferramenta semelhante, poste a entrada JSON para seu aplicativo lógico. A saída JSON transformada do seu aplicativo lógico é parecida com este exemplo:

![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Considerações sobre o modelo líquido

* Os modelos líquidos seguem os [limites de tamanho de arquivo para mapas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) nos aplicativos lógicos do Azure.

* A ação **transformar JSON em JSON-Liquid** segue a [implementação de DotLiquid para Liquid](https://github.com/dotliquid/dotliquid). Essa implementação é uma porta para a .NET Framework da [implementação Shopify para Liquid](https://shopify.github.io/liquid/) e difere em [casos específicos](https://github.com/dotliquid/dotliquid/issues).

  Aqui estão as diferenças conhecidas:

  * A ação **transformar JSON em JSON-Liquid** gera nativamente uma cadeia de caracteres, que pode incluir JSON, XML, HTML e assim por diante. A ação Liquid indica apenas que a saída de texto esperada do modelo líquido é uma cadeia de caracteres JSON. A ação instrui o aplicativo lógico a analisar a entrada como um objeto JSON e aplica um wrapper para que Liquid possa interpretar a estrutura JSON. Após a transformação, a ação instrui seu aplicativo lógico a analisar a saída de texto de Liquid de volta para JSON.

    DotLiquid não entende nativamente o JSON, portanto, certifique-se de escapar o caractere de barra invertida ( `\` ) e quaisquer outros caracteres JSON reservados.

  * Se o modelo usar [filtros líquidos](https://shopify.github.io/liquid/basics/introduction/#filters), certifique-se de seguir as [convenções de nomenclatura DotLiquid e C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), que usam *maiúsculas e minúsculas da frase*. Para todas as transformações de Liquid, verifique se os nomes de filtro em seu modelo também usam maiúsculas e minúsculas. Caso contrário, os filtros não funcionarão.

    Por exemplo, quando você usa o `replace` filtro, use `Replace` , não `replace` . A mesma regra se aplicará se você experimentar exemplos em [DotLiquid online](http://dotliquidmarkup.org/try-online). Para obter mais informações, consulte [Shopify Liquid Filters](https://shopify.dev/docs/themes/liquid/reference/filters) e [DotLiquid Liquid Filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). A especificação Shopify inclui exemplos para cada filtro, portanto, para comparação, você pode experimentar esses exemplos em [DotLiquid-Experimente o online](http://dotliquidmarkup.org/try-online).

  * O `json` filtro dos filtros de extensão Shopify não está [implementado no momento no DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Normalmente, você pode usar esse filtro para preparar a saída de texto para a análise de cadeia de caracteres JSON, mas em vez disso, você precisará usar o `Replace` filtro.

  * O `Replace` filtro padrão na [implementação DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) usa a [correspondência de expressão regular (Regex)](/dotnet/standard/base-types/regular-expression-language-quick-reference), enquanto a [implementação de Shopify](https://shopify.github.io/liquid/filters/replace/) usa correspondência de [cadeia de caracteres simples](https://github.com/Shopify/liquid/issues/202). Ambas as implementações parecem funcionar da mesma maneira até que você use um caractere reservado RegEx ou um caractere de escape no parâmetro Match.

    Por exemplo, para escapar o caractere de escape de barra invertida reservada RegEx ( `\` ), use `| Replace: '\\', '\\'` e not `| Replace: '\', '\\'` . Esses exemplos mostram como o `Replace` filtro se comporta de forma diferente quando você tenta escapar o caractere de barra invertida. Embora essa versão funcione com êxito:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Com este resultado:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Esta versão falha:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Com este erro:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Para obter mais informações, consulte [Substituir filtro padrão usa correspondência de padrão de Regex...](https://github.com/dotliquid/dotliquid/issues/385).

  * O `Sort` filtro na [implementação de DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) classifica itens em uma matriz ou coleção por propriedade, mas com essas diferenças:<p>

    * Segue o [comportamento de sort_natural do Shopify](https://shopify.github.io/liquid/filters/sort_natural/), não o [comportamento de classificação de Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Classifica somente em ordem alfanumérica de cadeia de caracteres. Para obter mais informações, consulte [classificação numérica](https://github.com/Shopify/liquid/issues/980).

    * Usa ordem que não diferencia *maiúsculas* de minúsculas, não diferencia maiúsculas de minúsculas. Para obter mais informações, consulte [o filtro de classificação não segue o comportamento de maiúsculas e minúsculas da especificação do Shopify]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Outras transformações usando Liquid

Liquid não está limitado a apenas transformações JSON. Você também pode usar Liquid para executar outras transformações, por exemplo:

* [JSON em texto](#json-text)
* [XML para JSON](#xml-json)
* [XML para texto](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Transformar o JSON em texto

Este é o modelo líquido usado para este exemplo:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Aqui estão os exemplos de entradas e saídas:

![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Transformar XML em JSON

Este é o modelo líquido usado para este exemplo:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

O `JSONArrayFor` loop é um mecanismo de looping personalizado para a entrada XML para que você possa criar cargas JSON que evitem uma vírgula à direita. Além disso, a `where` condição para esse mecanismo de loop personalizado usa o nome do elemento XML para comparação, em vez do valor do elemento como outros filtros líquidos. Para obter mais informações, consulte aprofundar [-se na política do corpo de conjunto-coleções de coisas](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Aqui estão os exemplos de entradas e saídas:

![Exemplo de saída XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Transformar XML em texto

Este é o modelo líquido usado para este exemplo:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Aqui estão os exemplos de entradas e saídas:

![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Próximas etapas

* [Linguagem Liquid Shopify e exemplos](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-Experimente online](http://dotliquidmarkup.org/try-online)
* [GitHub DotLiquid](https://github.com/dotliquid/dotliquid)
* [Problemas do GitHub DotLiquid](https://github.com/dotliquid/dotliquid/issues/)
* Saiba mais sobre [mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
