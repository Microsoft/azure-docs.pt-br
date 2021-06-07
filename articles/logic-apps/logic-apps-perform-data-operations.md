---
title: Executar operações em dados
description: Converter, gerenciar e manipular os formatos e saídas de dados nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84710364"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Executar operações de dados nos Aplicativos Lógicos do Azure

Este artigo mostra como você pode trabalhar com dados nos aplicativos lógicos adicionando ações para esses dados e mais:

* Crie tabelas a partir de matrizes.
* Crie matrizes a partir de outras matrizes com base em uma condição.
* Crie tokens amigáveis a partir de propriedades de objeto JSON (Notação de Objeto JavaScript) para poder usar facilmente essas propriedades no fluxo de trabalho.

Se você não encontrar a ação desejada aqui, tente navegar pelas várias funções de [manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que o aplicativo lógico do Azure fornece.

Essas tabelas resumem as operações de dados que você pode usar e estão organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição é exibida em ordem alfabética.

**Ações de matriz** 

Essas ações ajudam você a trabalhar com dados em matrizes.

| Ação | Descrição |
|--------|-------------|
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela CSV (valores separados por vírgula) a partir de uma matriz. |
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. |
| [**Filtrar matriz**](#filter-array-action) | Crie um subconjunto de matriz a partir de uma matriz com base no filtro especificado ou condição específica. |
| [**Em**](#join-action) | Crie uma cadeia de caracteres a partir de todos os itens em uma matriz e separe cada item com o caractere especificado. |
| [**Selecionar**](#select-action) | Crie uma matriz a partir de propriedades especificadas para todos os itens em uma matriz diferente. |
||| 

**Ações de JSON**

Essas ações ajudam você a trabalhar com dados em formato JSON (JavaScript Object Notation).

| Ação | Descrição |
|--------|-------------|
| [**Compor**](#compose-action) | Crie uma mensagem ou cadeia de caracteres a partir de várias entradas que podem ter diversos tipos de dados. Em seguida, é possível usar essa cadeia de caracteres como uma única entrada, em vez de inserir repetidamente as mesmas entradas. Por exemplo, você pode criar uma única mensagem JSON a partir de várias entradas. |
| [**Analisar JSON**](#parse-json-action) | Crie tokens de dados amigáveis para propriedades em conteúdo JSON para poder usar as propriedades nos aplicativos lógicos com mais facilidade. |
|||

Para criar transformações JSON mais complexas, consulte [Executar transformações JSON avançadas com modelos Liquid](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico em que você precisa da operação para trabalhar com dados

  Se você não estiver familiarizado com os aplicativos lógicos, examine [o que é o aplicativo lógico do Azure?](../logic-apps/logic-apps-overview.md) e o [início rápido: Crie seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Operações de dados estão disponíveis apenas como ações, portanto, para poder usar essas ações, inicie o aplicativo lógico com um gatilho e inclua quaisquer outras ações necessárias para criar as saídas que você quer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Ação para compor

Para construir uma única saída, como um objeto JSON de várias entradas, você pode usar a ação **compor** . As entradas podem ter vários tipos, como inteiros, boolianos, matrizes, objetos JSON e outro tipo nativo com suporte dos Aplicativos Lógicos do Azure, por exemplo, binário e XML. Em seguida, você pode usar a saída em ações que seguem após a ação **Redigir**. A ação **Redigir** também pode evitar que você insira repetidamente as mesmas entradas ao criar o fluxo de trabalho do aplicativo lógico.

Por exemplo, você pode construir uma mensagem JSON a partir de diversas variáveis, como variáveis de cadeia de caracteres que armazenam os nomes e sobrenomes das pessoas e uma variável de inteiro que armazena a idade das pessoas. Aqui, a ação **Redigir** aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para testar um exemplo, siga estas etapas usando o Designer do Aplicativo Lógico. Ou então, se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Redigir** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Redigir](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e várias ações **Inicializar variável**. Essas ações são configuradas para a criação de duas variáveis de cadeia de caracteres e uma variável de inteiro. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "compor"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. No aplicativo lógico em que você quer criar a saída, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "compor"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `compose` como o filtro. Na lista ações, selecione a ação **compor** .

   ![Selecionar ação "Redigir"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Na caixa **Entradas**, forneça as entradas desejadas para criar a saída.

   Neste exemplo, ao clicar dentro da caixa **Entradas**, a lista de conteúdo dinâmico é exibida para que você possa selecionar as variáveis criadas anteriormente:

   ![Selecionar entradas a serem usadas para a ação "compor"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está o exemplo concluído da ação **Redigir**: 

   ![Exemplo concluído para a ação "compor"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição de fluxo de trabalho subjacente, consulte a [Ação redigir](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Redigir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Redigir**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Redigir**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Redigir**, selecione **Saída**.

   Este exemplo usa a ação **enviar um email** e inclui os campos de **saída** no corpo e no assunto do email:

   ![Campos de "saída" para a ação "compor"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Redigir"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de tabela CSV

Para criar uma tabela de valores separados por vírgulas (CSV) que tenha as propriedades e valores de objetos JavaScript Object Notation (JSON) em uma matriz, use a ação **criar tabela CSV** . Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela CSV**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Criar tabela CSV** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. No aplicativo lógico em que você quer criar a tabela CSV, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `create csv table` como o filtro. Na lista ações, selecione a ação **criar tabela CSV** .

   ![Selecionar ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela CSV**.

   Aqui está o exemplo concluído da ação **Criar tabela CSV**: 

   ![Exemplo concluído para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="customize-table-format"></a>Personalizar formato de tabela

Por padrão, a propriedade **Columns** é definida para criar automaticamente as colunas da tabela com base nos itens da matriz. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **colunas** e selecione **personalizado**.

1. Na propriedade **header** , especifique o texto do cabeçalho personalizado para usar em vez disso.

1. Na propriedade **valor** , especifique o valor personalizado a ser usado em vez disso.

Para retornar valores da matriz, você pode usar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação **criar tabela CSV** . Em um `For_each` loop, você pode usar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você deseja colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para retornar apenas esses valores, siga estas etapas para trabalhar no modo de exibição de designer ou na exibição de código. Este é o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalhar no modo de exibição de designer

Na ação, mantenha a coluna de **cabeçalho** vazia. Em cada linha na coluna **valor** , desfaça referência a cada propriedade de matriz que você deseja. Cada linha sob **valor** retorna todos os valores para a propriedade de matriz especificada e torna-se uma coluna em sua tabela.

1. Em **valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista de conteúdo dinâmico, selecione **Expressão**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Desreferenciar "Description" para "criar tabela CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de matriz desejada. Quando terminar, sua ação será parecida com este exemplo:

   ![função "Item ()" em "criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Para resolver expressões em versões mais descritivas, alterne para o modo de exibição de código e de volta para o modo de exibição de designer e, em seguida, reabra a ação recolhida:

   A ação **criar tabela CSV** agora aparece como este exemplo:

   !["Criar tabela CSV"-expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar no modo de exibição de código

Na definição de JSON da ação, dentro da `columns` matriz, defina a `header` propriedade como uma cadeia de caracteres vazia. Para cada `value` propriedade, desfaça referência a cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **modo de exibição de código**.

1. No editor de código, na matriz da ação `columns` , adicione a propriedade Empty `header` e essa `value` expressão para cada coluna de valores de matriz que você deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Volte para o modo de exibição de designer e reabra a ação recolhida.

   A ação **criar tabela CSV** agora aparece como este exemplo, e as expressões foram resolvidas para versões mais descritivas:

   !["Criar tabela CSV"-expressões resolvidas e nenhum cabeçalho](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela CSV** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela CSV**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela CSV**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela CSV**, selecione **Saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar ação de tabela HTML

Para criar uma tabela HTML que tenha as propriedades e valores de objetos JavaScript Object Notation (JSON) em uma matriz, use a ação **criar tabela HTML** . Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela HTML**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Criar tabela HTML** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando aplicativo lógico de exemplo para "criar tabela HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. No aplicativo lógico em que você quer criar uma tabela CSV, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "criar tabela HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `create html table` como o filtro. Na lista ações, selecione a ação **criar tabela HTML** .

   ![Selecionar a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela HTML**.

   Aqui está o exemplo concluído da ação **Criar tabela HTML**:

   ![Exemplo concluído para "criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="customize-table-format"></a>Personalizar formato de tabela

Por padrão, a propriedade **Columns** é definida para criar automaticamente as colunas da tabela com base nos itens da matriz. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **colunas** e selecione **personalizado**.

1. Na propriedade **header** , especifique o texto do cabeçalho personalizado para usar em vez disso.

1. Na propriedade **valor** , especifique o valor personalizado a ser usado em vez disso.

Para retornar valores da matriz, você pode usar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação **criar tabela HTML** . Em um `For_each` loop, você pode usar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você deseja colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para retornar apenas esses valores, siga estas etapas para trabalhar no modo de exibição de designer ou na exibição de código. Este é o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalhar no modo de exibição de designer

Na ação, mantenha a coluna de **cabeçalho** vazia. Em cada linha na coluna **valor** , desfaça referência a cada propriedade de matriz que você deseja. Cada linha sob **valor** retorna todos os valores para a propriedade especificada e torna-se uma coluna em sua tabela.

1. Em **valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista de conteúdo dinâmico, selecione **Expressão**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Propriedade de desreferência na ação "criar tabela HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de matriz desejada. Quando terminar, sua ação será parecida com este exemplo:

   ![função "Item ()" em "criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Para resolver expressões em versões mais descritivas, alterne para o modo de exibição de código e de volta para o modo de exibição de designer e, em seguida, reabra a ação recolhida:

   A ação **criar tabela HTML** agora aparece como este exemplo:

   !["Criar tabela HTML"-expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar no modo de exibição de código

Na definição de JSON da ação, dentro da `columns` matriz, defina a `header` propriedade como uma cadeia de caracteres vazia. Para cada `value` propriedade, desfaça referência a cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **modo de exibição de código**.

1. No editor de código, na matriz da ação `columns` , adicione a propriedade Empty `header` e essa `value` expressão para cada coluna de valores de matriz que você deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Volte para o modo de exibição de designer e reabra a ação recolhida.

   A ação **criar tabela HTML** agora aparece como este exemplo, e as expressões foram resolvidas para versões mais descritivas:

   !["Criar tabela HTML"-expressões resolvidas e nenhum cabeçalho](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela HTML** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela HTML**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela HTML**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela HTML**, selecione **Saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para "criar tabela HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ao incluir a saída da tabela HTML em uma ação de e-mail, certifique-se de definir a propriedade **Is HTML** como **Sim** nas opções avançadas da ação de e-mail. Dessa forma, a ação de e-mail formata corretamente a tabela HTML.

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![Email com resultados de "criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação filtrar matriz

Para criar uma matriz menor que tenha itens, que atendam a critérios específicos, de uma matriz existente, use a ação **Filtrar matriz** . Em seguida, é possível usar a matriz filtrada em ações que seguem após a ação **Filtrar matriz**.

> [!NOTE]
> Qualquer texto de filtro que você usa nessa condição diferencia maiúsculas de minúsculas. Além disso, essa ação não pode alterar o formato ou os componentes dos itens na matriz. 
> 
> Para ações que usam a saída de matriz da ação **Filtrar matriz**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível.
> 
> Se você chamar um ponto de extremidade HTTP e receber uma resposta JSON, use a ação **analisar JSON** para processar a resposta JSON. 
> Caso contrário, a ação **Filtrar matriz** pode ler apenas o corpo da resposta e não a estrutura da carga JSON.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Filtrar matriz** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Filtrar matriz](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   > [!NOTE]
   > Embora este exemplo use uma matriz de inteiros simples, essa ação é especialmente útil para matrizes de objeto JSON em que é possível filtrar com base em propriedades e valores dos objetos.

   ![Iniciando aplicativo lógico de exemplo para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. No aplicativo lógico em que você quer criar a matriz filtrada, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `filter array` como o filtro. Na lista ações, selecione a ação **Filtrar matriz** .

   ![Selecionar a ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar o filtro.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Para a condição, especifique os itens da matriz para comparar, selecione o operador de comparação e o valor de comparação.

   Este exemplo usa a `item()` função para acessar cada item na matriz, enquanto a ação de **matriz de filtro** procura itens de matriz cujo valor é maior que um:

   ![Exemplo concluído para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação consultar](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Filtrar matriz** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Filtrar matriz**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Filtrar matriz**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for aberta, selecione **expressão**. Para obter a saída de matriz da ação **Filtrar matriz**, digite esta expressão que inclui o nome da ação **Filtrar matriz**:

   `@actionBody('Filter_array')`

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui as saídas da expressão **actionBody (' Filter_array ')** no corpo do email:

   ![Saídas de ação da ação "filtrar matriz"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Ação unir

Para criar uma cadeia de caracteres que tenha todos os itens de uma matriz e separar os itens com um caractere delimitador específico, use a ação de **junção** . Em seguida, é possível usar a cadeia de caracteres em ações que seguem após a ação **Unir**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Unir** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Unir](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. Esta ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando aplicativo lógico de exemplo para a ação "ingressar"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. No aplicativo lógico em que você quer criar a cadeia de caracteres a partir de uma matriz, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![SSelect "nova etapa" para a ação "ingressar"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `join` como o filtro. Na lista de ações, selecione esta ação: **Unir**

   ![Selecione a ação "ingressar"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Na caixa **De**, forneça a matriz que contém os itens que você quer unir como uma cadeia de caracteres.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:  

   ![Selecionar a saída de matriz para criar a cadeia de caracteres](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Na caixa **Unir com**, insira o caractere desejado para separar cada item da matriz. 

   Este exemplo usa dois-pontos (:) como separador.

   ![Fornecer o caractere separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação unir](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Unir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Unir**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Unir**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Unir**, selecione **Saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para a ação "junção"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Unir"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Ação analisar JSON

Para referenciar ou acessar propriedades no conteúdo JavaScript Object Notation (JSON), você pode criar campos amigáveis ou tokens para essas propriedades usando a ação **analisar JSON** . Dessa forma, é possível selecionar as propriedades na lista de conteúdo dinâmica ao especificar entradas para o aplicativo lógico. Para esta ação, é possível fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo de JSON de exemplo ou carga.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Analisar JSON** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Analisar JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando aplicativo lógico de exemplo para a ação "analisar JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. No aplicativo lógico em que você quer analisar o conteúdo JSON, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "analisar JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `parse json` como o filtro. Na lista ações, selecione a ação **analisar JSON** .

   ![Selecionar a ação "Analisar JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Na caixa **Conteúdo**, forneça o conteúdo JSON que você quer analisar.

   Neste exemplo, ao clicar dentro da caixa **Conteúdo**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar o objeto JSON para ação Analisar JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Insira o esquema JSON que descreve o conteúdo do JSON que você está analisando.

   Neste exemplo, aqui está o esquema JSON:

   ![Fornecer o esquema JSON para o objeto JSON a ser analisado](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se você não tiver o esquema, é possível gerar esse esquema a partir do conteúdo JSON ou da *carga* que estiver analisando. 
   
   1. Na ação **Analisar JSON**, selecione a opção **Usar carga de amostra para gerar esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, forneça o conteúdo JSON e, em seguida, selecione **concluído**.

      ![Inserir o conteúdo do JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição no fluxo de trabalho subjacente, consulte [Ação analisar JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Analisar JSON** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Analisar JSON**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Analisar JSON**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, na ação **Analisar JSON**, será possível selecionar as propriedades do conteúdo JSON analisado.

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui os campos **FirstName**, **LastName** e **email** no corpo do email:

   ![Propriedades JSON na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Aqui está a ação de e-mail concluída:

   ![Exemplo concluído para ação de email](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "analisar JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Ação selecionar

Para criar uma matriz que tenha objetos JSON criados a partir de valores em uma matriz existente, use a ação **selecionar** . Por exemplo, é possível criar um objeto JSON para cada valor em uma matriz de inteiros, especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E embora seja possível alterar os componentes nesses objetos JSON, a matriz de saída sempre tem o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para ações que usam a saída de matriz da ação **Selecionar**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível. 

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Selecionar** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Selecionar](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "selecionar"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. No aplicativo lógico em que você quer criar a matriz, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "selecionar"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `select` como o filtro. Na lista ações, selecione a ação **selecionar** .

   ![Selecionar a ação "Selecionar"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Na caixa **De**, especifique a matriz de origem desejada.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a matriz de origem para a ação Selecionar](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Na coluna esquerda da caixa **Mapear**, forneça o nome de propriedade que você quer atribuir cada valor na matriz de origem. Na coluna direita, especifique uma expressão que representa o valor que você quer atribuir a propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada valor na matriz de inteiros usando a `item()` função em uma expressão que acessa cada item de matriz. 

   ![Especificar a propriedade e os valores do objeto JSON para criar a matriz](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação concluída:

   ![Exemplo concluído para a ação "selecionar"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte [Ação selecionar](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Selecionar** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Selecionar**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Selecionar**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for aberta, selecione **expressão**. Para obter a saída de matriz da ação **Selecionar**, insira esta expressão que inclui o nome da ação **Selecionar**:

   `@actionBody('Select')`

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui as saídas da `@actionBody('Select')` expressão no corpo do email:

   ![Saídas de ação da ação "selecionar"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "'Selecionar"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
