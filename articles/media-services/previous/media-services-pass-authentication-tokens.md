---
title: Passar os tokens de autenticação para os Serviços de Mídia do Azure | Microsoft Docs
description: Saiba como enviar os tokens de autenticação do cliente para o serviço de entrega de chaves dos Serviços de Mídia do Azure
services: media-services
keywords: Proteção de conteúdo, gerenciamento de direitos digitais, autenticação de token
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4daec8ef5f2cc1da3a211113e7236dfd22803d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008226"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Saiba como os clientes passam tokens para o serviço de entrega de chaves dos Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Os clientes frequentemente perguntam como um player pode passar tokens para o serviço de entrega de chaves dos Serviços de Mídia do Azure para verificação para que o player possa obter a chave. Os Serviços de Mídia são compatíveis com formatos SWT (Token Web Simples) e JWT (Token Web JSON). A autenticação de token é aplicada a qualquer tipo de chave, independentemente de você usar uma criptografia comum ou criptografia de envelope AES no sistema.

 Dependendo do player e plataforma intencionados, você pode passar o token com o seu player dos seguintes modos:

- Por meio do cabeçalho de autorização HTTP.
    > [!NOTE]
    > O prefixo "Portador" é esperado para as especificações de OAuth 2.0. Um player de exemplo com a configuração do token está hospedado na [página de demonstração](https://ampdemo.azureedge.net/) do Player de Mídia do Azure. Para definir a fonte de vídeo, escolha **AES (Token JWT)** ou **AES (Token SWT)**. O token é passado por meio do cabeçalho de autorização.

- Com a adição de um parâmetro de consulta de URL com “token=tokenvalue”.  
    > [!NOTE]
    > O prefixo "Portador" não é esperado. Como o token é enviado por uma URL, é necessário proteger a cadeia de caracteres do token. Observe aqui um código de exemplo C# sobre como fazer isso:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Pelo campo CustomData.
Essa opção é usada apenas para a aquisição de licença do PlayReady, pelo campo CustomData do Desafio de Aquisição de Licença do PlayReady. Nesse caso, o token deve estar dentro do documento XML descrito aqui:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Coloque o seu token de autenticação no elemento Token.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
