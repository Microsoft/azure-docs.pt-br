---
title: Transformar XML entre formatos
description: Criar transformações ou mapas que convertem dados XML entre formatos nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654125"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Criar mapas que transformam dados XML entre formatos nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

O conector de Transformação da integração corporativa converte dados de um formato para outro formato. Por exemplo, talvez você tenha uma mensagem de entrada contendo a data atual no formato AnoMêsDia. Você pode usar uma transformação para reformatar a data para o formato MêsDiaAno.

## <a name="what-does-a-transform-do"></a>O que uma transformação faz?
Uma Transformação, que também é conhecida como um mapa, é formada por um esquema XML de origem (entrada) e um esquema XML de destino (saída). Você pode usar funções internas diferentes para ajudar a manipular e controlar os dados, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores do tempo de data e até mesmo construções em loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Você pode criar uma transformação/mapa usando o [SDK do Enterprise Integration](https://aka.ms/vsmapsandschemas)do Visual Studio. Após a conclusão da criação e do teste da transformação, carregue a transformação em sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de carregar a transformação em sua conta de integração, você poderá usá-la para criar um Aplicativo lógico. O Aplicativo lógico executará suas transformações sempre que o Aplicativo lógico for acionado (e quando houver um conteúdo de entrada que exija transformação).

**Estas são as etapas para usar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos

* Criar uma conta de integração e adicionar um mapa a ela  

Agora que você cuidou dos pré-requisitos, é hora de criar seu Aplicativo lógico:  

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](./logic-apps-enterprise-integration-create-integration-account.md "Saiba como vincular uma conta de integração a um aplicativo lógico") que contém o mapa.
2. Adicione um gatilho de **Solicitação** a seu Aplicativo lógico  
   ![Captura de tela da lista suspensa "mostrar APIs gerenciadas da Microsoft" com o gatilho de solicitação selecionado. O menu suspenso está em um aplicativo lógico criado usando o SDK de integração do Visual Studio Enterprise.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Adicione a ação **Transformar XML** selecionando primeiro **Adicionar uma ação**   
   ![Captura de tela mostrando o botão "adicionar uma ação" selecionado no gatilho de solicitação.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Insira a palavra *transformação* na caixa de pesquisa para filtrar todas as ações para encontrar aquela que você deseja usar  
   ![Captura de tela mostrando como Pesquisar a ação XML de transformação na lista suspensa "mostrar APIs gerenciadas da Microsoft" para que ela possa ser adicionada ao gatilho de solicitação.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação **Transformar XML**   
6. Adicione o **CONTEÚDO** XML que você transformar. Você pode usar quaisquer dados XML recebidos na solicitação HTTP como o **CONTEÚDO**. Neste exemplo, selecione o corpo da solicitação HTTP que disparou o Aplicativo Lógico.

   > [!NOTE]
   > Verifique se o conteúdo para o **XML de Transformação** é XML. Se o conteúdo não estiver em XML ou codificado em base64, deve especificar uma expressão que processa o conteúdo. Por exemplo, pode usar [funções](logic-apps-workflow-definition-language.md#functions), como ```@base64ToBinary``` para descodificar conteúdo ou ```@xml``` para processar o conteúdo como XML.
 

7. Selecione o nome do **MAPA** que você deseja usar para executar a transformação. O mapa já deve estar em sua conta de integração. Em uma etapa anterior, você já deu ao seu Aplicativo lógico acesso à sua conta de integração que contém o mapa.      
   ![Captura de tela mostrando os campos conteúdo e mapear na captura XML de transformação para o gatilho de solicitação.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Salve seu trabalho   
    ![Captura de tela mostrando o botão salvar no designer de aplicativos lógicos.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Neste ponto, você já configurou seu mapa. Em um aplicativo real, convém armazenar os dados transformados em um aplicativo LOB, como o SalesForce. Você pode adicionar facilmente uma ação para enviar a saída da transformação para o Salesforce. 

Agora você pode testar a ação de transformação fazendo uma solicitação ao ponto de extremidade HTTP.  


## <a name="features-and-use-cases"></a>Recursos e casos de uso
* A transformação criada em um mapa pode ser simples, como copiar um nome e endereço de um documento para outro. Ou você pode criar transformações mais complexas usando as operações de mapa prontas para uso.  
* Várias operações ou funções de mapeamento estão disponíveis, incluindo cadeias de caracteres, funções de data e hora, e assim por diante.  
* Você pode fazer uma cópia de dados direta entre os esquemas. No Mapeador incluído no SDK, isso é tão simples quanto desenhar uma linha que conecta os elementos no esquema de origem aos seus correspondentes no esquema de destino.  
* Ao criar um mapa, você exibe uma representação gráfica do mapa, que mostra todas as relações e os links que você cria.
* Use o recurso Testar Mapa para adicionar uma mensagem XML de exemplo. Com um simples clique, você pode testar o mapa que você criou e ver a saída gerada.  
* Carregar mapas existentes  
* Inclui suporte para o formato XML.

## <a name="advanced-features"></a>Recursos avançados

### <a name="reference-assembly-or-custom-code-from-maps"></a>Assembly de referência ou código personalizado dos mapas 
A ação de transformação também dá suporte a mapas ou transformações com referência ao assembly externo. Esse recurso permite chamadas para o código .NET personalizado diretamente de mapas XSLT. Aqui estão os pré-requisitos para usar o assembly nos mapas.

* O mapa e o assembly referenciado do mapa, precisa ser [carregado na conta de integração](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapa e assembly devem ser carregados em uma ordem específica. Você deve carregar o assembly antes de carregar o mapa que referencia o assembly.

* O mapa também deve ter esses atributos e uma seção CDATA que contém a chamada para o código do assembly:

    * **nome** é o nome de assembly personalizado.
    * **namespace** é o namespace em seu assembly que inclui o código personalizado.

  Este exemplo mostra um mapa que faz referência a um assembly chamado "XslUtilitiesLib" e chama o `circumreference` método do assembly.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Marca de ordem de byte
Por padrão, a resposta da transformação iniciará com marca de ordem de byte (BOM). Você pode acessar essa funcionalidade apenas enquanto estiver trabalhando no editor de exibição de código. Para desabilitar essa funcionalidade, especifique `disableByteOrderMark` para a propriedade `transformOptions`:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas de integração corporativa")  