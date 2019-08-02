---
title: Executar operações de imagem – Java
titleSuffix: Azure Cognitive Services
description: Explore um aplicativo Java Swing básico que usa a API da Pesquisa Visual Computacional nos Serviços Cognitivos do Azure. Execute o OCR, crie miniaturas e trabalhe com recursos visuais em uma imagem.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: 16f75095c7e4461c84a1fea28cc620c49b6c5bca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565691"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Usar recursos de Pesquisa Visual Computacional com a API REST e o Java

Este tutorial mostra os recursos da API REST da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure.

Explore um aplicativo Java Swing que usa a API REST da Pesquisa Visual Computacional para executar o OCR (reconhecimento óptico de caracteres), criar miniaturas com recorte inteligente, além de detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. Este exemplo permite enviar uma URL de imagem para análise ou processamento. É possível usar esse exemplo de software livre como um modelo para compilar seu próprio aplicativo em Java para usar a API REST da Pesquisa Visual Computacional.

Este tutorial abordará como usar a Pesquisa Visual Computacional para:

> [!div class="checklist"]
> * Analisar uma imagem
> * Identificar um ponto de referência artificial ou natural em uma imagem
> * Identificar uma celebridade em uma imagem
> * Criar uma miniatura de qualidade com base em uma imagem
> * Ler texto impresso em uma imagem
> * Ler texto manuscrito em uma imagem

O aplicativo Java Swing Form já foi escrito, mas não tem funcionalidade. Neste tutorial, você adiciona o código específico da API da Pesquisa Visual Computacional para concluir a funcionalidade do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi desenvolvido usando o IDE NetBeans. Especificamente, a versão **Java SE** do NetBeans, que você pode [baixar aqui](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Inscrever-se na API da Pesquisa Visual Computacional e obter uma chave de assinatura

Antes de criar o exemplo, você deve assinar API da Pesquisa Visual Computacional que faz parte dos serviços cognitivas do Azure. Para detalhes de assinatura e gerenciamento de chaves, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). As chaves primária e secundária são válidas para uso neste tutorial.

## <a name="acquire-incomplete-tutorial-project"></a>Adquirir projeto de tutorial incompleto

### <a name="download-the-project"></a>Baixe o projeto

1. Acesse o repositório do [Tutorial da Pesquisa Visual Computacional Java dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Clique no botão **Clonar ou baixar**.
1. Clique em **Baixar ZIP** para baixar um arquivo .zip do projeto de tutorial.

Não é necessário extrair o conteúdo do arquivo .zip, porque o NetBeans importa o projeto do arquivo .zip.

### <a name="import-the-tutorial-project"></a>Importar o projeto de tutorial

Importe o arquivo **cognitive-services-java-computer-vision-tutorial-master.zip** para o NetBeans.

1. No NetBeans, clique em **Arquivo** > **Importar Projeto** > **De ZIP...** . A caixa de diálogo **Importar projetos do ZIP** é exibida.
1. No campo **Arquivo ZIP:** , clique no botão **Procurar** para localizar o arquivo **cognitive-services-java-computer-vision-tutorial-master.zip** e, em seguida, clique e **Abrir**.
1. Clique em **Importar** na caixa de diálogo **Importar projetos do ZIP**.
1. No painel **Projetos**, expanda **ComputerVision** > **Pacotes de origem** >  **&lt;pacote padrão&gt;** . 
   Algumas versões do NetBeans usam **src** em vez de **Pacotes de origem** >  **&lt;pacote padrão&gt;** . Nesse caso, expanda **src**.
1. Clique duas vezes em **MainFrame.java** para carregar o arquivo no editor do NetBeans. A guia **Design** do arquivo **MainFrame.java** é exibida.
1. Clique na guia **Origem** para exibir o código-fonte Java.

### <a name="build-and-run-the-tutorial-project"></a>Criar e executar o projeto de tutorial

1. Pressione **F6** para criar e executar o aplicativo de tutorial.

    No aplicativo de tutorial, clique em uma guia para exibir o painel desse recurso. Os botões têm métodos vazios, portanto, não fazem nada.

    Na parte inferior da janela estão os campos **Chave de Assinatura** e **Região assinatura**. Esses campos devem ser preenchidos com uma chave de assinatura válida e a região correta dessa chave de assinatura. Para obter uma chave de assinatura, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). Se você obteve sua chave de assinatura da avaliação gratuita nesse link, então a região padrão **westcentralus** é a região correta para suas chaves de assinatura.

1. Saia do aplicativo de tutorial.

## <a name="add-tutorial-code-to-the-project"></a>Adicionar código de tutorial ao projeto

