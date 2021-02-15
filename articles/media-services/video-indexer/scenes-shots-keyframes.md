---
title: Video Indexer cenas, capturas e quadros-chave
titleSuffix: Azure Media Services
description: Este tópico fornece uma visão geral das Video Indexer cenas, capturas e quadros-chave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041899"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e quadros-chave

Video Indexer dá suporte ao segmentação de vídeos em unidades temporais com base nas propriedades estruturais e semânticas. Essa funcionalidade permite que os clientes naveguem, gerenciem e editem com facilidade seu conteúdo de vídeo com base em diferentes granularidades. Por exemplo, com base em cenas, capturas e quadros-chave, descritos neste tópico.   

![Cenas, capturas e quadros-chave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Detecção de cena  
 
Video Indexer determina quando uma cena é alterada em vídeo com base em indicações visuais. Uma cena descreve um único evento e é composta de uma série de capturas consecutivas, que são relacionadas semanticamente. Uma miniatura de cena é o primeiro quadro-chave de sua captura subjacente. O video indexer segmenta um vídeo em cenas com base em coerência de cores em capturas consecutivas e recupera a hora de início e de término de cada cena. A detecção de cena é considerada uma tarefa desafiadora, pois envolve a quantificação de aspectos semânticos de vídeos.

> [!NOTE]
> Aplicável a vídeos que contêm pelo menos 3 cenas.

## <a name="shot-detection"></a>Detecção de captura

Video Indexer determina quando uma captura é alterada no vídeo com base em indicações visuais, rastreando transições abruptas e graduais no esquema de cores de quadros adjacentes. Os metadados da captura incluem uma hora de início e de término, bem como a lista de quadros-chave incluídos nessa captura. As capturas são quadros consecutivos tirados da mesma câmera ao mesmo tempo.

## <a name="keyframe-detection"></a>Detecção de quadro-chave

Video Indexer seleciona os quadros que melhor representam cada imagem. Quadros-chave são os quadros representativos selecionados de todo o vídeo com base em Propriedades estética (por exemplo, contraste e capacidade de ativação). Video Indexer recupera uma lista de IDs de quadro-chave como parte dos metadados da captura, com base nas quais os clientes podem extrair o quadro-chave como uma imagem de alta resolução.  

### <a name="extracting-keyframes"></a>Extraindo quadros-chave

Para extrair quadros-chave de alta resolução para seu vídeo, você deve primeiro carregar e indexar o vídeo.

![Quadros chave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Com o site Video Indexer

Para extrair quadros-chave usando o site Video Indexer, carregue e indexe seu vídeo. Depois que o trabalho de indexação for concluído, clique no botão **baixar** e selecione **artefatos (zip)** . Isso baixará a pasta de artefatos em seu computador. 

![Captura de tela que mostra o menu suspenso "baixar" com "artefatos" selecionado.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Descompacte e abra a pasta. Na pasta *_KeyframeThumbnail* , você encontrará todos os quadros-chave extraídos do seu vídeo. 

#### <a name="with-the-video-indexer-api"></a>Com a API de Video Indexer

Para obter quadros-chave usando a API Video Indexer, carregue e indexe seu vídeo usando a chamada [carregar vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) . Quando o trabalho de indexação estiver concluído, chame [obter índice de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Isso fornecerá a você todas as informações que Video Indexer extraídas do seu conteúdo em um arquivo JSON.  

Você obterá uma lista de IDs de quadro-chave como parte dos metadados de cada captura. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Agora, você precisará executar cada uma dessas IDs de quadro-chave na chamada [obter miniaturas](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) . Isso baixará cada uma das imagens de quadro-chave em seu computador. 

## <a name="editorial-shot-type-detection"></a>Detecção de tipo editorial shot

Os quadros-chave são associados a capturas no JSON de saída. 

O tipo de captura associado a uma captura individual no JSON do insights representa seu tipo editorial. Você pode encontrar essas características de tipo de captura úteis ao editar vídeos em clipes, trailers ou ao pesquisar um estilo específico de quadro-chave para fins artísticos. Os diferentes tipos são determinados com base na análise do primeiro quadro-chave de cada captura. As capturas são identificadas pela escala, pelo tamanho e pelo local das faces que aparecem no primeiro quadro-chave. 

O tamanho e a escala da captura são determinados com base na distância entre a câmera e as faces que aparecem no quadro. Usando essas propriedades, Video Indexer detecta os seguintes tipos de captura:

* Largo: mostra o corpo de uma pessoa inteira.
* Médio: mostra o corpo superior e a face de uma pessoa.
* Fechar: mostra principalmente a face de uma pessoa.
* Close-up extremo: mostra a aparência de uma pessoa que está preenchendo a tela. 

Os tipos de captura também podem ser determinados pelo local dos caracteres de assunto em relação ao centro do quadro. Essa propriedade define os seguintes tipos de captura no Video Indexer:

* Face para a esquerda: uma pessoa aparece no lado esquerdo do quadro.
* Face central: uma pessoa aparece na região central do quadro.
* Face direita: uma pessoa aparece no lado direito do quadro.
* Externamente: uma pessoa aparece em uma configuração externa.
* Interno: uma pessoa aparece em uma configuração de interno.

Características adicionais:

* Duas capturas: mostra as faces de duas pessoas de tamanho médio.
* Vários rostos: mais de duas pessoas.


## <a name="next-steps"></a>Próximas etapas

[Examinar a saída de Video Indexer produzida pela API](video-indexer-output-json-v2.md#scenes)
