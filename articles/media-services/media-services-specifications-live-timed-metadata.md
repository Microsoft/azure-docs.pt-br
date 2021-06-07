---
title: Serviços de mídia do Azure – sinalizando metadados cronometrados na transmissão ao vivo
description: Esta especificação descreve métodos para sinalizar metadados cronometrados ao ingerir e transmitir para os serviços de mídia do Azure. Isso inclui o suporte a ID3 (sinais de metadados cronometrados) genéricos, bem como a sinalização de SCTE-35 para a inserção do anúncio e a sinalização de condição de União.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336387"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>A sinalização atingiu o tempo de metadados na transmissão ao vivo 

Última atualização: 2019-08-22

### <a name="conformance-notation"></a>Notação de conformidade

As palavras-chave “DEVE”, “NÃO DEVE”, “OBRIGATÓRIO”, “RECOMENDADO”, “PODE” E “OPCIONAL” neste documento devem ser interpretadas conforme descritas na RFC 2119

## <a name="1-introduction"></a>1. Introdução 

Para sinalizar a inserção de anúncios ou eventos de metadados personalizados em um player de cliente, os difusores geralmente fazem uso de metadados cronometrados inseridos no vídeo. Para habilitar esses cenários, os serviços de mídia fornecem suporte para o transporte de metadados cronometrados do ponto de ingestão do canal de transmissão ao vivo para o aplicativo cliente.
Esta especificação descreve vários modos com suporte dos serviços de mídia para metadados cronometrados em sinais de transmissão ao vivo.

1. [SCTE-35] sinalização que está em conformidade com os padrões descritos por [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] sinalização que está em conformidade com a especificação herdada [Adobe-Primetime] para sinalização de anúncio RTMP.
   
3. Um modo de sinalização de metadados cronometrado genérico, para mensagens que **não** são [SCTE-35] e podem carregar [ID3v2] ou outros esquemas personalizados definidos pelo desenvolvedor de aplicativos.

## <a name="11-terms-used"></a>1,1 termos usados

| Termo                | Definição                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Intervalo de anúncio            | Um local ou ponto no tempo em que um ou mais anúncios podem ser agendados para entrega; o mesmo que a oportunidade de posicionamento e disp.                                                                                                                     |
| Serviço de decisão do AD | serviço externo que decide quais AD (s) e durações serão mostrados para o usuário. Os serviços normalmente são fornecidos por um parceiro e estão fora do escopo deste documento.                                                                    |
| Marcar                 | Indicação de tempo e parâmetros do próximo intervalo de anúncio. Observe que as indicações podem indicar uma mudança pendente para um intervalo de anúncio, pendente alternar para o próximo anúncio dentro de um intervalo de anúncio e a mudança pendente de um intervalo de anúncio para o conteúdo principal.           |
| Empacotador            | O "ponto de extremidade de streaming" dos serviços de mídia do Azure fornece recursos de empacotamento dinâmico para DASH e HLS e é chamado de "Packager" no setor de mídia.                                                                              |
| Tempo da apresentação   | O horário em que um evento é apresentado em um espectador. O horário representa o momento na linha do tempo da mídia em que um espectador vê o evento. Por exemplo, a hora de apresentação de uma mensagem de comando splice_info() SCTE-35 é o splice_time(). |
| Horário de chegada        | O horário em que uma mensagem de evento chega. O horário é normalmente diferente do horário da apresentação do evento, pois mensagens de evento são enviadas antes do horário da apresentação do evento.                                                    |
| Faixa esparsa        | Faixa de mídia que não é contínua e é sincronizada por horário com um pai ou faixa de controle.                                                                                                                                                  |
| Origem              | O serviço de Streaming de Mídia do Azure                                                                                                                                                                                                             |
| Coletor do canal        | O serviço de Live Streaming de Mídia do Azure                                                                                                                                                                                                        |
| HLS                 | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                                            |
| DASH                | Dynamic Adaptive Streaming Over HTTP                                                                                                                                                                                                          |
| Smooth              | Protocolo Smooth Streaming                                                                                                                                                                                                                     |
| MPEG2-TS            | Fluxos de Transporte de MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Protocolo de multimídia em tempo real                                                                                                                                                                                                                 |
| uimsbf              | Inteiro sem sinal, bit mais significativo primeiro.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 referências de normativas

Os documentos a seguir contêm provisões, que, por meio de referência neste texto, constituem as disposições deste documento. Todos os documentos estão sujeitos à revisão pelos órgãos de padrões, e os leitores são incentivados a investigar a possibilidade de aplicar as edições mais recentes dos documentos listados abaixo. Os leitores também são lembrados de que as edições mais recentes dos documentos referenciados podem não ser compatíveis com esta versão da especificação de metadados cronometrada para os serviços de mídia do Azure.


