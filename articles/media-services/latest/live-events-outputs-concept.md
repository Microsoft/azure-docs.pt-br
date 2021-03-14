---
title: Conceitos de eventos ao vivo e saídas ao vivo
description: Este tópico fornece uma visão geral de eventos ao vivo e saídas ao vivo nos serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: a66532856263d31e9070bc99f297ae105ca48312
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454780"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventos e saídas ao vivo nos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os serviços de mídia do Azure permitem que você forneça eventos ao vivo para seus clientes na nuvem do Azure. Para configurar seus eventos de transmissão ao vivo nos serviços de mídia v3, você precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para clientes que estão migrando das APIs do Media Services v2, a entidade de **evento ao vivo** substitui o **canal** em v2 e a **saída ao vivo** substitui o **programa**.

## <a name="live-events"></a>Eventos ao vivo

[Eventos ao vivo](/rest/api/media/liveevents) são responsáveis por ingerir e processar feeds de vídeo ao vivo. Quando você cria um evento ao vivo, é criado um ponto de extremidade de entrada primário e secundário que você pode usar para enviar um sinal ao vivo de um codificador remoto. O codificador remoto dinâmico envia o feed de contribuição para esse ponto de extremidade de entrada usando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmentado-MP4). Para o protocolo de ingestão RTMP, o conteúdo pode ser enviado em Clear ( `rtmp://` ) ou criptografado com segurança na conexão ( `rtmps://` ). Para o protocolo de ingestão Smooth Streaming, os esquemas de URL compatíveis são `http://` ou `https://`.  

## <a name="live-event-types"></a>Tipos de evento ao vivo

Um [evento ao vivo](/rest/api/media/liveevents) pode ser definido como uma *passagem* (um codificador ao vivo local envia um fluxo de várias taxas de bits) ou *codificação ativa* (um codificador ao vivo local envia um fluxo de taxa de bits única). Os tipos são definidos durante a criação usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None**: um codificador ao vivo local envia um fluxo de taxas de bits múltiplas. O fluxo ingerido passa pelo evento ao vivo sem nenhum processamento adicional. Também chamado de modo de passagem.
* **LiveEventEncodingType. Standard**: um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxas de bits múltiplas. Se o feed de contribuição for de 720p ou de resolução superior, a predefinição de **default720p** codificará um conjunto de 6 pares de resolução/taxa de bits.
* **LiveEventEncodingType. Premium1080p**: um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxa de bits múltiplas. A predefinição de default1080p especifica o conjunto de saída de pares de resolução/taxa de bits.

### <a name="pass-through"></a>Passagem

![diagrama de exemplo de passagem ao vivo com serviços de mídia](./media/live-streaming/pass-through.svg)

Ao usar o evento de passagem **ao vivo**, você depende de seu codificador ao vivo local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o evento ao vivo (usando o protocolo RTMP ou MP4 fragmentado). O evento ao vivo então executa os fluxos de vídeo de entrada sem nenhum processamento adicional. Esse evento ao vivo de passagem é otimizado para eventos ao vivo de execução longa ou transmissão ao vivo linear 24x365. Ao criar esse tipo de evento ao vivo, especifique nenhum (LiveEventEncodingType. None).

Você pode enviar a contribuição em resoluções de até 4K e em uma taxa de quadros de 60 quadros / segundo, com codecs de vídeo H.264/AVC ou H.265/HEVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio. Para obter mais informações, consulte [comparação de tipos de eventos ao vivo](live-event-types-comparison.md).

> [!NOTE]
> Usar um método de passagem é a maneira mais econômica de realizar a transmissão ao vivo quando você está fazendo vários eventos por um longo período de tempo e já investiu em codificadores locais. Consulte os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
>

