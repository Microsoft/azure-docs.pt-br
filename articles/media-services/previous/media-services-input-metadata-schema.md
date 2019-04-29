---
title: Esquema de metadados de entrada dos Serviços de Mídia do Azure | Microsoft Docs
description: O tópico oferece uma visão geral do estema de metadados de entrada dos Serviços de Mídia do Azure.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: fa4487b07f130947ac5da2a5dbae6776b06acbe7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463762"
---
# <a name="input-metadata"></a>Metadados de entrada 

Um trabalho de codificação é associado um ativo (ou ativos) de entrada no qual você deseja executar algumas tarefas de codificação.  Após a conclusão de uma tarefa, um ativo de saída é produzido.  O ativo de saída contém vídeo, áudio, miniaturas, manifesto etc. O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: &lt;asset_id&gt;_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), onde &lt;asset_id&gt; é o valor de AssetId do ativo de entrada.  

Os Serviços de Mídia não analisam preventivamente os ativos de entrada para gerar metadados. Os metadados de entrada são gerados apenas como um artefato quando um ativo de entrada é processado em um trabalho. Portanto, este artefato é gravado no ativo de saída. Diversas ferramentas são usadas para gerar metadados para ativos de entrada e ativos de saída. Portanto, os metadados de entrada têm um esquema um pouco diferente dos metadados de saída.

Se desejar examinar o arquivo de metadados, você poderá criar um localizador **SAS** e baixar o arquivo em seu computador local. É possível encontrar um exemplo de como criar um localizador SAS e baixar um arquivo em [Usando as Extensões do SDK do .NET para os Serviços de Mídia](media-services-dotnet-get-started.md).  

Este artigo discute os elementos e os tipos do esquema XML no qual os metadados de entrada (&lt;asset_id&gt;_metadata.xml) se baseiam.  Para saber mais sobre o arquivo que contém metadados sobre o ativo de saída, veja [Metadados de saída](media-services-output-metadata-schema.md).  

