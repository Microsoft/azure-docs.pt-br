---
title: "Análise de mídia na plataforma de Serviços de Mídia | Microsoft Docs"
description: "Visão geral da versão prévia pública da Análise de Mídia, uma coleção de serviços de fala e de pesquisa visual computacional de nível empresarial, de conformidade, segurança e alcance global"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: c0bbe6f80370515fa783b12757434897fe2221b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="media-analytics-on-the-media-services-platform"></a>Análise de Mídia na plataforma de Serviços de Mídia
## <a name="overview"></a>Visão geral
Mais organizações estão usando vídeos como o meio preferido de treinar seus funcionários, contatar seus clientes e documentar suas funções comerciais. A computação em nuvem fornece uma maneira de armazenar, transmitir e acessar esses grandes arquivos de mídia. Contudo, à medida que a biblioteca de uma empresa de conteúdo de vídeo cresce, ela precisa de uma forma igualmente eficaz de extrair de informações do conteúdo. 

Para atender a essa necessidade cada vez maior, os Serviços de Mídia do Azure oferecem a Análise de Mídia do Azure. A Análise de Mídia é uma coleção de componentes de fala e pesquisa visual que facilitam a obtenção de análises acionáveis dos arquivos de vídeo de organizações e de empresas. Criado usando os componentes da plataforma principal dos Serviços de Mídia, a Análise de Mídia pode lidar com processamento de mídia em grande escala desde o primeiro dia.

Com a Análise de Mídia, os desenvolvedores podem trazer rapidamente funcionalidades de vídeo avançadas para aplicativos. Ela fornece aos ambientes empresariais toda a escala, conformidade, segurança e alcance global necessários para grandes organizações.

O diagrama a seguir mostra a Análise de Mídia e outras partes importantes da plataforma de Serviços de Mídia. 

