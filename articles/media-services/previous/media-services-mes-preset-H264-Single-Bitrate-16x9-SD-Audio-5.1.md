---
title: H264 Taxa de Bits Única 16x9 SD Audio 5.1 | Microsoft Docs
description: O tópico fornece uma visão geral da predefinição de tarefa **H264 Taxa de Bits Única 16x9 SD Audio 5.1**.
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d57cd39f-60e8-4248-a21d-c5b9f096b0ce
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/19/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: 730854f572e1e2ab21140f53d59b7593ad26cb05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463592"
---
# <a name="h264-single-bitrate-16x9-sd-audio-51"></a>H264 Taxa de Bits Única 16x9 SD Audio 5.1
`Media Encoder Standard` define um conjunto de predefinições de codificação que pode ser usado ao criar trabalhos de codificação. Você pode usar um `preset name` para especificar em qual formato deseja codificar o arquivo de mídia. Ou, pode criar suas próprias predefinições com base em JSON ou XML (usando a codificação UTF-8 ou UTF-16). Em seguida, você passaria a predefinição personalizada ao codificador. Para obter a lista de todos os nomes de predefinição com suporte por este codificador `Media Encoder Standard`, consulte [Predefinições de tarefa para o Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Este tópico mostra a predefinição `H264 Single Bitrate 16x9 SD Audio 5.1` no formato XML e JSON.  
  
 Essa predefinição produz um único arquivo MP4 com uma taxa de bits de 2200 kbps e áudio AAC 5.1. Para obter informações detalhadas sobre o perfil, taxa de bits, taxa de amostragem etc. dessa predefinição, examine o XML ou JSON definido abaixo. Para obter explicações sobre o significado de cada elemento, e os valores válidos para cada elemento, veja [Esquema do Media Encoder Standard](media-services-mes-schema.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>2200</Bitrate>  
          <Width>848</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>2200</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 2200,  
          "MaxBitrate": 2200,  
          "BufferWindow": "00:00:05",  
          "Width": 848,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