| Standard          | Definição                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Especificação de sinalização de inserção de programa digital Primetime 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Referência de linguagem FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| AMF0            | ["Formato da mensagem de ação AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Orientação de interoperabilidade do fórum do setor v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadados cronometrados para HTTP Live Streaming- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadados cronometrados no formato de aplicativo de mídia comum (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | 2.4.0 de versão da marca ID3  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: parte 12 formato de arquivo de mídia de base ISO, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Tecnologia da informação – streaming adaptável dinâmico sobre HTTP (DASH)--parte 1: Descrição da apresentação de mídia e formatos de segmento. Maio de 2014. Checked. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Tecnologia da informação--formato de aplicativo de multimídia (MPEG-A)--parte 19: formato de aplicativo de mídia comum (CMAF) para mídia segmentada. Janeiro de 2018. Checked. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologia da informação – tecnologias de sistemas MPEG--parte 7: criptografia comum em arquivos de formato de arquivo de mídia base ISO. Fevereiro de 2016. Checked. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 de maio de 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-ingestão]  | [Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. Maio. HTTP Live Streaming. Agosto de 2017. Informativa. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| RFC4648         | As codificações de dados para base16, Base32 e Base64- [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Protocolo de mensagens Real-Time da Adobe", 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-mensagem de advertência de inserção de programa digital para cabo- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH para serviços de cabo IP-Based parte 1: restrições e extensões do MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH para IP-Based serviços de cabo parte 3: perfil DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – agendamento de eventos e interface de notificação                                                                                                                                                  |
| [SCTE-250]        | API de gerenciamento de evento e sinalização (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. ingestão de metadados cronometrado

Os serviços de mídia do Azure dão suporte a metadados em tempo real para os protocolos [RTMP] e Smooth Streaming [MS-SSTR-ingestão]. Os metadados em tempo real podem ser usados para definir eventos personalizados, com seus próprios esquemas personalizados exclusivos (JSON, binário, XML), bem como formatos definidos pelo setor, como ID3 ou SCTE-35 para a sinalização de anúncios em um fluxo de difusão. 

Este artigo fornece detalhes sobre como enviar sinais de metadados cronometrados personalizados usando os protocolos de ingestão com suporte dos serviços de mídia do Azure. O artigo também explica como os manifestos para HLS, DASH e Smooth Streaming são decorados com os sinais de metadados cronometrados, bem como como eles são inseridos em banda quando o conteúdo é entregue usando os segmentos CMAF (fragmentos MP4) ou TS (fluxo de transporte) para HLS. 

Cenários de caso de uso comuns para metadados cronometrados incluem:

 - Sinais do SCTE-35 ad para disparar quebras de anúncio em um evento ao vivo ou difusão linear
 - Metadados ID3 personalizados que podem disparar eventos em um aplicativo cliente (navegador, iOS ou Android)
 - Metadados JSON, binários ou XML definidos personalizados para disparar eventos em um aplicativo cliente
 - Telemetria de um codificador ao vivo, câmera de IP ou drone
 - Eventos de uma câmera de IP como movimento, detecção facial, etc.
 - Informações de posição geográfica de uma câmera de ação, drone ou mover dispositivo
 - Músicas de música
 - Limites do programa em um feed dinâmico linear
 - Imagens ou metadados incrementados a serem exibidos em um feed ao vivo
 - Pontuações esportivas ou informações do relógio do jogo
 - Pacotes de anúncios interativos a serem exibidos junto com o vídeo no navegador
 - Testes ou pesquisas
  
Os eventos ao vivo e o empacotador dos serviços de mídia do Azure são capazes de receber esses sinais de metadados cronometrados e convertê-los em um fluxo de metadados que podem acessar aplicativos cliente usando protocolos baseados em padrões, como HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>2,1 metadados em tempo hábil de RTMP

O protocolo [RTMP] permite que os sinais de metadados cronometrados sejam enviados para vários cenários, incluindo metadados personalizados e sinais de anúncio SCTE-35. 

Os sinais de anúncio (mensagens de indicação) são enviados como [AMF0] indicações de mensagens inseridas no fluxo [RTMP]. As mensagens de indicação podem ser enviadas algum tempo antes do evento real ou do sinal de junção do AD [SCTE35] precisar ocorrer. Para dar suporte a esse cenário, o carimbo de data/hora de apresentação real do evento é enviado dentro da mensagem de indicação. Para obter mais informações, consulte [AMF0].

Os seguintes comandos [AMF0] têm suporte dos serviços de mídia do Azure para ingestão RTMP:

- **onUserDataEvent** -usado para metadados personalizados ou [ID3v2] metadados cronometrados
- **onAdCue** – usado principalmente para sinalizar uma oportunidade de posicionamento de anúncio na transmissão ao vivo. Há suporte para duas formas da indicação, um modo simples e um modo "SCTE-35". 
- **onCuePoint** – com suporte em determinados codificadores de hardware locais, como o codificador ao vivo elementar, para sinalizar mensagens [SCTE35]. 
  

A tabela a seguir descreve o formato da carga da mensagem AMF que os serviços de mídia ingerirão para os modos de mensagem "simples" e [SCTE35].

O nome da mensagem [AMF0] pode ser usado para diferenciar vários fluxos de eventos do mesmo tipo.  Para as mensagens [SCTE-35] e o modo "simples", o nome da mensagem AMF deve ser "onAdCue", conforme necessário na especificação [Adobe-Primetime].  Os campos não listados abaixo devem ser ignorados pelos serviços de mídia do Azure em ingestão.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP com metadados personalizados usando "onUserDataEvent"

Se você quiser fornecer feeds de metadados personalizados de seu codificador upstream, câmera de IP, drone ou dispositivo usando o protocolo RTMP, use o tipo de comando de mensagem de dados "onUserDataEvent" [AMF0].

O comando de mensagem de dados **"onUserDataEvent"** deve transportar uma carga de mensagem com a seguinte definição a ser capturada pelos serviços de mídia e empacotada no formato de arquivo em banda, bem como os manifestos para HLS, DASH e Smooth streaming.
É recomendável enviar mensagens de metadados expiradas com mais frequência do que uma vez a cada 0,5 segundos (500 MB) ou problemas de estabilidade com o fluxo ao vivo podem ocorrer. Cada mensagem pode agregar metadados de vários quadros se você precisar fornecer metadados em nível de quadro. Se você estiver enviando fluxos de múltiplas taxas de bits, é recomendável que você também forneça os metadados em uma única taxa de bits apenas para reduzir a largura de banda e evitar interferência com processamento de vídeo/áudio. 

A carga para **"onUserDataEvent"** deve ser uma mensagem de formato XML [MPEGDASH] EventStream. Isso facilita a passagem de esquemas definidos personalizados que podem ser transportados em cargas de "EMSG" em banda para conteúdo CMAF [MPEGCMAF] que é entregue por meio de protocolos HLS ou DASH. Cada mensagem de fluxo de evento de TRACEJADO contém um schemeIdUri que funciona como um identificador de esquema de mensagem do URN e define a carga da mensagem. Alguns esquemas, como " https://aomedia.org/emsg/ID3 " para [ID3v2] ou **urn: SCTE: scte35:2013: bin** para [SCTE-35], são padronizados por consortia do setor para interoperabilidade. Qualquer provedor de aplicativo pode definir seu próprio esquema personalizado usando uma URL que eles controlam (domínio de propriedade) e podem fornecer uma especificação nessa URL se escolherem. Se um jogador tiver um manipulador para o esquema definido, esse será o único componente que precisa entender a carga e o protocolo.

O esquema para a carga XML de EventStream [MPEG-DASH] é definido como (trecho do traço ISO-IEC-23009-1-3ª edição). Observe que apenas um "EventType" por "EventStream" tem suporte no momento. Somente o primeiro elemento de **evento** será processado se vários eventos forem fornecidos no **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Fluxo de eventos XML de exemplo com ID de esquema ID3 e carga de dados codificada em base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Fluxo de eventos de exemplo com ID de esquema personalizado e dados binários codificados em base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Fluxo de eventos de exemplo com ID de esquema personalizado e JSON personalizado  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>URIs de ID de esquema com suporte interno
| URI da ID do esquema                 | Descrição                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/EMSG/ID3 | Descreve como os metadados [ID3v2] podem ser transportados como metadados cronometrados em um MP4 fragmentado [MPEGCMAF] compatível com CMAF]. Para obter mais informações, consulte os [metadados cronometrados no formato de aplicativo de mídia comum (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Processamento de eventos e sinalização de manifesto

Ao receber um evento **"onUserDataEvent"** válido, os serviços de mídia do Azure procurarão uma carga XML válida que corresponda ao EventStreamType (definido em [MPEGDASH]), analisará a carga XML e convertê-la em uma caixa [MPEGCMAF] MP4 fragmento ' EMSG ' versão 1 para armazenamento no arquivo em tempo real e transmitindo para o Media Services Packager.   O empacotador detectará a caixa ' EMSG ' na transmissão ao vivo e:

- (a) "empacotar dinamicamente" em segmentos TS para entrega a clientes HLS em conformidade com a especificação de metadados Timed HLS [HLS-TMD] ou
- (b) passá-lo para entrega em fragmentos de CMAF por meio de HLS ou DASH, ou 
- (c) convertê-lo em um sinal de faixa esparsa para entrega via Smooth Streaming [MS-SSTR].

Além do formato "EMSG" em banda CMAF ou dos pacotes de TS PES para HLS, os manifestos para DASH (MPD) e Smooth Streaming conterá uma referência para os fluxos de eventos em banda (também conhecido como faixa de fluxo esparsa no Smooth Streaming).

Eventos individuais ou suas cargas de dados não são gerados diretamente nos manifestos HLS, traço ou Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Restrições informativas adicionais e padrões para eventos onUserDataEvent

- Se a escala de TIMESCALE não estiver definida no elemento EventStream, a escala de TIMESCALE de 1 kHz RTMP será usada por padrão
- A entrega de uma mensagem onUserDataEvent é limitada a uma vez a cada 500 ms. Se você enviar eventos com mais frequência, ele poderá afetar a largura de banda e a estabilidade do feed ao vivo

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>sinalização de indicação de anúncio RTMP 2.1.2 com "onAdCue"

Os serviços de mídia do Azure podem escutar e responder a vários tipos de mensagem [AMF0] que podem ser usados para sinalizar vários metadados sincronizados em tempo real na transmissão ao vivo.  A especificação [Adobe-Primetime] define dois tipos de indicação chamados de modo "simples" e "SCTE-35". Para o modo "simples", os serviços de mídia dão suporte a uma única mensagem de indicação de AMF chamada "onAdCue" usando uma carga que corresponde à tabela abaixo definida para o sinal "modo simples".  

A seção a seguir mostra o conteúdo RTMP "modo simples", que pode ser usado para sinalizar um sinal de anúncio básico "cojunção" que será transmitido ao manifesto do cliente para HLS, DASH e Microsoft Smooth Streaming. Isso é muito útil para cenários em que o cliente não tem um sistema de inserção de sinalização de anúncio com base em SCTE-35 complexo, e está usando um codificador básico local para enviar na mensagem de indicação por meio de uma API. Normalmente, o codificador local dará suporte a uma API baseada em REST para disparar esse sinal, que também "colocará a condição" no fluxo de vídeo inserindo um quadro de IDR no vídeo e iniciando um novo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>sinalização de indicação de anúncio RTMP 2.1.3 com "onAdCue" – modo simples

| Nome do campo | Tipo de campo | Necessário? | Descrições                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | Obrigatório  | A mensagem do evento.  Deve ser "SpliceOut" para designar uma junção de modo simples.                                                                                                                                                                                                         |
| id         | String     | Obrigatório  | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem                                                                                                                                                                                       |
| duration   | Número     | Obrigatório  | A duração da junção. As unidades são frações de segundo.                                                                                                                                                                                                                           |
| elapsed    | Número     | Opcional  | Quando o sinal está sendo repetido para dar suporte a ajuste, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. Ao usar o modo simples, esse valor não deve exceder a duração original da junção. |
| time       | Número     | Obrigatório  | Deverá ser o horário da junção, no horário da apresentação. As unidades são frações de segundo.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo de saída do manifesto de MPEG DASH ao usar o modo simples do Adobe RTMP

Confira o exemplo [3.3.2.1 MPEG Dash. MPD EventStream usando o modo simples do Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Consulte o exemplo [de manifesto 3.3.3.1 Dash com um único ponto e modo simples do Adobe ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo de saída de manifesto HLS ao usar o modo simples do Adobe RTMP

Consulte o [manifesto de 3.2.2 HLS usando o modo simples da Adobe e a marca ext-X-Cue](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>sinalização de indicação de anúncio RTMP 2.1.4 com "onAdCue"-modo SCTE-35

Quando você estiver trabalhando com um fluxo de trabalho de produção de difusão mais avançado que exige que a mensagem de carga completa do SCTE-35 seja transportada para o manifesto HLS ou DASH, é melhor usar o "modo SCTE-35" da especificação [Adobe-Primetime].  Esse modo dá suporte a sinais SCTE-35 em banda sendo enviados diretamente para um codificador ao vivo local, que codifica os sinais para o fluxo RTMP usando o "modo SCTE-35" especificado na especificação [Adobe-Primetime]. 

Normalmente, as mensagens SCTE-35 podem aparecer apenas em entradas de TS (fluxo de transporte MPEG-2) em um codificador local. Consulte o fabricante do codificador para obter detalhes sobre como configurar um ingestão de fluxo de transporte que contém SCTE-35 e habilitá-lo para passagem para RTMP no modo Adobe SCTE-35.

Nesse cenário, a carga a seguir deve ser enviada do codificador local usando o tipo de mensagem **"onAdCue"** [AMF0].

| Nome do campo | Tipo de campo | Necessário? | Descrições                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| advertência        | String     | Obrigatório  | A mensagem do evento.  Para mensagens [SCTE-35], ele deve ser o binário codificado na base64 [RFC4648] splice_info_section () para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash.                                                                                                                                                                                                                               |
| type       | String     | Obrigatório  | Uma URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], isso **deve** ser **"scte35"** para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash, em conformidade com [Adobe-Primetime]. Opcionalmente, o URN "urn: SCTE: scte35:2013: bin" também pode ser usado para sinalizar uma mensagem [SCTE-35].                                                                                                        |
| id         | String     | Obrigatório  | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem.  Mensagens com semântica equivalente devem ter o mesmo valor.                                                                                                                                                                                                                                                       |
| duration   | Número     | Obrigatório  | A duração do evento ou junção-segmento ad, se conhecido. Se for desconhecido, o valor **deverá** ser 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Número     | Opcional  | Quando o sinal [SCTE-35] ad está sendo repetido para ajustar, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. No modo [SCTE-35], esse valor pode exceder a duração especificada original da junção ou segmento.                                                                                                                   |
| time       | Número     | Obrigatório  | O horário da apresentação do evento ou junção ad.  A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I. Para saída de HLS, o tempo e a duração **devem ser** alinhados com limites de segmento. A hora de apresentação e a duração de mensagens de eventos diferentes dentro do mesmo fluxo de eventos NÃO DEVEM se sobrepor. As unidades são frações de segundo. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exemplo de manifesto HLS. M3U8 com sinal de modo SCTE-35
Consulte [a seção 3.2.1.1 de exemplo HLS manifest com SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>sinalização de anúncio RTMP 2.1.5 com "onCuePoint" para elemento ao vivo

O codificador local em tempo real dá suporte a marcadores de anúncio no sinal RTMP. Atualmente, os serviços de mídia do Azure dão suporte apenas ao tipo de marcador do AD "onCuePoint" para RTMP.  Isso pode ser habilitado nas configurações de grupo do Adobe RTMP nas configurações do codificador dinâmico de mídia elementar ou na API, definindo "**ad_markers**" como "onCuePoint".  Consulte a documentação do Live Element para obter detalhes. Habilitar esse recurso no grupo RTMP passará sinais SCTE-35 para as saídas do Adobe RTMP a serem processadas pelos serviços de mídia do Azure.

O tipo de mensagem "onCuePoint" é definido em [Adobe-Flash-AS] e tem a seguinte estrutura de carga quando enviado da saída RTMP ao vivo elementar.


| Propriedade   | Descrição                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | O nome deve ser '**scte35**' por um elemento ao vivo.                                                                                                                                                                              |
| time       | O tempo em segundos no qual o ponto de sinalização ocorreu no arquivo de vídeo durante a linha do tempo                                                                                                                                           |
| type       | O tipo de ponto de sinalização deve ser definido como "**evento**".                                                                                                                                                                             |
| parameters | Uma matriz associativa de cadeias de caracteres de pares de nome/valor que contém as informações da mensagem SCTE-35, incluindo a ID e a duração. Esses valores são analisados pelos serviços de mídia do Azure e incluídos na marca de decoração do manifesto. |


Quando esse modo de marcador do AD é usado, a saída do manifesto HLS é semelhante ao modo "simples" da Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exemplo MPEG DASH MPD, ponto único, sinais de modo simples do Adobe

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exemplo de lista de reprodução HLS, sinais de modo simples da Adobe usando marca de indicação EXT-X (truncado "..." para fins de brevidade)

O exemplo a seguir mostra a saída do empacotador dinâmico dos serviços de mídia para um fluxo de ingestão RTMP usando sinais de modo "simples" e a marca "Primetime] EXT-X-CUE herdada.  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>cancelamento e atualizações do 2.1.6

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação. A hora e ID de apresentação identificam exclusivamente o evento e a última mensagem recebida por um horário de apresentação específico que atende às restrições de pré-inscrição é a mensagem que é tratada. O evento atualizado substitui qualquer mensagem recebida anteriormente. A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingestão de MP4 fragmentado (Smooth Streaming)

Consulte [MS-SSTR-ingestão] para obter os requisitos de ingestão de fluxo ao vivo. As seções a seguir fornecem detalhes com relação à ingestão de metadados de apresentação cronometrados.  Os metadados de apresentação cronometrados são ingeridos como uma faixa esparsa, que é definida na caixa de manifesto do servidor ao vivo (consulte MS-SSTR) e a caixa de filme (' Moov ').  

Cada fragmento esparso consiste em uma caixa de fragmento de filme (' Moof ') e Data Box de mídia (' mdat '), em que a caixa ' mdat ' é a mensagem binária.

Para obter a inserção precisa de quadros de anúncios, o codificador deve dividir o fragmento no momento da apresentação onde a indicação precisa ser inserida.  É necessário criar um novo fragmento que começa com um quadro de IDR recém-criado ou com os pontos de acesso de fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 Caixa de manifesto do servidor Live

A faixa esparsa **deve** ser declarada na caixa de manifesto do Live Server com uma **\<textstream\>** entrada e **deve** ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Obrigatório?** | **Descrição**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Número         | Obrigatório      | **Deve** ser "0", indicando uma faixa com taxa de bits de variável desconhecida.                                                                                                                                                          |
| parentTrackName    | String         | Obrigatório      | **Deve** ser o nome da faixa pai, para a qual os códigos de tempo de faixa esparsa são alinhados em escala temporal. A faixa pai não pode ser uma faixa esparsa.                                                                             |
| manifestOutput     | Boolean        | Obrigatório      | **Deve** ser "true" para indicar que a faixa esparsa será inserida no manifesto do cliente suave.                                                                                                                        |
| Subtype            | String         | Obrigatório      | **Deve** ser o código de quatro caracteres "dados".                                                                                                                                                                                  |
| Esquema             | String         | Obrigatório      | **Deve** ser um urn ou uma URL que identifica o esquema de mensagem. Para mensagens [SCTE-35], **deve** ser "urn: SCTE: scte35:2013: bin" para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. |
| trackName          | String         | Obrigatório      | **Deve** ser o nome da faixa esparsa. O trackname pode ser usado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo de eventos exclusivo **deve** ter um nome de faixa exclusivo.                                |
| escala de tempo          | Número         | Opcional      | **Deve** ser a escala de temporal da faixa pai.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Caixa de filme

A caixa de filme (' Moov ') segue a caixa de manifesto do Live Server como parte do cabeçalho de fluxo de uma faixa esparsa.

A caixa ' Moov ' **deve** conter uma caixa **TrackHeaderBox (' tkhd ')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | Inteiro sem sinal de 64 bits | Obrigatório      | **Deve** ser 0, pois a caixa de controle tem zero amostra e a duração total dos exemplos na caixa de controle é 0. |

---

A caixa ' Moov ' **deve** conter um **HandlerBox (' hdlr ')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Obrigatório?** | **Descrição**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | Inteiro sem sinal de 32 bits | Obrigatório      | **Deve** ser ' meta '. |

---

A caixa ' stsd ' **deve** conter uma caixa MetaDataSampleEntry com um nome de codificação, conforme definido em [ISO-14496-12].  Por exemplo, para mensagens SCTE-35, o nome de codificação **deve** ser ' SCTE '.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caixa de fragmentos de filme e Data Box de Mídia

Fragmentos de faixa esparsa consistem em uma caixa de fragmento de filme (' Moof ') e uma Data Box de mídia (' mdat ').

> [!NOTE]
> Para obter a inserção precisa de quadros de anúncios, o codificador deve dividir o fragmento no momento da apresentação onde a indicação precisa ser inserida.  É necessário criar um novo fragmento que começa com um quadro de IDR recém-criado ou com os pontos de acesso de fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I
> 

A caixa caixa moviefragmentbox (' Moof ') **deve** conter uma caixa **TrackFragmentExtendedHeaderBox (' UUID ')** , conforme definido em [MS-SSTR], com os seguintes campos:

| **Nome do campo**         | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | Inteiro sem sinal de 64 bits | Obrigatório      | **Deve** ser a hora de chegada do evento. Esse valor alinha a mensagem com a faixa pai.           |
| fragment_duration      | Inteiro sem sinal de 64 bits | Obrigatório      | **Deve** ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

---


A caixa MediaDataBox (' mdat ') **deve** ter o seguinte formato:

| **Nome do campo**          | **Tipo de campo**                   | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Determina o formato do conteúdo da caixa ' mdat '. Versões não reconhecidas serão ignoradas. Atualmente, a única versão aceita é 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Ou seja, o processamento de qualquer caixa de mensagem de um evento com a mesma ID é suficiente.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | A soma da fragment_absolute_time, especificada em TrackFragmentExtendedHeaderBox, e a presentation_time_delta **deve** ser a hora da apresentação do evento. A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I. Para saída de HLS, o tempo e a duração **devem ser** alinhados com limites de segmento. A hora da apresentação e a duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos não **devem** se sobrepor. |
| message                 | matriz de bytes                       | Obrigatório      | A mensagem do evento. Para mensagens [SCTE-35], a mensagem é o splice_info_section binário (). Para mensagens [SCTE-35], isso **deve** ser o splice_info_section () para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. Para mensagens [SCTE-35], o binário splice_info_section () é a carga da caixa ' mdat ' e **não** é codificado em base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Cancelamento e atualizações

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação.  A hora e ID de apresentação identificam exclusivamente o evento. A última mensagem recebida por um horário de apresentação específico, que atende às restrições de pré-inscrição, é a mensagem que é tratada. A mensagem atualizada substitui qualquer mensagem recebida anteriormente.  A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadados cronometrados

Dados de fluxo de evento são opacos para Serviços de Mídia. Serviços de Mídia simplesmente passam três partes de informações entre o ponto de extremidade de ingestão e o ponto de extremidade do cliente. As propriedades a seguir são entregues ao cliente, em conformidade com [SCTE-35] e/ou o protocolo de streaming do cliente:

1.  Esquema – uma URN ou URL que identifica o esquema da mensagem.
2.  Horário da apresentação – o horário da apresentação do evento na linha do tempo da mídia.
3.  Duração – a duração do evento.
4.  ID – um identificador exclusivo opcional para o evento.
5.  Mensagem – os dados do evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 manifesto do Microsoft Smooth Streaming  

Consulte tratamento de faixas esparsas [MS-SSTR] para obter detalhes sobre como formatar uma faixa de mensagens esparsas. Para mensagens [SCTE35], Smooth Streaming produzirá o splice_info_section codificado na Base64 () em um fragmento esparso.
O StreamIndex **deve** ter um subtipo de "data" e o CustomAttributes **deve** conter um atributo com Name = "Schema" e Value = "urn: SCTE: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemplo de manifesto de cliente suave mostrando splice_info_section codificado na base64 [SCTE35] ()
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>Decoração de manifesto do Apple HLS 3,2

Os serviços de mídia do Azure dão suporte às seguintes marcas de manifesto HLS para sinalizar informações sobre o AD DISP durante um evento ao vivo ou sob demanda. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- "EXT-X-CUE" conforme definido em [Adobe-Primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

A saída de dados para cada marca irá variar com base no modo de sinal de ingestão usado. Por exemplo, a ingestão RTMP com o modo simples da Adobe não contém a carga codificada em base64 SCTE-35 completa.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 exemplo HLS manifest. M3U8 mostrando a sinalização EXT-X-CUE de SCTE-35

O exemplo de saída de manifesto HLS a seguir do empacotador dinâmico dos serviços de mídia mostra a marca de indicação EXT-X para [Adobe-Primetime] no modo SCTE35. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS com Adobe Primetime EXT-X-CUE

Os serviços de mídia (API da versão 2 e 3) dão suporte à saída da marca EXT-X-CUE, conforme definido no [Adobe-Primetime] "SCTE-35 Mode". Nesse modo, os serviços de mídia do Azure incorporarão a codificação Base64 [SCTE-35] splice_info_section () na marca EXT-X-CUE.  

A marca "legacy" EXT-X-CUE é definida como abaixo e também pode ser normativas referenciada na especificação [Adobe-Primetime]. Isso só deve ser usado para sinalização de SCTE35 herdada quando necessário; caso contrário, a marca recomendada é definida em [RFC8216] como EXT-X-DATERANGE. 

| **Nome do atributo** | **Tipo**                      | **Obrigatório?**                             | **Descrição**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ADVERTÊNCIA                | cadeia de caracteres entre aspas                 | Obrigatório                                  | A mensagem codificada como uma cadeia de caracteres codificada em base64, conforme descrito em [RFC4648]. Para mensagens [SCTE-35], este é o splice_info_section codificado em Base64 ().                                                                                                                                      |
| TYPE               | cadeia de caracteres entre aspas                 | Obrigatório                                  | Uma URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], o tipo usa o valor especial "scte35".                                                                                                                                                                          |
| ID                 | cadeia de caracteres entre aspas                 | Obrigatório                                  | Um identificador exclusivo para o evento. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                                                                              |
| DURAÇÃO           | um número de ponto flutuante decimal | Obrigatório                                  | A duração do evento. Se for desconhecido, o valor **deverá** ser 0. As unidades são frações de segundos.                                                                                                                                                                                           |
| DECORRIDO            | um número de ponto flutuante decimal | Opcional, mas Necessário para janela deslizante | Quando o sinal está sendo repetido para dar suporte a uma janela de apresentação deslizante, esse campo **deve** ser a quantidade de tempo de apresentação decorrido desde o início do evento. As unidades são frações de segundo. Esse valor pode exceder a duração especificada original da junção ou segmento. |
| TIME               | um número de ponto flutuante decimal | Obrigatório                                  | O horário da apresentação do evento. As unidades são frações de segundo.                                                                                                                                                                                                                        |

A camada de aplicativo de player HLS usará o TIPO para identificar o formato da mensagem, decodificar a mensagem, aplicar as conversões de tempo necessárias e processar o evento.  Os eventos são sincronizados por horário na lista de reprodução de segmento da faixa pai, de acordo com o carimbo de data/hora do evento.  Eles são inseridos antes do segmento mais próximo (marca #EXTINF).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>exemplo de manifesto 3.2.3 HLS. M3U8 usando Adobe Primetime EXT-X-CUE

O exemplo a seguir mostra a decoração de manifesto HLS usando a marca Primetime EXT-X-CUE da Adobe.  O parâmetro "CUE" contém apenas as propriedades TYPE e Duration, o que significa que essa era uma fonte RTMP usando a sinalização de modo "simples" da Adobe.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>Manipulação de mensagens 3.2.4 HLS para Adobe Primetime EXT-X-CUE

Os eventos são sinalizados na lista de reprodução de segmentos de cada faixa de vídeo e áudio. A posição da marca EXT-X-CUE **deve** sempre ser imediatamente anterior ao primeiro segmento HLS (para União externa ou início de segmento) ou imediatamente após o último segmento de HLS (para junção no ou fim de segmento) ao qual seus atributos de tempo e duração se referem, conforme exigido pelo [Adobe-Primetime].

Quando uma janela de apresentação deslizante está habilitada, a marca EXT-X-CUE **deve** ser repetida com frequência suficiente para que a junção ou o segmento esteja sempre totalmente descrito na lista de reprodução de segmentos, e o atributo decorrido **deve** ser usado para indicar a quantidade de tempo que a junção ou o segmento esteve ativo, conforme exigido pelo [Adobe-Primetime].

Quando uma janela de apresentação deslizante está habilitada, as marcas EXT-X-CUE são removidas da lista de reprodução de segmento quando o tempo de mídia a que eles se referem seja revertido para fora da janela deslizante de apresentação.

## <a name="33-dash-manifest-decoration-mpd"></a>Decoração de manifesto de traço 3,3 (MPD)

[MPEGDASH] fornece três maneiras de sinalizar eventos:

1.  Eventos sinalizados no EventStream do MPD
2.  Eventos sinalizados em banda usando a caixa de mensagem de evento (' EMSG ')
3.  Uma combinação de 1 e 2

Eventos sinalizados no EventStream do MPD são úteis para o streaming do VOD porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é baixado. Ele também é útil para a sinalização SSAI, em que o fornecedor downstream SSAI precisa analisar os sinais do manifesto MPD e inserir conteúdo do AD dinamicamente.  A solução em banda (' EMSG ') é útil para transmissão ao vivo, em que os clientes não precisam baixar o MPD novamente, ou não há nenhuma manipulação de manifesto SSAI acontecendo entre o cliente e a origem. 

O comportamento padrão dos serviços de mídia do Azure para DASH é sinalizar ambos em MPD EventStream e em banda usando a caixa de mensagem de evento (' EMSG ').

As mensagens de indicação ingeridas em [RTMP] ou [MS-SSTR-ingestão] são mapeadas em eventos DASH, usando caixas ' EMSG ' em banda e/ou EventStreams in-MPD. 

A sinalização em banda SCTE-35 para DASH segue a definição e os requisitos definidos em [SCTE-214-3] e também na seção [DASH-IF-IOP] 13.12.2 (' SCTE35 Events '). 

Para o carro em banda [SCTE-35], a caixa de mensagem de evento (' EMSG ') usa o schemeid = "urn: SCTE: scte35:2013: bin". Para decoração de manifesto do MPD, o EventStream schemeid usa "urn: SCTE: scte35:2014: XML + bin".  Esse formato é uma representação XML do evento que inclui uma saída binária codificada em base64 da mensagem SCTE-35 completa que chegou à ingestão. 

Normativas as definições de referência de carro de [SCTE-35] indicações de mensagens em DASH estão disponíveis em [SCTE-214-1] SEC 6.7.4 (MPD) e [SCTE-214-3] s 7.3.2 (carro de SCTE 35 Cue Messages).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>Sinalização do 3.3.1 MPEG DASH (MPD) EventStream

A decoração de eventos do manifesto (MPD) será sinalizada no MPD usando o elemento EventStream, que aparece dentro do elemento period. O schemeid usado é "urn: SCTE: scte35:2014: XML + bin".


> [!NOTE]
> Para fins de resumição [SCTE-35] permite o uso da seção codificada em base64 no elemento Signal. Binary (em vez do elemento Signal. SpliceInfoSection) como uma alternativa ao carro de uma mensagem de indicação completamente analisada.
> Os serviços de mídia do Azure usam essa abordagem de ' XML + bin ' para sinalizar no manifesto MPD.
> Esse também é o método recomendado usado na seção [DASH-IF-IOP]-consulte intitulada [' fluxos de eventos de inserção do AD ' do Dash se a diretriz de IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor **deve** ser um urn ou URL que identifica o esquema de mensagem; O schemeset de saída com suporte deve ser "urn: SCTE: scte35:2014: XML + bin" por [SCTE-214-1] SEC 6.7.4 (MPD), pois o serviço oferece suporte apenas a "XML + bin" neste momento para fins de brevidade no MPD. |
| value              | string                  | Opcional      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor **deve** ser definido como o nome do fluxo de eventos (trackname para [MS-SSTR-ingestão] ou o nome da mensagem AMF para ingestão [RTMP]).                                                                         |
| Escala de tempo          | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de e/s, em tiques por segundo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>Fluxos de eventos de exemplo 3.3.2 para MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>exemplo de 3.3.2.1 do manifesto MPEG DASH. MPD de streaming RTMP usando o modo simples da Adobe

O exemplo a seguir mostra um trecho EventStream do empacotador dinâmico dos serviços de mídia para um fluxo RTMP usando a sinalização de modo "simples" do Adobe.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>exemplo de 3.3.2.2 do manifesto MPEG DASH. MPD de um fluxo RTMP usando o modo Adobe SCTE-35

O exemplo a seguir mostra um trecho EventStream do empacotador dinâmico dos serviços de mídia para um fluxo RTMP usando a sinalização do modo Adobe SCTE-35.

Elemento EventStream de exemplo usando a sinalização de estilo XML + bin por [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Observe que presentationtime é o tempo de apresentação do evento [SCTE-35] traduzido para ser relativo à hora de início do período, não à hora de chegada da mensagem.
> [MPEGDASH] define o Event@presentationTime como "especifica a hora da apresentação do evento em relação ao início do período.
> O valor do tempo de apresentação em segundos é a divisão do valor desse atributo e o valor do EventStream@timescale atributo.
> Se não estiver presente, o valor da hora da apresentação será 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>exemplo de manifesto MPEG DASH (MPD) 3.3.3.1 com um único período, EventStream, usando sinais de modo simples do Adobe

O exemplo a seguir mostra a saída do empacotador dinâmico dos serviços de mídia para um fluxo RTMP de origem usando o método de sinal de anúncio do modo "simples" do Adobe. A saída é um manifesto de período único que mostra um EventStream usando o URI de schemeid definido como "urn: com: Adobe: dpi: simple: 2015" e a propriedade Value definida como "simplesignal".
Cada sinal simples é fornecido em um elemento de evento com @presentationTime as @duration Propriedades, e @id populadas com base nos sinais simples de entrada.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>Sinalização da caixa de mensagem de evento em banda do 3.3.4 MPEG DASH

Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream no nível do Conjunto de Adaptação.  Esse elemento tem um atributo schemeIdUri obrigatório e um atributo de TIMESCALE opcional, que também aparecem na caixa de mensagem de evento (' EMSG ').  Caixas de mensagem de evento com identificadores de esquema que não estão definidos no MPD não **devem** estar presentes.

Para o carro em banda [SCTE-35], os sinais **devem** usar schemeid = "urn: SCTE: scte35:2013: bin".
As definições normativas de carro de [SCTE-35] mensagens em banda são definidas em [SCTE-214-3] s 7.3.2 (carro de SCTE mensagens de sinalização 35).

Os detalhes a seguir descrevem os valores específicos que o cliente deve esperar em ' EMSG ' em conformidade com [SCTE-214-3]:

| **Nome do campo**          | **Tipo de campo**          | **Obrigatório?** | **Descrição**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor **deve** ser um urn que identifica o esquema de mensagem. Para mensagens [SCTE-35], **deve** ser "urn: SCTE: scte35:2013: bin" em conformidade com [SCTE-214-3]          |
| Valor                   | string                  | Obrigatório      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor será definido como o nome do fluxo de eventos (trackName para ingestão Smooth ou nome de mensagem AMF para ingestão RTMP). |
| Escala de tempo               | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de tempo, em tiques por segundo, dos campos horas e duração na caixa ' EMSG '.                                                                                                                                                                                                            |
| Presentation_time_delta | Inteiro sem sinal de 32 bits | Obrigatório      | O intervalo de tempo de apresentação de mídia do tempo de apresentação do evento e o primeiro horário nesse segmento. A hora e a duração da apresentação **devem** alinhar-se com os pontos de acesso do fluxo (SAP) do tipo 1 ou 2, conforme definido em [ISO-14496-12] anexo I.                                  |
| event_duration          | Inteiro sem sinal de 32 bits | Obrigatório      | A duração do evento ou 0xFFFFFFFF para indicar uma duração desconhecida.                                                                                                                                                                                                                              |
| ID                      | Inteiro sem sinal de 32 bits | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                        |
| Message_data            | matriz de bytes              | Obrigatório      | A mensagem do evento. Para mensagens [SCTE-35], os dados da mensagem são o splice_info_section binário () em conformidade com [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exemplo de entidade InBandEvenStream para o modo simples da Adobe
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Manipulação de mensagens do 3.3.5 DASH

Os eventos são sinalizados em banda, dentro da caixa ' EMSG ', para faixas de vídeo e áudio.  A sinalização ocorre para todas as solicitações de segmento para as quais o presentation_time_delta é de 15 segundos ou menos. 

Quando uma janela de apresentação deslizante está habilitada, as mensagens de evento são removidas do MPD quando a soma do tempo e da duração da mensagem de evento é menor que o tempo dos dados de mídia no manifesto.  Em outras palavras, as mensagens de evento são removidas do manifesto quando o tempo de mídia ao qual elas se referem foi transferida para fora da janela de apresentação deslizante.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 diretrizes de implementação de ingestão para fornecedores de codificador

As diretrizes a seguir são problemas comuns que podem afetar a implementação de um fornecedor de codificador dessa especificação.  As diretrizes a seguir foram coletadas com base nos comentários do parceiro real para facilitar a implementação dessa especificação para outras pessoas. 

[SCTE-35] as mensagens são ingeridas no formato binário usando o esquema **"urn: SCTE: scte35:2013: bin"** para [MS-SSTR-ingestão] e o tipo **"scte35"** para ingestão [RTMP]. Para facilitar a conversão de intervalo [SCTE-35], que se baseia em carimbos de data/hora de apresentação de fluxo de transporte MPEG-2 (pt), um mapeamento entre PTS (pts_time + pts_adjustment da splice_time()) e a linha do tempo de mídia é fornecida pelo tempo de apresentação do evento (o campo fragment_absolute_time para ingestão Smooth e o campo de hora para ingestão RTMP). O mapeamento é necessário porque o valor de PTS de 33 bits passa aproximadamente a cada 26,5 horas.

Smooth Streaming ingestão [MS-SSTR-ingestão] requer que o Data Box de mídia (' mdat ') **deva** conter o **splice_info_section ()** definido em [SCTE-35]. 

Para ingestão RTMP, o atributo Cue da mensagem AMF é definido como o splice_info_section codificado na base64 **()** definido em [SCTE-35].  


Quando as mensagens têm o formato descrito acima, elas são enviadas aos clientes HLS, Smooth e DASH, conforme definido acima.  

Ao testar sua implementação com a plataforma de serviços de mídia do Azure, comece testando com um LiveEvent de "passagem" primeiro, antes de passar para o teste em um LiveEvent de codificação.

---

## <a name="change-history"></a>Histórico de Alterações

| Data     | Alterações                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Suporte de ingestão RTMP revisado, adicionado "onCuePoint" RTMP para o elemento ao vivo                                            |
| 08/22/19 | Atualizado para adicionar OnUserDataEvent a RTMP para metadados personalizados                                                          |
| 1/08/20  | Erro fixo no modo SCTE35 RTMP simples e RTMP. Alterado de "onCuePoint" para "onAdCue". Tabela de modo simples atualizada. |
| 08/4/20  | Foi removido o suporte para a marca DATERANGE para corresponder à implementação no serviço de produção.    |

## <a name="next-steps"></a>Próximas etapas
Exibir os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
