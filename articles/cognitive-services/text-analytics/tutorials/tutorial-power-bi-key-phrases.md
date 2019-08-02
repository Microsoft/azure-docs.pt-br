---
title: 'Tutorial: Integrar o Power BI ao Serviço Cognitivo de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Saiba como usar a Análise de Texto para extrair frases-chave de textos armazenados no Power BI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: dba65e68e7c2204a4d4d7f80a603de607bba7609
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697341"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Tutorial: Integrar o Power BI ao Serviço Cognitivo de Análise de Texto

O Microsoft Power BI Desktop é um aplicativo gratuito que permite que você se conecte aos seus dados, transforme-os e visualize-os. O serviço Análise de Texto, parte dos Serviços Cognitivos do Microsoft Azure, oferece processamento de linguagem natural. Dado o texto não estruturado bruto, é possível extrair as frases mais importantes, analisar o sentimento e identificar entidades conhecidas, como marcas. Juntas, essas ferramentas podem ajudar você a ver rapidamente sobre o que seus clientes estão falando e como eles se sentem sobre isso.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Use o Power BI Desktop para importar e transformar dados
> * Criar uma função personalizada no Power BI Desktop
> * Integrar o Power BI Desktop à API de Frases-chave de Análise de Texto
> * Use a API de Frases-chave de Análise de Texto para extrair as frases mais importantes dos comentários do cliente
> * Criar uma nuvem de palavras com base nos comentários do cliente

