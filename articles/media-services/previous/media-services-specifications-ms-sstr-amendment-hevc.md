---
title: Serviços de Mídia do Azure - Aditamento do Protocolo Smooth Streaming (MS-SSTR) para HEVC | Microsoft Docs
description: Esta especificação descreve o protocolo e formato para transmissão ao vivo baseada em MP4 fragmentado com HEVC nos Serviços de Mídia do Azure. Esse é um adendo à documentação do protocolo Smooth Streaming (MS-SSTR) para incluir suporte à ingestão e transmissão de HEVC. Somente as alterações necessárias para entregar HEVC são especificadas neste artigo, exceto quando "(Sem Alteração)" indica que o texto é copiado apenas para esclarecimentos.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b26252ddda7997bebd730bb4c1007f76b3e645a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650702"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Aditamento do Protocolo Smooth Streaming para HEVC 

## <a name="1-introduction"></a>1 Introdução 

Este artigo fornece os aditamentos detalhados a serem aplicados à especificação do Protocolo Smooth Streaming [MS-SSTR] para habilitar o Smooth Streaming de vídeo codificado HEVC. Nesta especificação, descrevemos apenas as alterações necessárias para entregar o codec de vídeo HEVC. O artigo segue o mesmo esquema de numeração da especificação [MS-SSTR]. Os títulos vazios apresentados em todo o artigo são fornecidos para orientar o leitor sobre a posição na especificação [MS-SSTR].  "(Sem Alteração)" indica que o texto é copiado apenas para fins de esclarecimento.

O artigo fornece requisitos de implementação técnica para a sinalização do codec de vídeo HEVC em um manifesto do Smooth Streaming e as referências normativas são atualizadas para fazer referência aos padrões MPEG atuais que incluem HEVC, Criptografia Comum de HEVC e, os nomes de caixa para Formato de Arquivo de Mídia Base ISO foram atualizados para serem consistentes com as últimas especificações. 

A especificação do protocolo Smooth Streaming [MS-SSTR] descreve o formato de transferência eletrônica utilizada para entregar mídia digital ao vivo e on-demand, como áudio e vídeo, nos modos a seguir: de um codificador para um servidor Web, de um servidor para outro servidor e de um servidor para um cliente HTTP.
O uso de uma entrega de estrutura de dados MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) por HTTP permite uma comutação contínua em tempo quase real entre diferentes níveis de qualidade de conteúdo de mídia compactado. O resultado é uma experiência de reprodução constante para o usuário final do cliente HTTP, mesmo se as condições de renderização de rede e vídeo alteram para o dispositivo ou computador cliente.

## <a name="11-glossary"></a>1.1 Glossário 

Os termos a seguir são definidos em *[MS-GLOS]*:

>   **GUID (Identificador Global Exclusivo) UUID (Identificador Universal Exclusivo)**

Os termos a seguir são específicos deste documento:

