---
title: Modere imagens com o Console de API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a API de Moderação de Imagem no Content Moderator do Azure para iniciar os fluxos de trabalho de moderação de revisão e verificação de conteúdo de imagem.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 20353d488cba255925d21017b77d1def2d06b30b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561252"
---
# <a name="moderate-images-from-the-api-console"></a>Moderar imagens do console da API

Use a [API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) no Content Moderator do Azure para iniciar os fluxos de trabalho de moderação de revisão e verificação de conteúdo de imagem. O trabalho de moderação examina o seu conteúdo obscenidades e o compara com listas negras personalizadas e compartilhadas.

## <a name="use-the-api-console"></a>Usar o console de API
Antes de você pode testar a API no console on-line, você precisa da chave de assinatura. Ela está localizada na guia **Configurações**, na caixa **Ocp-Apim-Subscription-Key**. Para mais informações, confira [Visão Geral](overview.md).

1. Vá para a [referência da API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   A página de moderação de imagens **Imagem - Avaliar** é aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção da região da página Testar Imagem - Avaliar](images/test-drive-region.png)
  
   O console da API **Imagem - Avaliar** é aberto.

3. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

   ![Chave de assinatura do console Testar Imagem - Avaliar](images/try-image-api-1.PNG)

4. Na caixa **Corpo da solicitação**, use a imagem de exemplo padrão ou especifique uma imagem para verificar. Você pode enviar a imagem como dados de bits binários, ou especificar uma URL publicamente acessível de uma imagem. 

   Para este exemplo, use o caminho fornecido na caixa **Corpo da solicitação** e, em seguida, selecione **Enviar**. 

   ![Corpo da solicitação do console Testar Imagem - Avaliar](images/try-image-api-2.PNG)

   Esta é a imagem nessa URL:

   ![Imagem de exemplo do console Testar Imagem - Avaliar](images/sample-image.jpg) 

5. Selecione **Enviar**.

6. A API retorna uma pontuação de probabilidade para cada classificação. Ela também retorna uma determinação de se a imagem atende as condições (**true** ou **false**). 

   ![Pontuação de probabilidade e determinação de condição do console Testar Imagem - Avaliar](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Detecção facial

Você pode usar a API de Moderação de Imagem para localizar as faces em uma imagem. Essa opção pode ser útil quando você estiver preocupado com a privacidade e quiser impedir que uma face específica seja publicada em sua plataforma. 

1. Na [referência da API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, em **Imagem**, selecione **Localizar faces**. 

   A página **Imagem - Localizar faces** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção da região da página Testar Imagem - Localizar faces](images/test-drive-region.png)

   O console da API **Imagem - Localizar faces** será aberto.

3. Especifique uma imagem para verificar. Você pode enviar a imagem como dados de bits binários, ou especificar uma URL publicamente acessível de uma imagem. Este exemplo vincula a uma imagem que é usada em uma história da CNN.

   ![Imagem de exemplo Testar Imagem - Localizar faces](images/try-image-api-face-image.jpg)

   ![Solicitação de exemplo Testar Imagem - Localizar faces](images/try-image-api-face-request.png)

4. Selecione **Enviar**. Neste exemplo, a API localiza duas faces e retorna suas coordenadas na imagem.

   ![Caixa de conteúdo de resposta do exemplo Testar Imagem - Localizar faces](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Detecção de texto por meio do recurso de OCR

Você pode usar o recurso OCR do Content Moderator para detectar texto em imagens.

1. Na [referência da API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, em **Imagem**, selecione **OCR**. 

   A página **Imagem - OCR** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região da página Imagem - OCR](images/test-drive-region.png)

   O console da API **Imagem - OCR** será aberto.

3. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

4. Na caixa **Corpo da solicitação**, use a imagem de exemplo padrão. Esta é a mesma imagem que foi usada na seção anterior.

5. Selecione **Enviar**. O texto extraído é exibido em JSON:

   ![Caixa conteúdo de resposta de exemplo de Imagem - OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou inicie com o [Início rápido do .NET de Moderação de Imagem](image-moderation-quickstart-dotnet.md) para integrar o aplicativo.
