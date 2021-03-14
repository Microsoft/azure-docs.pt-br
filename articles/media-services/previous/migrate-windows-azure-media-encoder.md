---
title: Migrar do codificador de mídia do Windows Azure para Media Encoder Standard | Microsoft Docs
description: Este tópico discute como migrar do codificador de mídia do Windows Azure para o processador de mídia Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a80d25145162c81ef999f8af1015cd590d5a090
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011112"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrar do codificador de mídia do Windows Azure para Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo discute as etapas para migrar do processador de mídia herdado do Windows Azure Media Encoder (WAME) (que está sendo desativado) para o processador de mídia Media Encoder Standard. Para ver as datas de desativação, consulte o tópico de [componentes herdados](legacy-components.md).

Ao codificar arquivos com WAME, os clientes normalmente usaram uma cadeia de caracteres predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p` . Para migrar, seu código precisa ser atualizado para usar o processador de mídia **Media Encoder Standard** em vez de WAME, e uma das [predefinições](media-services-mes-presets-overview.md) equivalentes do sistema, como `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrando para o Media Encoder Standard

Aqui está um exemplo típico de código C# que usa o componente herdado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Aqui está a versão atualizada que usa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Cenários avançados 

Se você tiver criado sua própria predefinição de codificação para WAME usando seu esquema, haverá um [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Diferenças conhecidas 

O Media Encoder Standard é mais robusto, confiável, tem um desempenho melhor e produz uma saída de qualidade melhor do que o codificador WAME herdado. Além disso: 

* Media Encoder Standard produz arquivos de saída com uma Convenção de nomenclatura diferente de WAME.
* Media Encoder Standard produz artefatos como arquivos que contêm os [metadados do arquivo de entrada](media-services-input-metadata-schema.md) e os metadados dos arquivos de [saída](media-services-output-metadata-schema.md).
* Conforme documentado na [página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente na seção de perguntas frequentes), ao codificar vídeos usando Media Encoder Standard, você será cobrado com base na duração dos arquivos produzidos como saída. Com o WAME, você será cobrado com base nos tamanhos dos arquivos de vídeo de entrada e nos arquivos de vídeo de saída.

## <a name="need-help"></a>Precisa de ajuda?

Abra um tíquete de suporte navegando até [Nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Próximas etapas

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
