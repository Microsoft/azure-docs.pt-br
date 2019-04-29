---
title: Executar operações em dados - Aplicativos Lógicos do Azure | Microsoft Docs
description: Converter, gerenciar e manipular os formatos e saídas de dados nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120172"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Executar operações de dados nos Aplicativos Lógicos do Azure

Este artigo mostra como você pode trabalhar com dados nos aplicativos lógicos adicionando ações para esses dados e mais:

* Crie tabelas a partir de matrizes.
* Crie matrizes a partir de outras matrizes com base em uma condição.
* Crie tokens amigáveis a partir de propriedades de objeto JSON (Notação de Objeto JavaScript) para poder usar facilmente essas propriedades no fluxo de trabalho.

Se você não encontrar a ação desejada aqui, tente navegar nas diversas [funções de manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que os Aplicativos Lógicos fornecem. 

Essas tabelas resumem as operações de dados que você pode usar e estão organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição é exibida em ordem alfabética.

**Ações de matriz** 

Essas ações ajudam você a trabalhar com dados em matrizes.

| Ação | DESCRIÇÃO | 
|--------|-------------| 
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela CSV (valores separados por vírgula) a partir de uma matriz. | 
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. | 
| [**Filtrar matriz**](#filter-array-action) | Crie um subconjunto de matriz a partir de uma matriz com base no filtro especificado ou condição específica. | 
| [**Unir**](#join-action) | Crie uma cadeia de caracteres a partir de todos os itens em uma matriz e separe cada item com o caractere especificado. | 
| [**Selecionar**](#select-action) | Crie uma matriz a partir de propriedades especificadas para todos os itens em uma matriz diferente. | 
||| 

**Ações de JSON**

Essas ações ajudam você a trabalhar com dados em formato JSON (JavaScript Object Notation).

| Ação | DESCRIÇÃO | 
|--------|-------------| 
| [**Redigir**](#compose-action) | Crie uma mensagem ou cadeia de caracteres a partir de várias entradas que podem ter diversos tipos de dados. Em seguida, é possível usar essa cadeia de caracteres como uma única entrada, em vez de inserir repetidamente as mesmas entradas. Por exemplo, você pode criar uma única mensagem JSON a partir de várias entradas. | 
| [**Analisar JSON**](#parse-json-action) | Crie tokens de dados amigáveis para propriedades em conteúdo JSON para poder usar as propriedades nos aplicativos lógicos com mais facilidade. | 
||| 

Para criar transformações JSON mais complexas, consulte [Executar transformações JSON avançadas com modelos Liquid](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os exemplos neste artigo, é necessário estes itens:

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* O aplicativo lógico em que você precisa da operação para trabalhar com dados 

  Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Operações de dados estão disponíveis apenas como ações, portanto, para poder usar essas ações, inicie o aplicativo lógico com um gatilho e inclua quaisquer outras ações necessárias para criar as saídas que você quer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Ação para compor

Para construir uma única saída, tal como um objeto JSON de várias entradas, é possível usar a ação **Operações de Dados - Redigir**. As entradas podem ter vários tipos, como inteiros, boolianos, matrizes, objetos JSON e outro tipo nativo com suporte dos Aplicativos Lógicos do Azure, por exemplo, binário e XML. Em seguida, você pode usar a saída em ações que seguem após a ação **Redigir**. A ação **Redigir** também pode evitar que você insira repetidamente as mesmas entradas ao criar o fluxo de trabalho do aplicativo lógico. 

Por exemplo, você pode construir uma mensagem JSON a partir de diversas variáveis, como variáveis de cadeia de caracteres que armazenam os nomes e sobrenomes das pessoas e uma variável de inteiro que armazena a idade das pessoas. Aqui, a ação **Redigir** aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para testar um exemplo, siga estas etapas usando o Designer do Aplicativo Lógico. Ou, se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **redigir** e **inicializar variável** definições de ação deste artigo em seu próprio aplicativo de lógica subjacente do fluxo de trabalho definição: [Exemplos de código de operação de dados - compor](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e várias ações **Inicializar variável**. 
   Essas ações são configuradas para a criação de duas variáveis de cadeia de caracteres e uma variável de inteiro. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. No aplicativo lógico em que você quer criar a saída, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira “redigir” como o filtro. Na lista de ações, selecione esta ação: **Compor**

   ![Selecionar ação "Redigir"](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. Na caixa **Entradas**, forneça as entradas desejadas para criar a saída. 

   Neste exemplo, ao clicar dentro da caixa **Entradas**, a lista de conteúdo dinâmico é exibida para que você possa selecionar as variáveis criadas anteriormente:

   ![Selecionar as entradas para redigir](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está o exemplo concluído da ação **Redigir**: 

   ![Ação “Redigir” concluída](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição de fluxo de trabalho subjacente, consulte a [Ação redigir](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Redigir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Redigir**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Redigir**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Redigir**, selecione **Saída**. 

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui os campos **Saída** no corpo e no assunto do email:

   ![Campos "Saída" na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Redigir"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de tabela CSV

Para criar uma tabela CSV (valores separados por vírgulas) que tenha as propriedades e valores de objetos JSON (JavaScript Object Notation) em uma matriz, use a ação **Operações de Dados - Criar tabela CSV**. Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela CSV**. 

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **criar tabela CSV** e **inicializar variável** definições de ação deste artigo em seu próprio aplicativo de lógica subjacente do fluxo de trabalho definição: [Exemplos de código de operação de dados - criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. 
   Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. No aplicativo lógico em que você quer criar a tabela CSV, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "criar tabela csv" como o filtro. Na lista de ações, selecione esta ação: **Criar tabela CSV**

   ![Selecionar ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela. 

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Aqui está o exemplo concluído da ação **Criar tabela CSV**: 

   ![Ação "Criar tabela CSV" concluída](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Por padrão, essa ação cria automaticamente as colunas com base nos itens de matriz. 
   Para criar manualmente os cabeçalhos de coluna e valores, escolha **Mostrar opções avançadas**. 
   Para fornecer apenas os valores personalizados, altere **Colunas** para **Personalizado**. 
   Para fornecer também os cabeçalhos de coluna personalizados, altere **Incluir cabeçalhos** para **Sim**. 

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela CSV**.

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela CSV** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela CSV**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela CSV**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela CSV**, selecione **Saída**. 

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui os campos **Saída** no corpo do e-mail:

   ![Campos "Saída" na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar ação de tabela HTML

Para criar uma tabela HTML que tenha as propriedades e valores de objetos JSON (JavaScript Object Notation) em uma matriz, use a ação **Operações de Dados - Criar tabela HTML**. Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela HTML**.

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **criar tabela HTML** e **inicializar variável** definições de ação deste artigo em seu próprio aplicativo de lógica subjacente do fluxo de trabalho definição: [Exemplos de código de operação de dados - criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. 
   Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. No aplicativo lógico em que você quer criar uma tabela CSV, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "criar tabela html" como o filtro. Na lista de ações, selecione esta ação: **Criar tabela HTML**

   ![Selecionar a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela. 

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Aqui está o exemplo concluído da ação **Criar tabela HTML**: 

   ![Ação "Criar tabela HTML" concluída](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Por padrão, essa ação cria automaticamente as colunas com base nos itens de matriz. 
   Para criar manualmente os cabeçalhos de coluna e valores, escolha **Mostrar opções avançadas**. 
   Para fornecer apenas os valores personalizados, altere **Colunas** para **Personalizado**. 
   Para fornecer também os cabeçalhos de coluna personalizados, altere **Incluir cabeçalhos** para **Sim**. 

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela HTML**.

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela HTML** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela HTML**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela HTML**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela HTML**, selecione **Saída**. 

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui os campos **Saída** no corpo do e-mail:

   ![Campos "Saída" na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Ao incluir a saída da tabela HTML em uma ação de e-mail, certifique-se de definir a propriedade **Is HTML** como **Sim** nas opções avançadas da ação de e-mail. Dessa forma, a ação de e-mail formata corretamente a tabela HTML.

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação filtrar matriz

Para criar uma matriz menor contendo itens que atendem a critérios específicos, a partir de uma matriz existente, use a ação **Operações de Dados - Filtrar matriz**. Em seguida, é possível usar a matriz filtrada em ações que seguem após a ação **Filtrar matriz**. 

> [!NOTE]
> Qualquer texto de filtro que você usa nessa condição diferencia maiúsculas de minúsculas. Além disso, essa ação não pode alterar o formato ou os componentes dos itens na matriz. 
> 
> Para ações que usam a saída de matriz da ação **Filtrar matriz**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível. 

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **filtrar matriz** e **inicializar variável** definições de ação deste artigo em seu próprio aplicativo de lógica subjacente do fluxo de trabalho definição: [Exemplos de código de operação de dados - filtrar matriz](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   > [!NOTE]
   > Embora este exemplo use uma matriz de inteiros simples, essa ação é especialmente útil para matrizes de objeto JSON em que é possível filtrar com base em propriedades e valores dos objetos.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. No aplicativo lógico em que você quer criar a matriz filtrada, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "filtrar matriz" como o filtro. Na lista de ações, selecione esta ação: **Matriz de filtro**

   ![Selecionar a ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar o filtro. 

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Para a condição, especifique os itens da matriz para comparar, selecione o operador de comparação e o valor de comparação.

   Este exemplo usa a função **item()** para acessar cada item na matriz enquanto que a ação **Filtrar matriz** pesquisa itens de matriz cujo valor seja maior que 1:
   
   ![Ação "Filtrar matriz" concluída](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação consultar](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Filtrar matriz** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Filtrar matriz**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Filtrar matriz**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for exibida, escolha **Expressão**. Para obter a saída de matriz da ação **Filtrar matriz**, digite esta expressão que inclui o nome da ação **Filtrar matriz**:

   ```
   @actionBody('Filter_array')
   ```

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui as saídas da expressão **actionBody('Filter_array')** no corpo do e-mail:

   ![Saídas de ação na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Ação unir

Para criar uma cadeia de caracteres contendo todos os itens de uma matriz e separar esses itens com um caractere delimitador específico, use a ação **Operações de Dados - Unir**. Em seguida, é possível usar a cadeia de caracteres em ações que seguem após a ação **Unir**.

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **ingressar** e **inicializar variável** definição de fluxo de trabalho subjacente do definições de ação deste artigo em seu próprio aplicativo lógico: [Exemplos de código de operação de dados - junção](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   Esta ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. 
   Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. No aplicativo lógico em que você quer criar a cadeia de caracteres a partir de uma matriz, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "unir" como o filtro. Na lista de ações, selecione esta ação: **Join**

   ![Selecionar a ação "Dados operações – Unir"](./media/logic-apps-perform-data-operations/select-join-action.png)

4. Na caixa **De**, forneça a matriz que contém os itens que você quer unir como uma cadeia de caracteres. 

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:  

   ![Selecionar a saída de matriz para criar a cadeia de caracteres](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. Na caixa **Unir com**, insira o caractere desejado para separar cada item da matriz. 

   Este exemplo usa dois-pontos (:) como separador.

   ![Fornecer o caractere separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação unir](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Unir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Unir**. 

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Unir**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Unir**, selecione **Saída**. 

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui os campos **Saída** no corpo do e-mail:

   ![Campos "Saída" na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Unir"](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Ação analisar JSON

Para referenciar ou acessar propriedades no conteúdo JSON (JavaScript Object Notation), é possível criar tokens ou campos amigáveis para essas propriedades usando a ação **Operações de dados - Analisar JSON**.
Dessa forma, é possível selecionar as propriedades na lista de conteúdo dinâmica ao especificar entradas para o aplicativo lógico. Para esta ação, é possível fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo de JSON de exemplo ou carga.

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **Parse JSON** e **inicializar variável** definição de fluxo de trabalho subjacente do definições de ação deste artigo em seu próprio aplicativo lógico : [Exemplos de código de operação de dados - Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   A ação está configurada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. 
   Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. No aplicativo lógico em que você quer analisar o conteúdo JSON, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "analisar json" como o filtro. Na lista de ações, selecione esta ação: **Analisar o JSON**

   ![Selecionar a ação "Analisar JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. Na caixa **Conteúdo**, forneça o conteúdo JSON que você quer analisar. 

   Neste exemplo, ao clicar dentro da caixa **Conteúdo**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar o objeto JSON para ação Analisar JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Insira o esquema JSON que descreve o conteúdo do JSON que você está analisando. 

   Neste exemplo, aqui está o esquema JSON:

   ![Fornecer o esquema JSON para o objeto JSON a ser analisado](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se você não tiver o esquema, é possível gerar esse esquema a partir do conteúdo JSON ou da *carga* que estiver analisando. 
   
   1. Na ação **Analisar JSON**, selecione a opção **Usar carga de amostra para gerar esquema**.

   2. Em **Inserir ou colar uma carga JSON de amostra**, forneça o conteúdo JSON e, em seguida, escolha **Concluído**.

      ![Inserir o conteúdo do JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição no fluxo de trabalho subjacente, consulte [Ação analisar JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Analisar JSON** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Analisar JSON**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Analisar JSON**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, na ação **Analisar JSON**, será possível selecionar as propriedades do conteúdo JSON analisado.

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui os campos **FirstName**, **LastName** e **Email** no corpo do e-mail:

   ![Propriedades JSON na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Aqui está a ação de e-mail concluída:

   ![Ação de e-mail concluída](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Unir"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Ação selecionar

Para criar uma matriz contendo objetos JSON criados a partir de valores em uma matriz existente, use a ação **Operações de dados - Selecionar**. Por exemplo, é possível criar um objeto JSON para cada valor em uma matriz de inteiros, especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E embora seja possível alterar os componentes nesses objetos JSON, a matriz de saída sempre tem o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para ações que usam a saída de matriz da ação **Selecionar**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível. 

Se você preferir trabalhar no editor de exibição de código, você pode copiar o exemplo **selecionar** e **inicializar variável** definição de fluxo de trabalho subjacente do definições de ação deste artigo em seu próprio aplicativo lógico: [Exemplos de código de operação de dados - selecionar](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. 
   A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. 
   Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Iniciar aplicativo lógico de exemplo](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. No aplicativo lógico em que você quer criar a matriz, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "selecionar" como o filtro. Na lista de ações, selecione esta ação: **Selecionar**

   ![Selecionar a ação "Selecionar"](./media/logic-apps-perform-data-operations/select-select-action.png)

4. Na caixa **De**, especifique a matriz de origem desejada.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a matriz de origem para a ação Selecionar](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. Na coluna esquerda da caixa **Mapear**, forneça o nome de propriedade que você quer atribuir cada valor na matriz de origem. Na coluna direita, especifique uma expressão que representa o valor que você quer atribuir a propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada valor na matriz de inteiros usando a função **item()** em uma expressão que acessa a cada item da matriz. 

   ![Especificar a propriedade do objeto JSON e os valores para a matriz que você quer criar](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação concluída:

   ![Ação Selecionar concluída](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte [Ação selecionar](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Selecionar** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Selecionar**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Selecionar**.

2. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for exibida, escolha **Expressão**. Para obter a saída de matriz da ação **Selecionar**, insira esta expressão que inclui o nome da ação **Selecionar**:

   ```
   @actionBody('Select')
   ```

   Este exemplo usa a ação **Office 365 Outlook – Enviar um e-mail** e inclui as saídas da expressão **actionBody('Select')** no corpo do e-mail:

   ![Saídas de ação na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do Designer, escolha **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "'Selecionar"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