O aplicativo Java Swing é configurado com seis guias. Cada guia demonstra uma função diferente da Pesquisa Visual Computacional (analisar, OCR. e assim por diante). As seis seções do tutorial não têm interdependências, portanto é possível adicionar uma seção, todas as seis seções ou qualquer subconjunto. É possível adicionar as seções em qualquer ordem.

### <a name="analyze-an-image"></a>Analisar uma imagem

O recurso Analisar da Pesquisa Visual Computacional examina uma imagem para mais de 2.000 objetos reconhecíveis, seres vivos, cenários e ações. Quando a análise estiver concluída, o recurso Analisar retornará um objeto JSON que descreve a imagem com marcas descritivas, análise de cores, legendas e muito mais.

Para concluir o recurso Analisar do aplicativo de tutorial, execute as etapas a seguir:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Adicionar o código do manipulador de eventos para o botão analisar

O método do manipulador de eventos **analyzeImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **AnalyzeImage** para analisar a imagem. Quando **AnalyzeImage** é retornado, o método exibe a resposta JSON formatada na área de texto **Resposta**, extrai a primeira legenda do **JSONObject** e exibe a legenda e o nível de confiança de que a legenda está correta.

Copie e cole o seguinte código no método **analyzeImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **AnalyzeImage** encapsula a chamada à API REST para analisar uma imagem. O método retorna um **JSONObject** que descreve a imagem ou **null**, se houve um erro.

Copie e cole o método **AnalyzeImage** logo embaixo do método **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>Executar a função Analyze

Pressione **F6** para executar o aplicativo. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem para analisar e clique no botão**Analisar Imagem** para analisar uma imagem e ver o resultado.

### <a name="recognize-a-landmark"></a>Reconhecer um ponto de referência

O recurso Ponto de Referência da Pesquisa Visual Computacional analisa uma imagem para pontos de referência naturais e artificiais, como montanhas ou edifícios famosos. Quando a análise estiver concluída, o Ponto de Referência retornará um objeto JSON que identifica os pontos de referência localizados na imagem.

Para concluir o recurso Ponto de Referência do aplicativo de tutorial, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Adicionar o código do manipulador de eventos do botão do formulário

O método do manipulador de eventos **landmarkImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **LandmarkImage** para analisar a imagem. Quando **LandmarkImage** é retornado, o método exibe a resposta JSON formatada na área de texto **Resposta** e, em seguida, extrai o primeiro nome do ponto de referência do **JSONObject** e o exibe na janela juntamente com o nível de confiança de que o ponto de referência foi identificado corretamente.

Copie e cole o seguinte código no método **landmarkImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **LandmarkImage** encapsula a chamada à API REST para analisar uma imagem. O método retorna um **JSONObject** que descreve os pontos de referência encontrados na imagem ou **null**, se houve um erro.

Copie e cole o método **LandmarkImage** logo embaixo do método **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>Executar a função de ponto de referência

Pressione **F6** para executar o aplicativo. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Clique na guia **Ponto de Referência**, insira uma URL para uma imagem de um ponto de referência e, em seguida, clique no botão **Analisar imagem** para analisar uma imagem e veja o resultado.

### <a name="recognize-celebrities"></a>Reconhecer celebridades

O recurso Celebridades da Pesquisa Visual Computacional analisa uma imagem de pessoas famosas. Quando a análise estiver concluída, o recurso Celebridades retornará um objeto JSON que identifica as Celebridades localizadas na imagem.

Para concluir o recurso Celebridades do aplicativo de tutorial, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Adicionar o código do manipulador de eventos para o botão celebridades

O método do manipulador de eventos **celebritiesImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **CelebritiesImage** para analisar a imagem. Quando **CelebritiesImage** é retornado, o método exibe a resposta JSON formatada na área de texto **Resposta** e, em seguida, extrai o primeiro nome da celebridade do **JSONObject** e exibe o nome na janela juntamente com o nível de confiança de que a celebridade foi identificada corretamente.

Copie e cole o seguinte código no método **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **CelebritiesImage** encapsula a chamada à API REST para analisar uma imagem. O método retorna um **JSONObject** que descreve as celebridades encontradas na imagem ou **null**, se houve um erro.

Copie e cole o método **CelebritiesImage** logo embaixo do método **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>Executar a função celebridades

Pressione **F6** para executar o aplicativo. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Clique na guia **Celebridades**, insira uma URL para uma imagem de uma celebridade e, em seguida, clique no botão **Analisar imagem** para analisar uma imagem e veja o resultado.

