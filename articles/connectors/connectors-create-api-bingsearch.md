---
title: Conectar a Pesquisa do Bing
description: Automatizar tarefas e fluxos de trabalho que localizam resultados em Pesquisa do Bing usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 52bf42434640dc965999895549b4fa12a139dcce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999596"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Localizar resultados em Pesquisa do Bing usando os aplicativos lógicos do Azure

Este artigo mostra como é possível encontrar notícias, vídeos e outros itens por meio da Pesquisa do Bing e a partir de um aplicativo lógico com o conector de Pesquisa do Bing. Dessa forma, é possível criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para processar resultados da pesquisa e disponibilizar esses itens para outras ações. 

Por exemplo, é possível encontrar itens de notícias com base em critérios de pesquisa e fazer com que o Twitter publique esses itens como tweets no feed do Twitter.

Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a [referência do conector da Pesquisa do Bing](/connectors/bingsearch/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md)

* Uma [chave de API de Pesquisa do Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), que fornece acesso do aplicativo lógico às APIs da Pesquisa do Bing

* O aplicativo lógico no qual você deseja acessar o Hub de Eventos. Para iniciar o aplicativo lógico com um gatilho de Pesquisa do Bing, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicionar um gatilho de Pesquisa do Bing

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "Pesquisa do Bing" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa esse gatilho: **Pesquisa do Bing - No novo artigo de notícias**

   ![Localizar gatilho de Pesquisa do Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se for solicitado a fornecer detalhes da conexão, [crie a conexão de Pesquisa do Bing agora](#create-connection).
Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, forneça critérios para retornar artigos de correspondência correspondentes da Pesquisa do Bing.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de Pesquisa | Yes | <*Pesquisar-palavras*> | Insira os termos de pesquisa que você quer utilizar. |
   | Market | Yes | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas é possível selecionar outro valor. |
   | Salvar pesquisa | Yes | <*nível de pesquisa*> | O nível do filtro para excluir conteúdo adulto. O padrão é "Moderado", mas você seleciona outro nível. |
   | Contagem | Não | <*resultados-contagem*> | Retornar o número de resultados especificado. O padrão é 20, mas é possível especificar outro valor. O número atual de resultados retornados pode ser menor que o número especificado. |
   | Deslocamento | No | <*ignorar valor*> | O número de resultados para ignorar antes de retornar os resultados |
   |||||

   Por exemplo:

   ![Configurar gatilho](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecione o intervalo e a frequência de quantas vezes você quer que o gatilho verifique os resultados.

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

6. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Adicionar uma ação de Pesquisa do Bing

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico inicia com um gatilho de Pesquisa do Bing que retorna artigos de notícias que correspondem aos critérios especificados.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. No gatilho ou ação, selecione **nova etapa**  >  **Adicionar uma ação**.

   Este exemplo usa este gatilho:

   **Pesquisa do Bing-no novo artigo de notícias**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. 
   Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "Pesquisa do Bing" como seu filtro.
Na lista de ações, selecione a ação desejada.

   Este exemplo usa esta ação:

   **Pesquisa do Bing-listar notícias por consulta**

   ![Localize a ação da Pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se for solicitado a fornecer detalhes da conexão, [crie a conexão de Pesquisa do Bing agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   Para este exemplo, forneça os critérios para retornar ao subconjunto dos resultados do gatilho.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de Pesquisa | Yes | <*expressão de pesquisa*> | Insira uma expressão para consultar os resultados do gatilho. É possível selecionar os campos ou a lista de conteúdo dinâmico ou criar uma expressão com o construtor de expressões. |
   | Market | Yes | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas é possível selecionar outro valor. |
   | Salvar pesquisa | Yes | <*nível de pesquisa*> | O nível do filtro para excluir conteúdo adulto. O padrão é "Moderado", mas você seleciona outro nível. |
   | Contagem | Não | <*resultados-contagem*> | Retornar o número de resultados especificado. O padrão é 20, mas é possível especificar outro valor. O número atual de resultados retornados pode ser menor que o número especificado. |
   | Deslocamento | No | <*ignorar valor*> | O número de resultados para ignorar antes de retornar os resultados |
   |||||

   Por exemplo, suponha que você queira aqueles resultados cuja categoria inclua a palavra "tech".

   1. Clique na caixa **Consulta de Pesquisa** para que a lista de conteúdo dinâmico seja exibida. 
   Nessa lista, selecione **expressão** para que o construtor de expressões seja exibido. 

      ![Gatilho de Pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora, é possível iniciar a criação da expressão.

   2. Na lista funções, selecione a função **contains()**, que aparece na caixa de expressões. Clique em **Conteúdo dinâmico** para que a lista de campos seja exibida novamente, mas certifique-se de que o cursor permaneça dentro dos parênteses.

      ![Definir uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Na lista de campos, selecione **Categoria**, que converte em um parâmetro. 
   Adicione uma vírgula após o primeiro parâmetro e, após a vírgula, adicione esta palavra: `'tech'` 

      ![Selecionar um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando terminar, selecione **OK**.

      A expressão agora aparece na caixa **Consulta de Pesquisa** neste formato:

      ![Expressão concluída](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na exibição de código, essa expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Conectar a Pesquisa do Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando for solicitado a fornecer informações de conexão, forneça estes detalhes:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Conexão | Yes | <*nome da conexão*> | O nome a criar para a conexão |
   | Versão da API | Yes | <*Versão da API*> | Por padrão, a versão da API de Pesquisa do Bing é definida para a versão atual. É possível selecionar uma versão anterior, conforme necessário. |
   | Chave de API | Yes | <*Chave de API*> | A chave da API de Pesquisa do Bing obtida anteriormente. Se você não tiver uma chave, obtenha a [Chave de API agora](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Por exemplo:

   ![Criar conexão](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando terminar, selecione **Criar**.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/bingsearch/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