![Fluxo de trabalho VoD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

O processador de mídia da Análise de Mídia produz arquivos MP4 ou arquivos JSON. Se um processador de mídia produzir um arquivo MP4, você poderá baixar o arquivo progressivamente. Se um processador de mídia produzir um arquivo JSON, você poderá baixar o arquivo do Armazenamento de Blobs do Azure. 

## <a name="media-analytics-services"></a>Serviços de Análise de Mídia

### <a name="indexer"></a>Indexador
Com o Azure Media Indexer, você pode tornar o conteúdo pesquisável e gerar faixas de legendagem. Comparada à versão anterior, a Versão Prévia do Azure Media Indexer 2 oferece indexação mais rápida e suporte mais amplo a idiomas. Dentre os idiomas com suporte estão: inglês, espanhol, francês, alemão, italiano, chinês, português e árabe. Para obter informações detalhadas e exemplos, consulte [Processar vídeos com o Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
O Microsoft Hyperlapse combina estabilização do vídeo e funcionalidades de lapso de tempo para criar vídeos rápidos e consumíveis do seu conteúdo longo. Além de criar vídeos de lapso de tempo, você também pode usar o Hyperlapse para criar vídeos estáveis de vídeos tremidos capturados com telefones celulares e câmeras de vídeo. Para ver informações detalhadas e exemplos, consulte [Arquivos de Mídia do Hyperlapse com o Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Detector de Movimento
É possível usar o Motion Detector para detectar movimento em um vídeo com segundo plano fixo. Isso torna possível verificar se há falsos positivos em eventos de movimento detectados por câmeras de vigilância. Para obter informações detalhadas e exemplos, consulte [Detecção de movimento para Análise de Mídia do Azure](media-services-motion-detection.md).
### <a name="face-detector"></a>Detector Facial
Usando o Face Detector, é possível detectar as faces das pessoas e suas emoções, incluindo felicidade, tristeza e surpresa. Isso tem várias aplicações úteis na indústria, descritas abaixo, incluindo agregar e analisar reações de pessoas participando de um evento. Para obter informações detalhadas e exemplos, consulte [Detecção facial e de emoções da Análise de Mídia do Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Resumo de vídeo
O resumo de vídeo pode ajudá-lo a criar resumos de vídeos de longa duração com a seleção automática de trechos interessantes do vídeo de origem. Essa habilidade é útil quando você desejar fornecer uma visão geral rápida do que esperar de um vídeo de longa duração. Para obter informações detalhadas e exemplos, consulte [Usar Azure Media Video Thumbnails para criar um resumo de vídeo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Reconhecimento de caractere óptico
O OCR (reconhecimento óptico de caracteres) de Mídia do Azure permite que você converta o conteúdo de texto de arquivos de vídeo em texto digital editável e pesquisável. Você poderá então automatizar a extração de metadados significativos do sinal de vídeo de sua mídia.
### <a name="scalable-face-redaction"></a>Edição facial escalonável
O Azure Media Redactor é um processador de mídia da Análise de Mídia do Azure que oferece edição facial escalonável na nuvem. Usar a edição facial permite modificar seu vídeo para desfocar rostos de pessoas selecionadas. Pode ser útil usar o serviço de edição facial em cenários jornalísticos ou de segurança pública. Alguns minutos de vídeo que contém vários rostos podem levar horas para serem editados manualmente, contudo, com esse serviço, o processo de edição facial exigirá apenas algumas etapas simples. Para obter mais informações, consulte o artigo [Edição facial com a Análise de Mídia do Azure](media-services-face-redaction.md).

## <a name="common-scenarios"></a>Cenários comuns
A Análise de Mídia pode ajudar as organizações e empresas a obter novas informações de vídeos e gerenciar grandes volumes de conteúdo de vídeo com mais eficácia. Veja os diversos cenários a seguir:

* **Call centers**. Mesmo com o advento da mídia social, os call centers de clientes ainda recebem um grande percentual de transações de serviço de atendimento ao cliente. Codificado nesses dados de áudio há uma grande quantidade de informações do cliente que podem ser analisadas para alcançar uma satisfação do cliente ainda maior. Usando o Indexador de Mídia, as organizações podem extrair texto e criar índices de pesquisa e painéis. Em seguida, elas podem extrair inteligência sobre reclamações comuns, fontes de reclamações e outros dados relevantes.
* **Moderação de conteúdo gerado pelo usuário**. Desde os meios de comunicação até as delegacias de polícia, muitas organizações têm portais voltados ao público que aceitam mídia gerada pelo usuário, como vídeos e imagens. O volume de conteúdo pode ter picos devido a eventos inesperados. Nesses cenários, fica difícil conduzir uma revisão manual eficaz do conteúdo em relação à adequação. Os clientes podem contar com o serviço de moderação de conteúdo para se concentrar no conteúdo apropriado.
* **Vigilância**. Com o crescimento do uso de câmeras IP surgiu um inventário crescente de vídeos de vigilância. Revisar manualmente o vídeo de vigilância consome muito tempo e está sujeito a erros humanos. A Análise de Mídia fornece serviços como detecção de movimento, detecção facial e Hyperlapse para facilitar o processo de revisão, gerenciamento e criação de derivados.

## <a name="media-analytics-media-processors"></a>Processadores de mídia da Análise de Mídia
Esta seção lista todos os processadores de mídia da Análise de Mídia e mostra como usar .NET ou REST para obter um objeto de processador de mídia (MP).

### <a name="mp-names"></a>Nomes dos MP
* Preview do Indexador de Mídia do Azure 2
* Indexador de Mídia do Azure
* Azure Media Hyperlapse
* Detector de Rostos em Mídias do Azure
* Detector de Movimento em Mídias do Azure
* Miniaturas de Vídeo de Mídia do Azure
* OCR de Mídia do Azure

### <a name="net"></a>.NET
A função a seguir usa um dos nomes de MP especificados e retorna um objeto MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Solicitação:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Resposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demonstrações
Consulte [Demonstrações da Análise de Mídia do Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
Consulte o [Comunicado da análise dos Serviços de Mídia](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