Você pode encontrar o [Código de Esquema](media-services-input-metadata-schema.md#code) e um [Exemplo de XML](media-services-input-metadata-schema.md#xml) no final deste artigo.  
 

## <a name="AssetFiles"></a> Elemento AssetFiles (elemento raiz)
Contém uma coleção de [elementos AssetFile](media-services-input-metadata-schema.md#AssetFile) para o trabalho de codificação.  

Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

| NOME | DESCRIÇÃO |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Um único elemento filho. Para saber mais, veja [Elemento AssetFile](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> Elemento AssetFile
 Contém atributos e elementos que descrevem um arquivo de ativo.  

 Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Nome**<br /><br /> Obrigatório |**xs:string** |Nome do arquivo de ativo. |
| **Tamanho**<br /><br /> Obrigatório |**xs:long** |Tamanho do arquivo de ativo em bytes. |
| **Duração**<br /><br /> Obrigatório |**xs:duration** |Duração da reprodução de conteúdo. Exemplo: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Obrigatório |**xs:int** |Número de fluxos no arquivo de ativo. |
| **FormatNames**<br /><br /> Obrigatório |**xs: string** |Nomes de formato. |
| **FormatVerboseNames**<br /><br /> Obrigatório |**xs: string** |Nomes detalhados de formato. |
| **StartTime** |**xs:duration** |Hora de início do conteúdo. Exemplo: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Taxa de bits média do arquivo de ativo em kbps. |

> [!NOTE]
> Os quatro elementos filhos a seguir devem aparecer em uma sequência.  
> 
> 

### <a name="child-elements"></a>Elementos filho
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Programas**<br /><br /> minOccurs="0" | |A coleção de todos os [elementos Programs](media-services-input-metadata-schema.md#Programs) quando o arquivo de ativo está no formato MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativo físico pode conter nenhuma ou mais faixas de vídeos intercaladas em um formato de contêiner apropriado. Esse elemento contém uma coleção de todas as [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) que fazem parte do arquivo de ativo. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Cada arquivo de ativo físico pode conter nenhuma ou mais faixas de áudio intercaladas em um formato de contêiner apropriado. Esse elemento contém uma coEsse elemento contém uma coleção de todas as [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) que fazem parte do arquivo de ativo. |
| **Metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadados do arquivo de ativo representados como cadeias de caracteres de chave\valor. Por exemplo: <br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Id**<br /><br /> Obrigatório |**xs:int** |Índice baseado em zero da faixa de áudio ou de vídeo.<br /><br /> Essa não é necessariamente a TrackID como usada em um arquivo MP4. |
| **Codec** |**xs:string** |Cadeia de caracteres de codec de faixa de vídeo. |
| **CodecLongName** |**xs: string** |Nome longo de codec de faixa de áudio ou vídeo. |
| **TimeBase**<br /><br /> Obrigatório |**xs:string** |Base de tempo. Exemplo: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Número de quadros (presentes em faixas de vídeo). |
| **StartTime** |**xs: duration** |Hora de início da faixa. Exemplo: StartTime="PT2.669S" |
| **Duração** |**xs:duration** |Duração da faixa. Exemplo: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> Os dois elementos filhos a seguir devem aparecer em uma sequência.  
> 
> 

### <a name="child-elements"></a>Elementos filho
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Disposição**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Contém informações de apresentação (por exemplo, se uma determinada faixa de áudio for para usuários com deficiência visual). |
| **Metadados**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |As cadeias de caracteres de chave/valor genéricas que podem ser usadas para armazenar uma variedade de informações. Por exemplo, key=”language” e value=”eng”. |

## <a name="AudioTrackType"></a> AudioTrackType (herda de TrackType)
 **AudioTrackType** é um tipo complexo global que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

 O tipo representa uma faixa de áudio específica no arquivo de ativo.  

 Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Formato de exemplo. |
| **ChannelLayout** |**xs: string** |Layout do canal. |
| **Canais**<br /><br /> Obrigatório |**xs:int** |Número (0 ou mais) de canais de áudio. |
| **SamplingRate**<br /><br /> Obrigatório |**xs:int** |Taxa de amostragem de áudio em amostras/s ou Hz. |
| **Bitrate** |**xs:int** |Taxa média de bits de áudio em bits por segundo, calculada com base no arquivo de ativo. Apenas a carga de fluxo elementar é contada, e a sobrecarga de empacotamento não está incluída nesta contagem. |
| **BitsPerSample** |**xs:int** |Bits por amostra para o tipo de formato wFormatTag. |

## <a name="VideoTrackType"></a> VideoTrackType (herda de TrackType)
**VideoTrackType** é um tipo complexo global que herda de [TrackType](media-services-input-metadata-schema.md#TrackType).  

O tipo representa uma faixa de vídeo específica no arquivo de ativo.  

Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **FourCC**<br /><br /> Obrigatório |**xs:string** |Código FourCC de codec de vídeo. |
| **Perfil** |**xs: string** |Perfil da faixa de vídeo. |
| **Level** |**xs: string** |Nível da faixa de vídeo. |
| **PixelFormat** |**xs: string** |Formato de pixel da faixa de vídeo. |
| **Largura**<br /><br /> Obrigatório |**xs:int** |Largura do vídeo codificado em pixels. |
| **Altura**<br /><br /> Obrigatório |**xs:int** |Altura do vídeo codificado em pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Obrigatório |**xs: double** |Numerador de taxa de proporção de exibição do vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Obrigatório |**xs:double** |Denominador de taxa de proporção de exibição do vídeo. |
| **DisplayAspectRatioDenominator**<br /><br /> Obrigatório |**xs: double** |Numerador de proporção de amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs: double** |Numerador de proporção de amostra de vídeo. |
| **SampleAspectRatioNumerator** |**xs:double** |Denominador de proporção de amostra de vídeo. |
| **FrameRate**<br /><br /> Obrigatório |**xs:decimal** |Medida de taxa de quadros de vídeo em formato .3f. |
| **Bitrate** |**xs:int** |Taxa média de bits de vídeo em quilobits por segundo, calculada desde o arquivo de ativo. Apenas a carga de fluxo elementar é contada, e a sobrecarga de empacotamento não está incluída. |
| **MaxGOPBitrate** |**xs: int** |Taxa de bits média do GOP máximo para esta faixa de vídeo em quilobits por segundo. |
| **HasBFrames** |**xs:int** |Número de faixas de vídeo de quadros B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** é um tipo global complexo que descreve os metadados de um arquivo de ativo como cadeias de caracteres de chave/valor. Por exemplo, key=”language” e value=”eng”.  

Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **chave**<br /><br /> Obrigatório |**xs:string** |A chave no par chave/valor. |
| **valor**<br /><br /> Obrigatório |**xs:string** |O valor do par chave/valor. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** é um tipo global complexo que descreve um programa.  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **ProgramId**<br /><br /> Obrigatório |**xs:int** |Id do Programa |
| **NumberOfPrograms**<br /><br /> Obrigatório |**xs:int** |Número de programas. |
| **PmtPid**<br /><br /> Obrigatório |**xs:int** |As PMTs (Tabelas de Mapa de Programa) contêm informações sobre programas.  Para saber mais, confira [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Obrigatório |**xs: int** |Usado pelo decodificador. Para saber mais, confira [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |Iniciando o carimbo de data/hora de apresentação. |
| **EndPTS** |**xs: long** |Hora de término do carimbo de data/hora de apresentação. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** é um tipo global complexo que descreve o fluxo.  

Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Padrão**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta é a apresentação padrão. |
| **Dub**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta é a apresentação dublada. |
| **Original**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta é a apresentação original. |
| **Comentário**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa contém comentários. |
| **Lyrics**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa contém letras. |
| **Karaoke**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que ele representa a faixa de karaokê (música em segundo plano, sem vocais). |
| **Forced**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta é a apresentação forçada. |
| **HearingImpaired**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa é para as pessoas que estão com dificuldades auditivas. |
| **VisualImpaired**<br /><br /> Obrigatório |**xs:int** |Defina esse atributo como 1 para indicar que esta faixa é para os deficientes visuais. |
| **CleanEffects**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta faixa tem efeitos limpos. |
| **AttachedPic**<br /><br /> Obrigatório |**xs: int** |Defina esse atributo como 1 para indicar que esta faixa contém fotos. |

## <a name="Programs"></a> Elemento Programs
Elemento de wrapper que contém vários elementos **Program**.  

### <a name="child-elements"></a>Elementos filho
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **Programa**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Para os arquivos de ativo no formato MPEG-TS, eles contêm informações sobre os programas no arquivo de ativo. |

## <a name="VideoTracks"></a> Elemento VideoTracks
 Elemento de wrapper que contém vários elementos **VideoTrack**.  

 Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementos filho
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (herdado de TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Contém informações sobre as faixas de vídeo no arquivo de ativo. |

## <a name="AudioTracks"></a> Elemento AudioTracks
 Elemento de wrapper que contém vários elementos **AudioTrack**.  

 Consulte um exemplo de XML no final deste artigo: [Exemplo de XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementos
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (herdado de TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Contém informações sobre as faixas de áudios no arquivo de ativo. |

## <a name="code"></a> Código de Esquema
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a> Exemplo de XML
Este é um exemplo do arquivo de metadados de entrada.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

