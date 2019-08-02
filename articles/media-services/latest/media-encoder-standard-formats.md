---
title: Codecs e formatos de codificador padrão-Azure
description: Este tópico fornece uma visão geral dos codecs e formatos de codificador padrão.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: d4bbc83a101ba6b110ec97d5107ba3f79dd10843
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870949"
---
# <a name="standard-encoder-formats-and-codecs"></a>Codecs e formatos de codificador padrão

Este artigo contém uma lista dos formatos de arquivo de importação e exportação mais comuns que você pode usar com [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar predefinições personalizadas usando **StandardEncoderPreset**, veja [Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada

| Formatos de arquivo (extensões de arquivo) | Com suporte |
| --- | --- |
| FLV (com codecs H.264 e AAC) (.flv) |Sim |
| MXF    (.mxf) |Sim |
| GXF    (.gxf) |Sim |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Sim |
| Vídeo do Windows Media (WMV)/ASF (.wmv, .asf) |Sim |
| AVI (8 bits/10 bits descompactado) (.avi) |Sim |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Sim |
| [Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (.wav) |Sim |
| QuickTime (.mov) |Sim |

> [!NOTE]
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em contêineres de entrada

O codificador padrão dá suporte à manutenção dos seguintes formatos de áudio em contêineres de entrada:

* Arquivos do MXF, GXF e QuickTime que têm faixas de áudio com exemplos em estéreo intercalado ou de 5.1

ou

* Arquivos MXF, GXF e QuickTime onde o áudio é executado como faixas PCM separadas, mas o mapeamento de canal (para estéreo ou 5.1) pode ser deduzido dos metadados do arquivo

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Com suporte |
| --- | --- |
| AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| DNxHD ávido (em MXF) |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |
| Vídeo digital (VD) (em arquivos AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Até perfil 422 |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |Não |
| MPEG-4, parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 descompactado, ou mezzanine |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Apple ProRes Proxy |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |
| HEVC/H.265| Perfil Principal|

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Com suporte |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |
| MPEG Layer 2 |Sim |
| MP3 (MPEG-1 Audio Layer 3) |Sim |
| Áudio do Windows Media |Sim |
| WAV/PCM |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |
| AMR (multitaxa adaptável) |Sim |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |
| Dolby® E |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (E-AC3) |Não |

## <a name="output-formats-and-codecs"></a>Codecs e formatos de saída
A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.

| Formato de arquivo | Codec de vídeo | Codec de áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo contêineres MP4 de múltiplas taxas de bits) |H.264 (Perfis Alto, Principal e Linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (Perfis Alto, Principal e Linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Próximas etapas

[Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md)
