---
title: Conectar-se a pesquisa do Bing - aplicativos lógicos do Azure
description: Encontre notícias com APIs REST de Pesquisa do Bing e Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952495"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Encontre notícias com a Pesquisa do Bing e os Aplicativos Lógicos do Azure

Este artigo mostra como é possível encontrar notícias, vídeos e outros itens por meio da Pesquisa do Bing e a partir de um aplicativo lógico com o conector de Pesquisa do Bing. Dessa forma, é possível criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para processar resultados da pesquisa e disponibilizar esses itens para outras ações. 

Por exemplo, é possível encontrar itens de notícias com base em critérios de pesquisa e fazer com que o Twitter publique esses itens como tweets no feed do Twitter.

Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">referência do conector da Pesquisa do Bing</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md)

* Uma [chave de API de Pesquisa do Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), que fornece acesso do aplicativo lógico às APIs da Pesquisa do Bing

* O aplicativo lógico no qual você deseja acessar o Hub de Eventos. Para iniciar o aplicativo lógico com um gatilho de Pesquisa do Bing, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicionar um gatilho de Pesquisa do Bing

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "Pesquisa do Bing" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa esse gatilho: **Pesquisa do Bing - no novo artigo de notícias**

   ![Localizar gatilho de Pesquisa do Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se for solicitado a fornecer detalhes da conexão, [crie a conexão de Pesquisa do Bing agora](#create-connection).
Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, forneça critérios para retornar artigos de correspondência correspondentes da Pesquisa do Bing.

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Search Query | Sim | <*search-words*> | Insira os termos de pesquisa que você quer utilizar. |
   | Market | Sim | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas é possível selecionar outro valor. |
   | Safe Search | Sim | <*search-level*> | O nível do filtro para excluir conteúdo adulto. O padrão é "Moderado", mas você seleciona outro nível. |
   | Count | Não | <*results-count*> | Retornar o número de resultados especificado. O padrão é 20, mas é possível especificar outro valor. O número atual de resultados retornados pode ser menor que o número especificado. |
   | Offset | Não | <*skip-value*> | O número de resultados para ignorar antes de retornar os resultados |
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

2. No gatilho ou ação, escolha **Nova etapa** > **Adicionar uma ação**.

   Este exemplo usa esse gatilho:

   **Pesquisa do Bing - no novo artigo de notícias**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, escolha **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "Pesquisa do Bing" como seu filtro.
Na lista de ações, selecione a ação desejada.

   Este exemplo usa essa ação:

   **Pesquisa do Bing - notícias de lista por consulta**

   ![Localize a ação da Pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se for solicitado a fornecer detalhes da conexão, [crie a conexão de Pesquisa do Bing agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   Para este exemplo, forneça os critérios para retornar ao subconjunto dos resultados do gatilho.

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Search Query | Sim | <*search-expression*> | Insira uma expressão para consultar os resultados do gatilho. É possível selecionar os campos ou a lista de conteúdo dinâmico ou criar uma expressão com o construtor de expressões. |
   | Market | Sim | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas é possível selecionar outro valor. |
   | Safe Search | Sim | <*search-level*> | O nível do filtro para excluir conteúdo adulto. O padrão é "Moderado", mas você seleciona outro nível. |
   | Count | Não | <*results-count*> | Retornar o número de resultados especificado. O padrão é 20, mas é possível especificar outro valor. O número atual de resultados retornados pode ser menor que o número especificado. |
   | Offset | Não | <*skip-value*> | O número de resultados para ignorar antes de retornar os resultados |
   |||||

   Por exemplo, suponha que você queira aqueles resultados cuja categoria inclua a palavra "tech".

   1. Clique na caixa **Consulta de Pesquisa** para que a lista de conteúdo dinâmico seja exibida. 
   Na lista, escolha **Expressão** para que o construtor de expressões seja exibido. 

      ![Gatilho de Pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora, é possível iniciar a criação da expressão.

   2. Na lista funções, selecione a função **contains()**, que aparece na caixa de expressões. Clique em **Conteúdo dinâmico** para que a lista de campos seja exibida novamente, mas certifique-se de que o cursor permaneça dentro dos parênteses.

      ![Definir uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Na lista de campos, selecione **Categoria**, que converte em um parâmetro. 
   Adicione uma vírgula após o primeiro parâmetro e, após a vírgula, adicione esta palavra: `'tech'` 

      ![Selecionar um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando terminar, escolha **OK**.

      A expressão agora aparece na caixa **Consulta de Pesquisa** neste formato:

      ![Expressão concluída](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na exibição de código, essa expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Conectar a Pesquisa do Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando for solicitado a fornecer informações de conexão, forneça estes detalhes:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Conexão | Sim | <*connection-name*> | O nome a criar para a conexão |
   | Versão da API | Sim | <*API-version*> | Por padrão, a versão da API de Pesquisa do Bing é definida para a versão atual. É possível selecionar uma versão anterior, conforme necessário. |
   | Chave de API | Sim | <*API-key*> | A chave da API de Pesquisa do Bing obtida anteriormente. Se você não tiver uma chave, obtenha a [Chave de API agora](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Por exemplo:

   ![Criar conexão](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando terminar, escolha **Criar**.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente conhecido como Swagger) de arquivos, consulte o [página de referência do conector](/connectors/bingsearch/).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