Consulte o exemplo de código .NET para criar um evento de passagem ao vivo em [evento ao vivo com DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Codificação ativa  

![diagrama de exemplo de codificação ativa com os serviços de mídia](./media/live-streaming/live-encoding.svg)

Ao usar a codificação ativa com os serviços de mídia, você configura seu codificador ao vivo local para enviar um vídeo de taxa de bits única como o feed de contribuição para o evento ao vivo (usando o protocolo RTMP ou Fragmented-Mp4). Em seguida, você configura um evento ao vivo para que ele codifique que o fluxo de taxa de bits única de entrada para um [fluxo de vídeo de várias taxas de bits](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e disponibiliza a saída para a entrega para reproduzir dispositivos por meio de protocolos como MPEG-Dash, HLS e Smooth streaming.

Ao usar a codificação ativa, você pode enviar o feed de contribuição somente em resoluções até a resolução de uma taxa de quadros de 30 quadros/segundo, com H. 264/AVC Video Codec e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Observe que os eventos ao vivo de passagem podem dar suporte a resoluções de até 4K em 60 quadros/segundo. Para obter mais informações, consulte [comparação de tipos de eventos ao vivo](live-event-types-comparison.md).

As resoluções e taxas de bits contidas na saída do codificador ao vivo são determinadas pela predefinição. Se estiver usando um codificador ativo **padrão** (LiveEventEncodingType. Standard), a predefinição *default720p* especificará um conjunto de seis pares de taxa de resolução/bit, indo de 720p a 3,5 Mbps para 192P a 200 Kbps. Caso contrário, se estiver usando um codificador **Premium1080p** Live (LiveEventEncodingType. Premium1080p), a predefinição de *default1080p* especificará um conjunto de seis pares de taxa de resolução/bit, indo de 1080p a 3,5 Mbps para 180p a 200 Kbps. Para obter informações, confira [Predefinições do sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ativa, abra um tíquete de suporte por meio de portal do Azure. Especifique a tabela desejada de resolução e taxas de bits. Verifique se há apenas uma camada em 720p (se solicitar uma predefinição para um codificador ao vivo padrão) ou em 1080p (se solicitar uma predefinição para um codificador ao vivo Premium1080p) e 6 camadas no máximo.

## <a name="creating-live-events"></a>Criando eventos ao vivo

### <a name="options"></a>Opções

Ao criar um evento ao vivo, você pode especificar as seguintes opções:

* Você pode dar um nome e uma descrição ao evento ao vivo.
* A codificação de nuvem inclui passagem (sem codificação de nuvem), padrão (até 720p) ou Premium (até 1080p). Para a codificação standard e Premium, você pode escolher o modo de ampliação do vídeo codificado.
  * Nenhum: respeita estritamente a resolução de saída especificada na predefinição de codificação sem considerar a taxa de proporção de pixel ou exibir a taxa de proporção do vídeo de entrada.
  * AutoSize: substitui a resolução de saída e a altera para corresponder à taxa de proporção de exibição da entrada, sem preenchimento. Por exemplo, se a entrada for 1920 x 1080 e a predefinição de codificação solicitar 1280x1280, o valor na predefinição será substituído e a saída será em 1280x720, que mantém a taxa de proporção de entrada de 16:9.
  * AutoAjuste: Preencha a saída (com a caixa Letterbox ou pilar) para respeitar a resolução de saída, garantindo que a região de vídeo ativa na saída tenha a mesma taxa de proporção que a entrada. Por exemplo, se a entrada for 1920 x 1080 e a predefinição de codificação solicitar 1280x1280, a saída será em 1280x1280, que contém um retângulo interno de 1280x720 na taxa de proporção de 16:9, com regiões de caixa de pilar 280 pixels de largura à esquerda e à direita.
* Protocolo de streaming (atualmente, há suporte para os protocolos RTMP e Smooth Streaming). Você não pode alterar a opção de protocolo enquanto o evento ao vivo ou suas saídas dinâmicas associadas estiverem em execução. Se você precisar de protocolos diferentes, crie um evento ao vivo separado para cada protocolo de streaming.
* A ID de entrada que é um identificador global exclusivo para o fluxo de entrada do evento ao vivo.
* Prefixo de nome de host estático que inclui nenhum (nesse caso, uma cadeia de caracteres hexadecimal de 128 bits aleatório será usada), use o nome do evento ao vivo ou use o nome personalizado.  Quando você opta por usar um nome de cliente, esse valor é o prefixo de nome de host personalizado.
* Você pode reduzir a latência de ponta a ponta entre a transmissão ao vivo e a reprodução Configurando o intervalo de quadros-chave de entrada, que é a duração (em segundos) de cada segmento de mídia na saída de HLS. O valor deve ser um inteiro diferente de zero no intervalo de 0,5 a 20 segundos.  O valor padrão será 2 segundos se *nenhum* dos intervalos de quadros-chave de entrada ou saída for definido. O intervalo de quadros chave só é permitido em eventos de passagem.
* Ao criar o evento, você pode defini-lo como inicialização automática. Quando AutoStart é definido como true, o evento ao vivo será iniciado após a criação. A cobrança começa assim que o evento ao vivo começa a ser executado. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças. Como alternativa, você pode iniciar o evento quando estiver pronto para iniciar o streaming.

> [!NOTE]
> A taxa de quadros máxima é de 30 fps para codificação standard e Premium.

## <a name="standby-mode"></a>Modo de espera

Ao criar um evento ao vivo, você pode defini-lo como modo de espera. Enquanto o evento estiver no modo de espera, você poderá editar a descrição, o prefixo de nome de host estático e restringir as configurações de acesso de entrada e visualização.  O modo de espera ainda é um modo Faturável, mas tem o preço diferente de quando você inicia uma transmissão ao vivo.

Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).

* Restrições de IP sobre a ingestão e versão prévia. Você pode definir os endereços IP que têm permissão para ingerir um vídeo para esse evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').
<br/><br/>
Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos formatos a seguir: endereço IPv4 com quatro números e intervalo de endereços CIDR.
<br/><br/>
Se você quiser habilitar determinados IPs em seus próprios firewalls ou se quiser restringir as entradas aos seus eventos ao vivo para os endereços IP do Azure, baixe um arquivo JSON dos [intervalos de endereços IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Para obter detalhes sobre esse arquivo, selecione a seção **detalhes** na página.

* Ao criar o evento, você pode optar por ativar as transcrições dinâmicas. Por padrão, a transcrição ao vivo está desabilitada. Para obter mais informações sobre a transcrição dinâmica de leitura [ao](live-transcription.md)vivo de transcrição.

### <a name="naming-rules"></a>Regras de nomenclatura

* O nome de evento ativo máximo é de 32 caracteres.
* O nome deve seguir este padrão de [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Consulte também [convenções de nomenclatura de pontos de extremidade de streaming](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Para garantir a exclusividade do seu nome de evento ao vivo, você pode gerar um GUID e, em seguida, remover todos os hifens e chaves (se houver). A cadeia de caracteres será exclusiva em todos os eventos ao vivo e seu comprimento será garantido como 32.

## <a name="live-event-ingest-urls"></a>URLs de ingestão de eventos ao vivo

Depois que o evento ao vivo for criado, você poderá obter URLs de ingestão que você fornecerá ao codificador local em tempo real. O codificador dinâmico usa essas URLs para gerar a entrada de um fluxo ao vivo. Para obter mais informações, consulte [codificadores ativos locais recomendados](recommended-on-premises-live-encoders.md).

>[!NOTE]
> A partir da versão da API 2020-05-01, as URLs do intuitivo são conhecidas como nomes de host estáticos

Você pode usar URLs intuitivas ou não intuitivas.

> [!NOTE]
> Para que uma URL de ingestão seja preditiva, defina o modo de "personalização".

* URL não intuitiva

    A URL não intuitivo é o modo padrão no Media Services V3. Você potencialmente Obtém o evento ao vivo rapidamente, mas a URL de ingestão é conhecida somente quando o evento ao vivo é iniciado. A URL será alterada se você parar/iniciar o evento ao vivo. Não intuitivo é útil em cenários quando um usuário final deseja transmitir usando um aplicativo em que o aplicativo deseja obter um evento ao vivo e ter uma URL de ingestão dinâmica não é um problema.

    Se um aplicativo cliente não precisar gerar previamente uma URL de ingestão antes que o evento ao vivo seja criado, permita que os serviços de mídia gerem automaticamente o token de acesso para o evento ao vivo.

* URL intuitiva

    O modo intuitivo é preferido por difusores de mídia grandes que usam codificadores de difusão de hardware e não desejam reconfigurar seus codificadores quando eles iniciam o evento ao vivo. Esses difusores desejam uma URL de ingestão preditiva que não é alterada ao longo do tempo.

    > [!NOTE]
    > No portal do Azure, a URL intuitivo é denominada "*prefixo de nome de host estático*".

    Para especificar esse modo na API, defina `useStaticHostName` como `true` no momento da criação (o padrão é `false` ). Quando `useStaticHostname` é definido como true, o `hostnamePrefix` especifica a primeira parte do nome de host atribuído aos pontos de extremidade de visualização e ingestão de eventos ao vivo. O hostname final seria uma combinação desse prefixo, o nome da conta do serviço de mídia e um código curto para os data center dos serviços de mídia do Azure.

    Para evitar um token aleatório na URL, você também precisa passar seu próprio token de acesso ( `LiveEventInput.accessToken` ) no momento da criação.  O token de acesso deve ser uma cadeia de caracteres GUID válida (com ou sem os hifens). Depois que o modo for definido, ele não poderá ser atualizado.

    O token de acesso precisa ser exclusivo em seu data center. Se seu aplicativo precisar usar uma URL intuitivo, é recomendável sempre criar uma nova instância de GUID para seu token de acesso (em vez de reutilizar qualquer GUID existente).

    Use as seguintes APIs para habilitar a URL intuitivo e definir o token de acesso para um GUID válido (por exemplo, `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Linguagem|Habilitar URL intuitivo|Definir token de acesso|
    |---|---|---|
    |REST|[Properties. vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--intuitivo-URL](/cli/azure/ams/live-event#az-ams-live-event-create)|[--Access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent. VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão dinâmica

* A cadeia de caracteres *aleatória* abaixo é um número hexadecimal de 128 bits composto de 32 caracteres de “0” a “9” e “a” a “f”.
* *seu token de acesso*: a cadeia de caracteres GUID válida que você define ao usar o modo intuitivo. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome do fluxo*: indica o nome do fluxo para uma conexão específica. O valor do nome do fluxo geralmente é adicionado pelo codificador ao vivo que você usa. Você pode configurar o codificador ao vivo para usar qualquer nome para descrever a conexão, por exemplo: "video1_audio1", "video2_audio1", "fluxo".

#### <a name="non-vanity-url"></a>URL não intuitiva

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Streaming suave

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL intuitiva

Nos caminhos a seguir, `<live-event-name>` significa o nome fornecido ao evento ou o nome personalizado usado na criação do evento ao vivo.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Streaming suave

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de visualização de eventos ao vivo

Depois que o evento ao vivo começar a receber o feed de contribuição, você poderá usar seu ponto de extremidade de visualização para visualizar e validar que você está recebendo a transmissão ao vivo antes da publicação adicional. Depois de verificar se o fluxo de visualização é bom, você pode usar o evento ao vivo para disponibilizar o Live Stream para entrega por meio de um ou mais pontos de extremidade de streaming (pré-criados). Para fazer isso, crie uma nova [saída ao vivo](/rest/api/media/liveoutputs) no evento ao vivo.

> [!IMPORTANT]
> Certifique-se de que o vídeo está fluindo para a URL de visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa execução de evento ao vivo

Para obter detalhes, consulte [operações de execução longa](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Saídas ao vivo

Depois que o fluxo fluir para o evento ao vivo, você poderá iniciar o evento de streaming criando um [ativo](/rest/api/media/assets), uma [saída ao vivo](/rest/api/media/liveoutputs)e um [localizador de streaming](/rest/api/media/streaminglocators). a saída ao vivo arquivará o fluxo e o tornará disponível para os visualizadores por meio do [ponto de extremidade de streaming](/rest/api/media/streamingendpoints).  

Para obter informações detalhadas sobre as saídas ao vivo, consulte [usando um DVR de nuvem](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Consulte o artigo [perguntas](frequently-asked-questions.md#live-streaming) frequentes.

## <a name="ask-questions-and-get-updates"></a>Faça perguntas e obtenha atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de live streaming](stream-live-tutorial-with-api.md)
