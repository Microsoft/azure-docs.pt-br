---
title: "Visão geral da colocação em escala do processamento de mídia | Microsoft Docs"
description: "Este tópico é uma visão geral do dimensionamento de processamento de mídia com os Serviços de Mídia do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 1009d8b87cb3a131909f3ae9ee231f9ebb408c04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-media-processing-overview"></a>Visão geral do dimensionamento do processamento de mídia
Esta página fornece uma visão geral de como e por que dimensionar o processamento de mídia. 

## <a name="overview"></a>Visão geral
Uma conta dos Serviços de Mídia está associada a um Tipo de Unidade Reservada que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**. Para obter mais informações, veja [Reserved Unit Types](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)(Tipos de unidade reservada).

Além de especificar o tipo de unidade reservada, você pode especificar o provisionamento da sua conta com unidades reservadas. O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas a serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolha entre tipos diferentes de unidade reservada
A tabela a seguir o ajudará a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Também apresenta alguns casos de parâmetro de comparação e fornece as URLs de SAS para baixar os vídeos para executar seus próprios testes:

| Cenários | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Exemplo de uso indicado |Codificação de taxa de bits única. <br/>Arquivos com resoluções SD ou inferiores, não sensível ao tempo, de baixo custo. |Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD. |Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Vídeos com resolução Full HD e 4K. Codificação urgente com retorno mais rápido. |
| Parâmetro de comparação |[Arquivo de entrada: 5 minutos de duração, 640x360p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>A codificação para um arquivo MP4 de taxa de bits única com a mesma resolução leva aproximadamente 11 minutos. |[Arquivo de entrada: 5 minutos de duração, 1280x720p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>A codificação com a predefinição "H264 Taxa de Bits Única 720p" leva cerca de 5 minutos.<br/><br/>A codificação com a predefinição "H264 Taxa de Bits Múltipla 720p" levará cerca de 11,5 minutos. |[Arquivo de entrada: 5 minutos de duração, 1920x1080p a 29,97 quadros/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>A codificação com a predefinição "H264 Taxa de Bits Única 1080p" leva cerca de 2,7 minutos.<br/><br/>A codificação com a predefinição "H264 Taxas de Bits Múltiplas 1080p" leva aproximadamente 5,7 minutos. |

## <a name="considerations"></a>Considerações
> [!IMPORTANT]
> Examine as considerações descritas nesta seção.  
> 
> 

* As Unidades Reservadas trabalham para paralelizar todo o processamento de mídia, incluindo trabalhos de indexação usando o Indexador de Mídia do Azure.  No entanto, ao contrário da codificação, a indexação de trabalhos não será processada mais rapidamente com unidades reservadas mais rápidas.
* Se estiver usando o pool compartilhado, ou seja, sem nenhuma unidade reservada, suas tarefas de codificação terão o mesmo desempenho do que com unidades reservadas S1. No entanto, não há nenhum limite superior para o tempo que as Tarefas podem gastar no estado na fila e, a qualquer momento específico, no máximo, apenas uma Tarefa será executada.
* Os data centers a seguir não oferecem o tipo de unidade reservada **S2**: Sul do Brasil e Índia Ocidental.
* Os data centers a seguir não oferecem o tipo de unidade reservada **S3**: Índia Ocidental.

## <a name="billing"></a>Cobrança

A cobrança é realizada com base no número real de minutos de uso das Unidades Reservadas de Mídia. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="quotas-and-limitations"></a>Cotas e limitações
Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Próxima etapa
Realize a tarefa de dimensionamento de processamento da mídia com uma dessas tecnologias: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