## <a name="prerequisites"></a>Pré-requisitos
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Faça download sem qualquer custo](https://powerbi.microsoft.com/get-started/).
- Uma conta do Microsoft Azure. [Inicie uma avaliação gratuita](https://azure.microsoft.com/free/) ou [entre](https://portal.azure.com/).
- Uma conta da API de Serviços Cognitivos com a API de Análise de Texto. Se você não tiver uma, poderá [inscrever-se](../../cognitive-services-apis-create-account.md) e usar a camada gratuita para 5.000 transações/mês (consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) para concluir este tutorial.
- A [chave de acesso da Análise de Texto](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerada para você durante a inscrição.
- Comentários do cliente. É possível usar [nossos dados de exemplo](https://aka.ms/cogsvc/ta) ou seus próprios dados. Este tutorial pressupõe que você está usando nossos dados de exemplo.

## <a name="load-customer-data"></a>Carregar dados do cliente
<a name="LoadingData"></a>

Para começar, abra o Power BI Desktop e carregue o arquivo CSV (valores separados por vírgulas) `FabrikamComments.csv` que você baixou em [Pré-requisitos](#Prerequisites). Esse arquivo representa um dia inteiro de atividades hipotéticas no fórum de suporte de uma pequena empresa fictícia.

> [!NOTE]
> O Power BI pode usar dados de uma ampla variedade de fontes, como o Facebook ou um banco de dados SQL. Saiba mais em [Integração do Facebook com o Power BI](https://powerbi.microsoft.com/integrations/facebook/) e [Integração do SQL Server com o Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Na janela principal do Power BI Desktop, selecione a faixa de opções **Início**. No grupo **Dados externos** da faixa de opções, abra o menu suspenso **Obter dados** e selecione **Texto/CSV**.

![[O botão Obter Dados]](../media/tutorials/power-bi/get-data-button.png)

O diálogo Abrir é exibido. Navegue até sua pasta de Downloads ou até a pasta em que você baixou o arquivo `FabrikamComments.csv`. Clique em `FabrikamComments.csv` e no botão **Abrir**. O diálogo de importação do CSV é exibido.

![[O diálogo de importação do CSV]](../media/tutorials/power-bi/csv-import.png)

O diálogo de importação do CSV permite verificar se o Power BI Desktop detectou conjunto de caracteres, delimitadores, linhas de cabeçalho e tipos de coluna corretamente. Todas essas informações estão corretas e, portanto, clique em **Carregar**.

Para ver os dados carregados, clique no botão **Exibição de Dados** na borda esquerda do workspace do Power BI. Uma tabela é aberta com os dados, como ocorre no Microsoft Excel.

![[A exibição inicial dos dados importados]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Preparar os dados
<a name="PreparingData"></a>

Talvez seja necessário transformar seus dados no Power BI Desktop antes que eles possam ser processados pela API de Frases-chave do serviço Análise de Texto.

Os dados de exemplo contêm uma coluna `subject` e uma coluna `comment`. Com a função Mesclar colunas no Power BI Desktop, é possível extrair frases-chave dos dados em ambas as colunas, em vez de apenas da coluna `comment`.

No Power BI Desktop, selecione a faixa de opções **Início**. No grupo **Dados externos**, clique em **Editar consultas**.

![[O grupo Dados Externos na faixa de opções Página Inicial]](../media/tutorials/power-bi/edit-queries.png)

Selecione `FabrikamComments` na lista **Consultas** do lado esquerdo da janela, se ainda não estiver selecionado.

Agora, selecione as colunas `subject` e `comment` na tabela. Talvez seja necessário rolar horizontalmente para ver essas colunas. Primeiro, clique no cabeçalho da coluna `subject`, em seguida, mantenha pressionada a tecla Control e clique no cabeçalho da coluna `comment`.

![[Selecionando campos a serem mesclados]](../media/tutorials/power-bi/select-columns.png)

Selecione a faixa de opções **Transformar**. No grupo **Colunas de Texto** da faixa de opções, clique em **Mesclar colunas**. O diálogo Mesclar Colunas é exibido.

![[Mesclando campos usando o diálogo Mesclar Colunas]](../media/tutorials/power-bi/merge-columns.png)

Na caixa de diálogo Mesclar Colunas, escolha `Tab` como separador e clique em **OK.**

Você também pode considerar a filtragem de mensagens em branco usando o filtro Remover Vazio ou removendo os caracteres que não podem ser impressos usando a transformação Limpar. Se seus dados contiverem uma coluna como a coluna `spamscore` no arquivo de exemplo, será possível ignorar comentários "spam" usando um Filtro de número.

## <a name="understand-the-api"></a>Compreender a API
<a name="UnderstandingAPI"></a>

A [API de Frases-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) do serviço Análise de Texto pode processar até mil documentos de texto por solicitação HTTP. O Power BI prefere lidar com um registro por vez; portanto, neste tutorial, suas chamadas à API incluirão apenas um único documento cada. A API de Frases-chave requer os campos a seguir para cada documento que está sendo processado.

| | |
| - | - |
| `id`  | Um identificador exclusivo para este documento dentro da solicitação. A resposta também contém esse campo. Dessa forma, se você processar mais de um documento, poderá associar facilmente as frases-chave extraídas ao documento que as originou. Neste tutorial, como você está processando apenas um documento por solicitação, é possível embutir o valor de `id` em código para ser o mesmo para cada solicitação.|
| `text`  | O texto a ser processado. O valor desse campo é proveniente da coluna `Merged` que você criou na [seção anterior](#PreparingData), que contém o texto de comentário e a linha do assunto combinados. A API de Frases-Chave exige que esses dados tenham menos de 5.120 caracteres.|
| `language` | O código para o idioma natural em que o documento foi escrito. Todas as mensagens nos dados de exemplo estão em inglês; portanto, é possível embutir o valor `en` em código para esse campo.|

## <a name="create-a-custom-function"></a>Criar uma função personalizada
<a name="CreateCustomFunction"></a>

Agora você está pronto para criar a função personalizada que integrará o Power BI e a Análise de Texto. A função recebe o texto a ser processado como um parâmetro. Ela converte os dados de e para o formato JSON necessário e realiza a solicitação HTTP para a API de Frases-chave. A função analisa a resposta da API e retorna uma cadeia de caracteres que contém uma lista separada por vírgulas das frases-chave extraídas.

> [!NOTE]
> As funções personalizadas do Power BI Desktop são escritas na [linguagem de fórmula Power Query M](https://docs.microsoft.com/powerquery-m/power-query-m-reference), ou sua forma abreviada "M”. M é uma linguagem de programação funcional com base em [F#](https://docs.microsoft.com/dotnet/fsharp/). Você não precisa ser um programador para concluir este tutorial, pois o código necessário está incluído abaixo.

No Power BI Desktop, certifique-se de ainda estar na janela do Editor de consultas. Se não estiver, selecione a faixa de opções **Início** e, no grupo **Dados externos**, clique em **Editar consultas**.

Agora, na faixa de opções **Início**, no grupo **Nova Consulta**, abra o menu suspenso **Nova fonte** e selecione **Consulta em branco**. 

Uma nova consulta, nomeada inicialmente como `Query1`, é exibida na lista de consultas. Clique duas vezes nessa entrada e nomeie-a `KeyPhrases`.

Agora, na faixa de opções **Início**, no grupo **Consulta**, clique em **Editor Avançado** para abrir a janela do Editor Avançado. Exclua o código que já está nessa janela e cole o código a seguir. 

> [!NOTE]
> Os exemplos a seguir pressupõem que o ponto de extremidade da API de Análise de Texto começa com `https://westus.api.cognitive.microsoft.com`. A Análise de Texto permite que você crie uma assinatura em 13 regiões diferentes. Se você se inscreveu no serviço em uma região diferente, use o ponto de extremidade da região selecionada. É possível encontrar esse ponto de extremidade entrando no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), selecionando a assinatura da Análise de Texto e selecionando a página Visão geral.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Substitua `YOUR_API_KEY_HERE` pela chave de acesso da Análise de Texto. Também é possível encontrar essa chave entrando no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), selecionando a assinatura da Análise de Texto e selecionando a página Visão geral. Deixe as aspas antes e após a chave. Em seguida, clique em **Concluído.**

## <a name="use-the-custom-function"></a>Usar a função personalizada
<a name="UseCustomFunction"></a>

Agora é possível usar a função personalizada para extrair as frases-chave de cada um dos comentários de clientes e armazená-las em uma nova coluna na tabela. 

No Power BI Desktop, na janela do Editor de Consultas, retorne para a consulta `FabrikamComments`. Selecione a faixa de opções **Adicionar coluna**. No grupo **Geral**, clique em **Invocar função personalizada**.

![[Botão Invocar Função Personalizada]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

A caixa de diálogo Invocar função personalizada é exibida. No **Novo nome da coluna**, insira `keyphrases`. Na **Consulta de função**, selecione a função personalizada que você criou, `KeyPhrases`.

Um novo campo é exibido na caixa de diálogo **texto (opcional)** . Este campo está solicitando qual coluna queremos usar para fornecer valores para o parâmetro `text` da API de Frases-chave. (Lembre-se de que você já embutiu os valores em código para os parâmetros `language` e `id`.) Selecione `Merged` (a coluna que criamos [anteriormente](#PreparingData) mesclando os campos de assunto e mensagem) no menu suspenso.

![[Invocando uma função personalizada]](../media/tutorials/power-bi/invoke-custom-function.png)

Por fim, clique em **OK.**

Se tudo estiver pronto, o Power BI chamará sua função personalizada uma vez para cada linha na tabela. Ela envia as consultas para a API de Frases-chave e adiciona uma nova coluna à tabela para armazenar os resultados. Mas antes que isso ocorra, talvez seja necessário especificar as configurações de autenticação e privacidade.

## <a name="authentication-and-privacy"></a>Autenticação e privacidade
<a name="Authentication"></a>

Após fechar a caixa de diálogo Invocar função personalizada, uma faixa poderá ser exibida solicitando que você especifique como se conectar à API de Frases-chave.

![[faixa de credenciais]](../media/tutorials/power-bi/credentials-banner.png)

Clique em **Editar credenciais**, certifique-se de que `Anonymous` está selecionado na caixa de diálogo e, em seguida, clique em **Conectar**. 

> [!NOTE]
> Você seleciona `Anonymous`, porque o serviço Análise de Texto faz a autenticação usando sua chave de acesso para que o Power BI não precise fornecer credenciais para a solicitação HTTP em si.

![[definindo a configuração como anônima]](../media/tutorials/power-bi/access-web-content.png)

Se você vê a faixa Editar Credenciais mesmo depois de escolher o acesso anônimo, é possível que tenha se esquecido de colar sua chave de acesso da Análise de Texto no código na `KeyPhrases` [função personalizada](#CreateCustomFunction).

Em seguida, uma faixa pode aparecer solicitando que você forneça informações sobre a privacidade de suas fontes de dados. 

![[faixa de privacidade]](../media/tutorials/power-bi/privacy-banner.png)

Clique em **Continuar** e escolha `Public` em cada uma das fontes de dados na caixa de diálogo. Em seguida, clique em **Salvar.**

![[configurando a privacidade da fonte de dados]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Criar a nuvem de palavras
<a name="WordCloud"></a>

Depois de lidar com eventuais faixas exibidas, clique em **Fechar e Aplicar** na faixa de opções Página Inicial para fechar o Editor de Consultas.

O Power BI Desktop leva algum tempo para fazer as solicitações HTTP necessárias. Para cada linha na tabela, a nova coluna `keyphrases` contém as frases-chave detectadas no texto pela API de frases-chave. 

Agora você usará esta coluna para gerar uma nuvem de palavras. Para começar, clique no botão **Relatório** na janela principal do Power BI Desktop à esquerda do workspace.

> [!NOTE]
> Por que usar frases-chave extraídas para gerar uma nuvem de palavras em vez de usar o texto completo de cada comentário? As frases-chave nos fornecem as palavras *importantes* dos comentários dos clientes, não apenas as palavras *mais comuns*. Além disso, o dimensionamento das palavras na nuvem gerada não é distorcido pelo uso frequente de uma palavra em um número relativamente pequeno de comentários.

Se você ainda não tem o objeto visual personalizado da Nuvem de Palavras instalado, instale-o. No painel Visualizações à direita do workspace, clique nas reticências ( **...** ) e escolha **Importar da Store**. Em seguida, pesquise "nuvem" e clique no botão **Adicionar** ao lado do objeto visual Nuvem de Palavras. O Power BI instala o objeto visual Nuvem de Palavras e avisa que ele foi instalado com êxito.

![[adicionando um objeto visual personalizado]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Primeiro, clique no ícone da Nuvem de Palavras no painel Visualizações.

![[Ícone da Nuvem de Palavras no painel Visualizações]](../media/tutorials/power-bi/visualizations-panel.png)

Um novo relatório aparece no workspace. Arraste o campo `keyphrases` do painel Campos para o campo Categoria no painel Visualizações. A nuvem de palavras é exibida no relatório.

Agora, alterne para a página Formato do painel Visualizações. Na categoria Palavras irrelevantes, ative **Palavras Irrelevantes Padrão** para eliminar palavras curtas e comuns como "de" da nuvem. 

![[ativando palavras irrelevantes padrão]](../media/tutorials/power-bi/default-stop-words.png)

Um pouco mais abaixo no painel, desative **Girar Texto** e **Título**.

![[ativar modo de foco]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Clique na ferramenta Modo de Foco no relatório para obter uma visão melhor da nuvem de palavras. A ferramenta expande a nuvem de palavras para preencher o workspace inteiro, conforme mostrado abaixo.

![[Uma nuvem de palavras]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Mais serviços da Análise de Texto
<a name="MoreServices"></a>

O serviço Análise de Texto, um dos serviços Cognitivos oferecidos pelo Microsoft Azure, também fornece detecção de idioma e análise de sentimento. A detecção de idioma será particularmente útil se os comentários do seu cliente não estiverem em inglês.

Essas outras APIs são semelhantes à API de frases-chave. Isso significa que é possível integrá-las ao Power BI Desktop usando funções personalizadas quase idênticas à que você criou neste tutorial. Basta criar uma consulta em branco e colar o código apropriado abaixo no Editor Avançado, como feito anteriormente. (Não se esqueça da chave de acesso!) Em seguida, como feito anteriormente, use a função para adicionar uma nova coluna à tabela.

A função Análise de Sentimento abaixo retorna uma pontuação que indica o grau de positividade do sentimento expressado no texto.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Aqui estão duas versões de uma função Detecção de Idioma. A primeira retorna o código de idioma ISO (por exemplo, `en` para inglês) e a segunda retorna o nome "amigável" (por exemplo, `English`). Você deve notar que a última linha do corpo é diferente entre as duas versões.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Por fim, aqui está uma variante da função Frases-chave já apresentada que retorna as frases como um objeto de lista, em vez de uma única cadeia de caracteres de frases separada por vírgulas. 

> [!NOTE]
> O retorno de uma única cadeia de caracteres simplificou nosso exemplo de nuvem de palavras. Por outro lado, o formato de lista é mais flexível para funcionar com as frases retornadas no Power BI. Você pode manipular objetos de lista no Power BI Desktop usando o grupo Coluna Estruturada na faixa de opções Transformação do Editor de Consultas.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Próximas etapas
<a name="NextSteps"></a>

Saiba mais sobre o serviço Análise de Texto, a linguagem de fórmula Power Query M ou o Power BI.

> [!div class="nextstepaction"]
> [Referência de API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Referência do Power Query M](https://docs.microsoft.com/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Documentação do Power BI](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)
