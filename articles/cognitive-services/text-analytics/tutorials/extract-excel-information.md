---
title: Extrair informações no Excel usando a Análise de Texto e o Power Automate
titleSuffix: Azure Cognitive Services
description: Saiba como extrair o texto do Excel sem precisar escrever código, usando Análise de Texto e a automatização de energia.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359894"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrair informações no Excel usando a Análise de Texto e o Power Automate 

Neste tutorial, você criará um fluxo de automatização de energia para extrair texto em uma planilha do Excel sem precisar escrever código. 

Esse fluxo usará uma planilha de problemas relatados sobre um apartamento complexo e os classificará em duas categorias: encanamento e outros. Ele também extrairá os nomes e números de telefone dos locatários que os enviaram. Por fim, o fluxo acrescentará essas informações à planilha do Excel. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar a automatização de energia para criar um fluxo
> * Carregar dados do Excel do OneDrive for Business
> * Extrair texto do Excel e enviá-lo para o API de Análise de Texto 
> * Use as informações da API para atualizar uma planilha do Excel.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Microsoft Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/cognitive-services/) e [entre](https://portal.azure.com/).
- Um recurso Análise de Texto. Se você não tiver uma, poderá [criar uma na portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e usar a camada gratuita para concluir este tutorial.
- A [chave e o ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerado para você durante a inscrição.
- Uma planilha que contém problemas de locatário. Os dados de exemplo são fornecidos no GitHub
- Microsoft 365, com o OneDrive for Business.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Adicionar o arquivo do Excel ao OneDrive for Business

Baixe o arquivo do Excel de exemplo do [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Esse arquivo deve ser armazenado em sua conta do OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exemplos do arquivo do Excel.":::

Os problemas são relatados em texto não processado. Usaremos o reconhecimento de entidade nomeada do API de Análise de Texto para extrair o nome da pessoa e o número de telefone. Em seguida, o fluxo procurará a palavra "encanamento" na descrição para categorizar os problemas. 

## <a name="create-a-new-power-automate-workflow"></a>Criar um novo fluxo de trabalho de automação de energia

Vá para o [site de energia automatizada](https://preview.flow.microsoft.com/)e faça logon. Em seguida, clique em **criar** e **agendar fluxo**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="A tela de criação de fluxo.":::


Na página **criar um fluxo agendado** , inicialize o fluxo com os seguintes campos:

|Campo |Valor  |
|---------|---------|
|**Nome do fluxo**     | **Revisão agendada** ou outro nome.         |
|**Iniciando**     |  Insira a data e a hora atuais.       |
|**Repetir a cada**     | **1 hora**        |

## <a name="add-variables-to-the-flow"></a>Adicionar variáveis ao fluxo

> [!NOTE]
> Se você quiser ver uma imagem do fluxo concluído, você pode baixá-lo do [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Crie variáveis que representem as informações que serão adicionadas ao arquivo do Excel. Clique em **nova etapa** e procure a **variável de inicialização**. Faça isso quatro vezes para criar quatro variáveis.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicialize as variáveis.":::

Adicione as informações a seguir às variáveis que você criou. Elas representam as colunas do arquivo do Excel. Se alguma variável for recolhida, você poderá clicar nelas para expandi-las.

| Ação |Nome   | Type | Valor |
|---------|---------|---|---|
| Inicializar variável | var_person | String | Pessoa |
| Inicializar variável 2 | var_phone | String | Phone_Number |
| Inicializar variável 3 | var_plumbing | String | detalhes técnicos |
| Inicializar a variável 4 | var_other | String | outros | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informações contidas nas variáveis de fluxo":::

## <a name="read-the-excel-file"></a>Ler o arquivo do Excel

Clique em **nova etapa** e digite **Excel** e, em seguida, selecione **listar linhas presentes em uma tabela** na lista de ações.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="adicionar linhas do Excel.":::

Adicione o arquivo do Excel ao fluxo preenchendo os campos nesta ação. Este tutorial requer que o arquivo tenha sido carregado no OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="preencher linhas do Excel":::

Clique em **nova etapa** e adicione uma **aplicação a cada** ação.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Adicione um comando Apply.":::

Clique em **selecionar uma saída da etapa anterior**. Na caixa conteúdo dinâmico que aparece, selecione **valor**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecione saída do arquivo do Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Enviar uma solicitação para o API de Análise de Texto

Se ainda não tiver feito isso, você precisará criar um [recurso de análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal do Azure.

### <a name="create-a-text-analytics-connection"></a>Criar uma conexão Análise de Texto

Em **aplicar a cada**, clique em **Adicionar uma ação**. Vá para a página de Análise de Texto do recurso **e o ponto de extremidade** no portal do Azure e obtenha a chave e o ponto de extremidade para o recurso de análise de texto.

Em seu fluxo, insira as informações a seguir para criar uma nova conexão de Análise de Texto.

> [!NOTE]
> Se você já tiver criado uma conexão Análise de Texto e desejar alterar os detalhes da conexão, clique nas reticências no canto superior direito e clique em **+ Adicionar nova conexão**.

| Campo           | Valor                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nome da Conexão | Um nome para a conexão com o recurso de Análise de Texto. Por exemplo, `TAforPowerAutomate`. |
| Chave de conta     | A chave para o recurso de Análise de Texto.                                                                                   |
| URL do site        | O ponto de extremidade para seu recurso de Análise de Texto.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo.":::

## <a name="extract-the-excel-content"></a>Extrair o conteúdo do Excel 

Depois que a conexão for criada, procure **análise de texto** e selecione **entidades**. Isso irá extrair informações da coluna Descrição do problema.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Adicionar entidades de Análise de Texto.":::

Clique no campo de **texto** e selecione **Descrição** nas janelas de conteúdo dinâmico que aparecem. Insira `en` para o idioma. (Clique em mostrar opções avançadas se você não vir o idioma)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Adicionar configurações de Análise de Texto.":::


## <a name="extract-the-person-name"></a>Extrair o nome da pessoa

Em seguida, localizaremos o tipo de entidade Person na saída Análise de Texto. Em **aplicar a cada**, clique em **Adicionar uma ação** e crie outra **se aplicar a cada** ação. Clique dentro da caixa de texto e selecione **entidades** na janela de conteúdo dinâmico que aparece.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 2":::

Dentro do recém-criado **aplica-se a cada 2** ação, clique em **Adicionar uma ação** e adicione um controle de **condição** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. Beta":::

Na janela condição, clique na primeira caixa de texto. Na janela de conteúdo dinâmico, pesquise **tipo de entidades** e selecione-o.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. quatro":::

Verifique se a segunda caixa está definida como **sendo igual a**. Em seguida, selecione a terceira caixa e procure `var_person` na janela de conteúdo dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 05":::

Na condição **se sim** , digite Excel e, em seguida, selecione **atualizar uma linha**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 152":::

Insira as informações do Excel e atualize a **coluna de chave**, o **valor de chave** e os campos **PersonName** . Isso acrescentará o nome detectado pela API à planilha do Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 7":::

## <a name="get-the-phone-number"></a>Obter o número de telefone

Minimize a ação **aplicar a cada 2** clicando no nome. Em seguida, adicione outro **se aplicar a cada** ação, como antes. Ele será chamado **de aplicar a cada 3**. Selecione a caixa de texto e adicione **entidades** como a saída para esta ação. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 8":::

Em **aplicar a cada 3**, adicione um controle de **condição** . Ela será nomeada **condição 2**. Na primeira caixa de texto, pesquise e adicione o **tipo de entidades** na janela de conteúdo dinâmico. Verifique se a caixa central está definida como **igual a**. Em seguida, na caixa de texto à direita, digite `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 99":::

Na condição **se sim** , adicione uma ação **atualizar uma linha** . Em seguida, insira as informações como fizemos acima, para a coluna números de telefone da planilha do Excel. Isso acrescentará o número de telefone detectado pela API à planilha do Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 254":::


## <a name="get-the-plumbing-issues"></a>Obter os problemas de encanamento

Minimize **aplicar a cada 3** clicando no nome. Em seguida, crie outro **se aplicar a cada** na ação pai. Selecione a caixa de texto e adicione **entidades** como a saída para esta ação na janela de conteúdo dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 11":::


Em seguida, o fluxo verificará se a descrição do problema da linha da tabela do Excel contém a palavra "encanamento". Se sim, ele adicionará "encanamento" na coluna IssueType. Caso contrário, entraremos em "other".

Dentro da ação **aplicar a cada 4** , adicione um controle de **condição** . Ele será denominado **condição 3**. Na primeira caixa de texto, procure e adicione a **Descrição** do arquivo do Excel, usando a janela de conteúdo dinâmico. Verifique se a caixa central diz **contém**. Em seguida, na caixa de texto à direita, localize e selecione `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 12":::


Na condição **se sim** , clique em **Adicionar uma ação** e selecione **atualizar uma linha**. Em seguida, insira as informações como antes. Na coluna IssueType, selecione `var_plumbing` . Isso aplicará um rótulo de "encanamento" à linha.

Na condição **se não** , clique em **Adicionar uma ação** e selecione **atualizar uma linha**. Em seguida, insira as informações como antes. Na coluna IssueType, selecione `var_other` . Isso aplicará um rótulo "outro" à linha.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Adicione Análise de Texto credenciais ao seu fluxo. 13":::

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

No canto superior direito da tela, clique em **salvar** e **teste**. Selecione  **eu executarei a ação do gatilho**. Clique em **salvar & teste**, **executar fluxo** e, em seguida, **concluído**.

O arquivo do Excel será atualizado em sua conta do OneDrive. Ele será semelhante ao mostrado abaixo.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A planilha do Excel atualizada.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar mais soluções](../text-analytics-user-scenarios.md)