>  **tempo de composição:** o tempo em que uma amostra é apresentada no cliente, conforme definido em [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **CENC**: Criptografia Comum, conforme definido em [ISO/IEC 23001-7], Segunda Edição.
> 
>   **tempo de decodificação:** o tempo que uma amostra deve ser decodificada no cliente, conforme definido em [[ISO/IEC https://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**fragmento:** uma unidade de **mídia** que pode ser baixada independentemente, que compreende uma ou mais **amostras**.

>   **HEVC:** Codificação de Vídeo de Alta Eficiência, conforme definido em [ISO/IEC 23008-2]
> 
>   **manifesto:** metadados sobre a **apresentação** que permite que um cliente faça solicitações para **mídia**. **mídia:** dados de áudio, vídeo e texto compactados utilizados pelo cliente para reproduzir uma **apresentação**. **formato de mídia:** um formato bem definido para representar áudio ou vídeo como uma **amostra** compactada.
> 
>   **apresentação:** o conjunto de todos os **fluxos** e metadados relacionados necessários para reproduzir um único filme. **solicitação:** uma mensagem HTTP enviada do cliente para o servidor, conforme definido em [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **resposta:** uma mensagem HTTP enviada do servidor para o cliente, conforme definido em [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **amostra:** a menor unidade fundamental (como um quadro) na qual a **mídia** é armazenada e processada.
> 
>   **PODE, DEVERIA, DEVE, NÃO DEVERIA, NÃO DEVE:** esses termos (todos em maiúsculo) são utilizados conforme descrito em [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Todas as instruções de comportamento opcional utilizam PODE, DEVERIA OU NÃO DEVERIA.

## <a name="12-references"></a>1.2 Referências

>   Referências à documentação do Microsoft Open Specifications não incluem um ano de publicação porque os links são para a última versão dos documentos, que são frequentemente atualizados. Referências a outros documentos incluem um ano de publicação quando um estiver disponível.

### <a name="121-normative-references"></a>1.2.1 Referências normativas 

>  [MS-SSTR] Protocolo Smooth Streaming *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Organização Internacional de Normalização, "Tecnologia da informação – Codificação de objetos audiovisuais – Parte 12: Formato de Arquivo de Mídia Base ISO", ISO/IEC 14496-12:2014, Edição 4, Plus Retificação adicional 1, Aditamentos 1 e 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Organização Internacional de Normalização, "Tecnologia da informação – Codificação de objetos audiovisuais – Parte 15: Carro de vídeo estruturado da unidade do NAL no Formato de arquivo de mídia base ISO”, ISO 14496-15:2015, Edição 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Tecnologia da informação – Codificação de alta eficiência e entrega de mídia em ambientes heterogêneos – Parte 2: Codificação de vídeo de alta eficiência: 2013 ou edição mais recente <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Tecnologia da informação — Tecnologias de sistemas MPEG — Parte 7: Criptografia comum em arquivos de formato de arquivo de mídia base ISO, CENC edição 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "Os parâmetros 'Codecs' e 'Perfis' para tipos de mídia "Bucket"" <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] A autoridade de registro MP4, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "palavras-chave para uso em RFCs para indicar os níveis de requisição", BCP 14, RFC 2119, março de 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Referências informativas 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*."
> 
>   [RFC3548] Josefsson, S., ED., "O Base16, Base32 e Base64 codificações de dados", RFC 3548, julho de 2003 [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., e Overell, P., "BNF aumentado para Especificações de Sintaxe: ABNF", STD 68, RFC 5234, janeiro de 2008   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Visão geral 

>   Somente as alterações na especificação Smooth Streaming exigida para a entrega de HEVC são especificadas abaixo. Os cabeçalhos de seção inválidos são listados para manter a localização na especificação Smooth Streaming referenciada [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Relacionamento com outros protocolos 

## <a name="15-prerequisitespreconditions"></a>1.5 Pré-requisitos/Pré-condições 

## <a name="16-applicability-statement"></a>1.6 Applicability Statement 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Controle de versão e negociação de capacidade 

## <a name="18-vendor-extensible-fields"></a>1.8 Campos extensíveis para fornecedor 

>   O método a seguir DEVERÁ ser utilizado para identificar fluxos usando o formato de vídeo HEVC:
> 
>   * **Códigos descritivos personalizados para formatos de mídia:** essa funcionalidade é fornecida pelo campo **FourCC**, conforme especificado na seção *2.2.2.5*.
>   Os implementadores podem garantir que as extensões não entrem em conflito, registrando códigos de extensão com o MPEG4-RA, conforme especificado na [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Atribuições standard 

## <a name="2-messages"></a>2 Mensagens 

## <a name="21-transport"></a>2.1 Transporte

## <a name="22-message-syntax"></a>2.2 Sintaxe da mensagem 

### <a name="221-manifest-request"></a>2.2.1 Solicitação do manifesto 

### <a name="222-manifest-response"></a>2.2.2 Resposta do manifesto 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variável):** a versão secundária da mensagem da Resposta do Manifesto. DEVE ser definido para 2. (Sem Alteração)
> 
>   **TimeScale (variável):** a escala de tempo do atributo Duração, especificada como o número de incrementos em um segundo. O valor padrão é
> 1. (Sem Alteração)
> 
>    O valor recomendado é 90000 para representar a duração exata de fragmentos que contém o vídeo de taxa de quadros fracional (por exemplo, 30/1.001 Hz) e quadros de vídeo.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

O ProtectionElement DEVERÁ estar presente quando a Criptografia Comum (CENC) for aplicada a fluxos áudio ou vídeo. Fluxos criptografados HEVC DEVERÃO estar em conformidade com Criptografia Comum 2ª Edição [ISO/IEC 23001-7]. Somente dados de fatia em unidades de VCL NAL DEVERÃO ser criptografados.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variável):** a escala de tempo para valores de tempo e duração nesse fluxo, especificado como o número de incrementos em um segundo. É recomendável um valor de 90000 para fluxos HEVC. Um valor correspondente à frequência da amostra da forma de onda (por exemplo, 48000 ou 44100) é recomendado para transmissões de áudio.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variável):** um código de quatro caracteres que identifica qual formato de mídia é utilizado para cada amostra. O intervalo de valores a seguir é reservado com os seguintes significados semânticos:
> 
> * "hev1”: as amostras de vídeo para essa faixa usam o vídeo HEVC, utilizando o formato de descrição de amostra 'hev1' especificado em [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variável):** dados que especificam parâmetros específicos para o formato de mídia e comuns a todas as amostras na faixa, representados como uma cadeia de caracteres de bytes com codificação hexadecimal. O formato e o significado semântico da sequência de bytes variam com o valor do campo **FourCC**, da seguinte maneira:
> 
>   * Quando um TrackElement descrever o vídeo HEVC, o campo **FourCC** DEVERÁ ser equivalente a **"hev1"** e;
> 
>   O **CodecPrivateData** campo deve conter uma representação de cadeia de caracteres codificado em Hexa da seguinte sequência de bytes especificada em ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (sem alteração desde MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField contém o SPS (Conjunto de Parâmetros de Sequência).
> 
>   * PPSField contém o PPS (Conjunto de Parâmetros de Fatia).
> 
>   Observação: o VPS (Conjunto de Parâmetros de Vídeo) não está contido no CodecPrivateData, mas deve ser contido no cabeçalho do arquivo dos arquivos armazenados na caixa 'hvcC'. Os sistemas que utilizam o Protocolo Smooth Streaming devem sinalizar parâmetros de decodificação adicionais (por exemplo, Camada HEVC) utilizando os "codecs" de Atributo Personalizado.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   O campo **SmoothStreamingMedia’s MajorVersion** DEVE ser configurado para 2 e o campo **MinorVersion** DEVE ser configurado para 2. (Sem Alteração)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Solicitação de fragmento 

>   **Observação**: o formato de mídia padrão solicitado para **MinorVersion** 2 e ‘hev1’ é a marca ‘iso8’ do Formato de Arquivo de Mídia Base ISO especificado em [ISO/IEC 14496-12] Formato de Arquivo de Mídia Base ISO, Quarta Edição e [ISO/IEC 23001-7] Criptografia Comum, Segunda Edição.

### <a name="224-fragment-response"></a>2.2.4 Resposta de fragmento 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   O **TfxdBox** foi preterido e sua função substituída pela caixa 'tfdt' (track fragment decode time) especificado em [ISO/IEC 14496-12], seção 8.8.12.
> 
>   **Observação**: um cliente pode calcular a duração de um fragmento, somando as durações de amostra listadas na caixa 'trun' (track run) ou multiplicando o número de amostras vezes a duração da amostra padrão. O baseMediaDecodeTime em 'tfdt' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento.
> 
>   Uma caixa 'prft' (producer reference time) DEVERIA ser inserida antes de uma caixa 'moof' (movie fragment), conforme necessário, para indicar a hora UTC correspondente ao track fragment decode time da primeira amostra referenciada pela caixa movie fragment, conforme especificado em [ISO/IEC 14496-12] seção 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   O **TfrfBox** foi preterido e sua função substituída pela caixa 'tfdt' (track fragment decode time) especificado em [ISO/IEC 14496-12] seção 8.8.12.
> 
>   **Observação**: um cliente pode calcular a duração de um fragmento, somando as durações de amostra listadas na caixa 'trun' (track run) ou multiplicando o número de amostras vezes a duração da amostra padrão. O baseMediaDecodeTime em 'tfdt' mais a duração do fragmento é igual ao parâmetro de tempo de URL para o próximo fragmento. Examine os endereços que foram preteridos porque atrasam a transmissão ao vivo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   O **TfhdBox** e os campos relacionados encapsulam os padrões por metadados de amostra no fragmento. A sintaxe do campo **TfhdBox** é um subconjunto estrito da sintaxe da caixa track fragment header definido em [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695), seção 8.8.7.
> 
>   **BaseDataOffset (8 bytes):** o deslocamento, em bytes, desde o início do campo **MdatBox** até o campo de amostra no campo **MdatBox**. Para sinalizar essa restrição, o sinalizador default-base-is-moof (0x020000) deve ser configurado.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   O **TrunBox** e os campos relacionados encapsulam por metadados de amostra para o fragmento solicitado. A sintaxe de **TrunBox** é um subconjunto estrito da caixa track fragment run Versão 1 definido em [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]*, seção 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bytes):** o deslocamento de tempo de composição de amostra de cada amostra ajustado, de modo que o tempo de apresentação da primeira amostra apresentada no fragmento seja igual ao tempo de decodificação da primeira amostra decodificada. Deslocamentos de composição de amostra de vídeo negativo DEVERÃO ser utilizados,
> 
>   conforme definido em [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Observação: isso evita um erro de sincronização de vídeo causado por áudio de atraso de vídeo igual ao maior atraso de remoção de buffer de imagem decodificada e mantém o tempo de apresentação entre fragmentos alternativos que podem ter atrasos de remoção diferentes.
> 
>   A sintaxe dos campos definidos nesta seção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) permanece a mesma, exceto a seguinte:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Campos comuns de resposta de fragmento 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Ponteiro de fluxo esparso 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragmento ainda não disponível 

### <a name="227-live-ingest"></a>2.2.7 Ingestão ao vivo 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (variável):** especifica o subtipo e o uso pretendido do arquivo MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) e atributos de alto nível.
> 
>   **MajorBrand (variável):** a principal marca do arquivo de mídia. DEVE ser definido para "isml."
> 
>   **MinorVersion (variável):** a versão secundária do arquivo de mídia. DEVE ser definido para 1.
> 
>   **CompatibleBrands (variável):** Especifica as marcas do MPEG-4 com suporte.
>   DEVE incluir "ccff" e "iso8."
> 
>   A sintaxe dos campos definidos nesta seção, especificada em ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) é a seguinte:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Observação**: as marcas de compatibilidade ‘ccff’ e ‘iso8’ indicam que os fragmentos estão em conformidade com o "Formato de Arquivo de Contêiner Comum" e Criptografia Comum [ISO/IEC 23001-7] e Formato de Arquivo de Mídia Base ISO Edição 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragmento 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Cabeçalho estendido de fragmento de faixa 

### <a name="228-server-to-server-ingest"></a>2.2.8 Ingestão servidor a servidor 

## <a name="3-protocol-details"></a>3 Detalhes do protocolo 


## <a name="31-client-details"></a>3.1 Detalhes do cliente 

### <a name="311-abstract-data-model"></a>3.1.1 Modelo de dados abstratos 

#### <a name="3111-presentation-description"></a>3.1.1.1 Descrição da apresentação 

>   O elemento de dados da Descrição de Apresentação encapsula todos os metadados para a apresentação.
> 
>   Metadados de apresentação: um conjunto de metadados comuns a todos os fluxos na apresentação. Metadados de Apresentação compreendem os campos a seguir, especificados na seção *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Duração**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Apresentações contendo Fluxos HEVC DEVERÃO definir:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Observação: caixas preteridas)
> 
>   As apresentações também DEVERIAM definir:

    TimeScale = 90000

>   Coleção de fluxo: uma coleção de elementos de dados de Descrição de Fluxo, conforme especificado na seção *3.1.1.1.2*.
> 
>   Descrição de proteção: uma coleção de elementos de dados da Descrição de Metadados do Sistema de Proteção, conforme especificado na seção *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Descrição de Metadados do Sistema de Proteção 

>   O elemento de dados da Descrição de Metadados do Sistema de Proteção encapsula metadados específicos para um único Sistema de Proteção de Conteúdo. (Sem Alteração)
> 
>   Descrição do cabeçalho de proteção: metadados de proteção de conteúdo que pertencem a um único Sistema de Proteção de Conteúdo. A Descrição do Cabeçalho de Proteção compreende os campos a seguir, especificados na seção *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Descrição do fluxo 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Descrição da faixa 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Descrição do atributo personalizado 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Descrição da referência do fragmento 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Descrição da referência do fragmento específico da faixa 

#### <a name="3112-fragment-description"></a>3.1.1.2 Descrição do fragmento 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Descrição da amostra 

### <a name="312-timers"></a>3.1.2 Temporizadores 

### <a name="313-initialization"></a>3.1.3 Inicialização 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Eventos disparados de camada superior 

#### <a name="3141-open-presentation"></a>3.1.4.1 Abrir presentação 

#### <a name="3142-get-fragment"></a>3.1.4.2 Obter fragmento 

#### <a name="3143-close-presentation"></a>3.1.4.3 Fechar apresentação 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Processando eventos e regras de sequenciamento 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Solicitação de manifesto e resposta de manifesto 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Solicitação de fragmento e resposta de fragmento

## <a name="32-server-details"></a>3.2 Detalhes do servidor

## <a name="33-live-encoder-details"></a>3.3 Detalhes do codificador dinâmico 

## <a name="4-protocol-examples"></a>4 Exemplos de protocolo 

## <a name="5-security"></a>5 Segurança 

## <a name="51-security-considerations-for-implementers"></a>5.1 Considerações de segurança para implementadores

>   Se o conteúdo transportado usando esse protocolo tiver um alto valor comercial, um Sistema de Proteção de Conteúdo deverá ser utilizado para impedir o uso não autorizado do conteúdo. O **ProtectionElement** pode ser usado para transportar metadados relacionados ao uso de um Sistema de Proteção de Conteúdo. O conteúdo de áudio e vídeo protegido DEVERÁ ser criptografado, conforme especificado pela Criptografia Comum MPEG, Segunda Edição: 2015[ISO/IEC 23001-7].
> 
>   **Observação**: para vídeo HEVC, somente dados de fatia em VCL NALs são criptografados. Cabeçalhos de fatia e outros NALs serão acessíveis aos aplicativos de apresentação antes da descriptografia. em um caminho de vídeo seguro, as informações criptografadas não estão disponíveis para aplicativos de apresentação.

## <a name="52-index-of-security-parameters"></a>5.2 Índice de parâmetros de segurança 


| **Parâmetro de segurança**  | **Seção**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Caixas de Criptografia Comum | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Caixas de Criptografia Comum

As caixas a seguir podem estar presentes nas respostas de fragmentos quando a Criptografia Comum for aplicada e são especificadas em [ISO/IEC 23001-7] ou [ISO/IEC 14496-12]:

1.  Caixa ‘pssh’ (protection system specific header)

2.  Caixa ‘senc’ (sample encryption)

3.  Caixa ‘saio’ (sample auxiliary information offset)

4.  Caixa ‘saiz’ (sample auxiliary information size)

5.  Caixa ‘sgpd’ (sample group description)

6.  Caixa ‘sbgp’ (sample to group)

-----------------------

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
