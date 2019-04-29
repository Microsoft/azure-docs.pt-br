---
title: Converter dados JSON com transformações de Liquid – Aplicativos Lógicos do Azure | Microsoft Docs
description: Crie transformações ou mapas para transformações avançadas de JSON usando o Aplicativo Lógico do Azure e o modelo Liquid
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 5472a8ce2670a34174d6d39f0d90faca8a7002ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467398"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Realize transformações avançadas de JSON com modelos Liquid em Aplicativos Lógicos do Azure

Você pode executar transformações básicas de JSON em seus aplicativos lógicos com ações de operação de dados nativos como **Compor** ou **Analisar JSON**. Para executar transformações avançadas de JSON, você pode criar modelos ou mapas com [Liquid](https://shopify.github.io/liquid/), que é uma linguagem do modelo de código-fonte aberto para aplicativos Web flexíveis. Um modelo Liquid define como transformar a saída JSON e dá suporte a transformações JSON mais complexas, como iterações, fluxos de controle, variáveis e assim por diante. 

Antes de executar uma transformação Liquid no seu aplicativo lógico, primeiro você deve definir o JSON para mapeamento de JSON com um modelo de Liquid e armazenar esse mapa em sua conta de integração. Este artigo mostra como criar e usar esse modelo ou mapa Liquid. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Ou [inscreva-se para uma assinatura de Pagamento Conforme o Uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Um básico [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Conhecimento básico sobre a [Linguagem do modelo Liquid.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Criar um modelo ou mapa Liquid para sua conta de integração

1. Para este exemplo, crie o modelo Liquid de amostra descrito nesta etapa. O modelo de Liquid, você pode usar [Liquid filtra](https://shopify.github.io/liquid/basics/introduction/#filters), que usam [DotLiquid](https://dotliquidmarkup.org/) e C# convenções de nomenclatura. 

   > [!NOTE]
   > Verifique se os nomes de filtro usam *capitalização de frase* em seu modelo. Caso contrário, os filtros não funcionarão.

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

2. Entre no [Portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **Todos os recursos**. Na caixa de pesquisa, encontre e selecione sua conta de integração.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Em **Componentes**, selecione **Mapas**.

    ![Selecione mapas](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Escolha **Adicionar** e forneça esses detalhes para o mapa:

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------|
   | **Nome** | JsonToJsonTemplate | O nome de seu mapa, que é "JsontoJsonTemplate" neste exemplo | 
   | **Tipo de mapa** | **liquid** | O tipo do mapa. Para JSON para transformação de JSON, você deve selecionar **Liquid**. | 
   | **Map** | "SimpleJsonToJsonTemplate.liquid" | Um arquivo de modelo ou mapa Liquid existente para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para localizar este arquivo, você pode usar o seletor de arquivos. |
   ||| 

   ![Adicionar modelo de Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação Liquid para transformação de JSON

1. No portal do Azure, siga estas etapas para [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. No Designer do Aplicativo Lógico, adicione o [Gatilho de solicitação](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) ao seu aplicativo lógico.

3. No gatilho, escolha **Nova etapa**. 
   Na caixa de pesquisa, digite "liquid" como filtro e selecione esta ação: **Transformar JSON em JSON – Liquid**

   ![Localizar e selecionar a ação Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Clique dentro da caixa **Conteúdo** para que seja exibida a lista de conteúdo dinâmica e selecione o token **Corpo**.
  
   ![Selecione corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Na lista **Mapa**, selecione o modelo de Liquid, que é “JsonToJsonTemplate” neste exemplo.

   ![Selecionar mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, o aplicativo lógico muito provavelmente não está vinculado à sua conta de integração. 
   Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu aplicativo lógica, selecione **Configurações de fluxo de trabalho**.

   2. Na lista **Selecionar uma conta de integração**, selecione sua conta de integração e escolha **Salvar**.

      ![Vincular o aplicativo lógico a uma conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Poste a entrada JSON no seu aplicativo lógico do [Postman](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída JSON transformada do seu aplicativo lógico é parecida com este exemplo:
  
![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação de Liquid
O Liquid não está limitado a apenas transformações de JSON. Aqui estão outras ações de transformação disponíveis que usam o Liquid.

* Transformar o JSON em texto
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão as entradas e saídas de exemplo:
  
   ![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Aqui está o modelo Liquid usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão as entradas e saídas de exemplo:

   ![Exemplo de saída XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em texto
  
  Aqui está o modelo Liquid usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão as entradas e saídas de exemplo:

   ![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas da integração corporativa")  