### <a name="intelligently-generate-a-thumbnail"></a>Gerar uma miniatura de maneira inteligente

O recurso Miniatura da Pesquisa Visual Computacional gera uma miniatura de uma imagem. Ao usar o recurso **Recorte Inteligente** o recurso Miniatura identificará a área de interesse em uma imagem e centralizará a miniatura nessa área, para gerar mais imagens em miniatura esteticamente agradáveis.

Para concluir o recurso Miniatura do aplicativo de tutorial, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Adicionar o código do manipulador de eventos para o botão de miniatura

O método do manipulador de eventos **thumbnailImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **getThumbnailImage** para criar a miniatura. Quando **getThumbnailImage** é retornado, o método exibe a miniatura gerada.

Copie e cole o seguinte código no método **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **getThumbnailImage** encapsula a chamada à API REST para analisar uma imagem. O método retorna um **BufferedImage** que contém a miniatura ou **null**, se houve um erro. A mensagem de erro será retornada no primeiro elemento da matriz de cadeia de caracteres **jsonError**.

Copie e cole o seguinte método **getThumbnailImage** logo embaixo do método **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>Executar a função de miniatura

Pressione **F6** para executar o aplicativo. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Clique na guia **Miniatura**, insira uma URL para uma imagem e, em seguida, clique no botão **Gerar miniatura** para analisar uma imagem e veja o resultado.

### <a name="read-printed-text-ocr"></a>Ler texto impresso (OCR)

O recurso OCR (Reconhecimento Óptico de Caracteres) da Pesquisa Visual Computacional analisa uma imagem do texto impresso. Após a conclusão da análise, o OCR retornará um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir o recurso OCR do aplicativo de tutorial, execute as etapas a seguir:

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Adicionar o código do manipulador de eventos ao botão OCR

O método do manipulador de eventos **ocrImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **OcrImage** para analisar a imagem. Quando **OcrImage** é retornado, o método exibe o texto detectado como JSON formatado na área de texto **Resposta**.

Copie e cole o seguinte código no método **ocrImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **OcrImage** encapsula a chamada à API REST para analisar uma imagem. O método retorna um **JSONObject** dos dados JSON retornados da chamada ou **null**, se houve um erro.

Copie e cole o seguinte método **OcrImage** logo embaixo do método **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>Executar a função OCR

Pressione **F6** para executar o aplicativo. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Clique na guia **OCR**, insira uma URL para uma imagem de texto impresso e, em seguida, clique no botão **Ler imagem** para analisar uma imagem e veja o resultado.

### <a name="read-handwritten-text-handwriting-recognition"></a>Ler texto manuscrito (reconhecimento de manuscrito)

O recurso Reconhecimento de Manuscrito da Pesquisa Visual Computacional analisa uma imagem de texto manuscrito. Após a conclusão da análise, o Reconhecimento de Manuscrito retornará um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir o recurso Reconhecimento de Manuscrito do aplicativo de tutorial, execute as etapas a seguir:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Adicione o código do manipulador de eventos ao botão de manuscrito

O método do manipulador de eventos **handwritingImageButtonActionPerformed** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama o método **HandwritingImage** para analisar a imagem. Quando **HandwritingImage** é retornado, o método exibe o texto detectado como JSON formatado na área de texto **Resposta**.

Copie e cole o seguinte código no método **handwritingImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá que você cole na linha de definição de método (```private void```) ou na chave de fechamento desse método. Para copiar o código, copie as linhas entre a definição do método e a chave de fechamento e cole-as no conteúdo do método.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **HandwritingImage** encapsula as duas chamadas à API REST necessárias para analisar uma imagem. Como o reconhecimento de manuscrito é um processo lento, é utilizado um processo de duas etapas. A primeira chamada envia a imagem para processamento e a segunda chamada recupera o texto detectado quando o processamento é concluído.

Depois que o texto é recuperado, o método **HandwritingImage** retorna um **JSONObject** que descreve o texto e os locais do texto ou **null**, se houve um erro.

Copie e cole o seguinte método **HandwritingImage** logo embaixo do método **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>Executar a função de manuscrito

Para executar o aplicativo, pressione **F6**. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Clique na guia **Ler texto manuscrito**, insira uma URL para um texto manuscrito e, em seguida, clique no botão **Ler imagem** para analisar uma imagem e veja o resultado.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você usou a API REST do Pesquisa Visual Computacional com Java para testar muitos dos recursos de análise de imagem disponíveis. Em seguida, consulte a documentação de referência para saber mais sobre as APIs envolvidas.

- [API REST do Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
