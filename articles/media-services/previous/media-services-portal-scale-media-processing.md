---
title: Dimensionar o processamento de mídia usando o portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas do dimensionamento do processamento de mídia usando o portal do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 49c3899b912a88605e9269cdb1c34c7e18ed5247
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009718"
---
# <a name="change-the-reserved-unit-type"></a>Alterar o tipo de unidade reservada

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Visão geral

Uma conta dos Serviços de Mídia está associada a um Tipo de Unidade Reservada que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**.

Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com **unidades reservadas** (RUs). O número de URs provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em determinada conta.

>[!NOTE]
>As URs trabalham para paralelizar todo o processamento de mídia, incluindo os trabalhos de indexação, usando o Azure Media Indexer. No entanto, ao contrário da codificação, a indexação de trabalhos não será processada mais rapidamente com unidades reservadas mais rápidas.

> [!IMPORTANT]
> Lembre-se de examinar o tópico [Visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o tópico de dimensionamento de processamento de mídia.
> 
> 

## <a name="scale-media-processing"></a>Processamento de mídia de escala
Para alterar o tipo de unidade reservada e o número de unidades reservadas, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Unidades reservadas de mídia**.
   
    Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionado, use o controle deslizante **Unidades Reservadas de Mídia** no canto superior da tela.
   
    Para alterar o **TIPO DE UNIDADE RESERVADA**, clique na barra **Velocidade das unidades reservadas de processamento**. Em seguida, selecione o tipo de preço de que precisa: S1, S2 ou S3.
   
3. Pressione o botão SALVAR para salvar as alterações.
   
    As novas unidades reservadas são alocadas quando você pressiona SALVAR.

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
