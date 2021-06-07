---
title: Transmissão ao vivo usando os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits | Microsoft Docs
description: Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com taxa de bits única de um codificador local e, em seguida, executa a codificação ativa para o fluxo de taxa de bits adaptável com os Serviços de Mídia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013203"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão ao vivo usando os Serviços de Mídia do Azure para criar fluxos de múltiplas taxas de bits

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não darão mais suporte ao protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Faça a migração de RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Visão geral
No AMS (Serviços de Mídia do Azure), um **Canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Um **canal** recebe fluxos de entrada ao vivo de uma das duas maneiras:

* Um codificador ativo local envia uma transmissão de taxa de bits adaptável única para o Canal que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.
* Um codificador ativo local envia múltiplas taxas de bits **RTMP** ou **Smooth Streaming** (MP4 fragmentado) para o Canal que não está habilitado para executar a codificação ativa com o AMS. Os fluxos ingeridos passam pelos **Canais** sem nenhum processamento adicional. Esse método é chamado **passagem**. Você pode usar os codificadores dinâmicos a seguir, que produzem Smooth Streaming com múltiplas taxas de bits: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), HaiVision, Teradek concodificadores.  Um codificador ativo também pode enviar uma transmissão de taxa de bits única para um canal que não está habilitado para a codificação ativa, porém, isso não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.
  > 
  > 

A partir da versão 2.10 dos Serviços de Mídia, quando você cria um canal, você pode especificar de que modo você deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Você tem duas opções:

* **Nenhum** – Especifique esse valor se você planejar usar um codificador ativo local, que emitirá uma transmissão de taxa de bits múltipla (uma transmissão de passagem). Nesse caso, o fluxo de entrada foi transmitido para a saída sem qualquer codificação. Esse é o comportamento de um canal em versão anterior à 2.10.  Para obter informações mais detalhadas sobre como trabalhar com canais desse tipo, confira [Transmissão ao vivo com codificadores locais que criam transmissões de múltiplas taxas de bits](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – Escolha esse valor se você pretende usar os Serviços de Mídia para codificar sua transmissão ao vivo de taxa de bits única para uma transmissão de múltiplas taxas de bits. Lembre-se de que há um impacto de cobrança para codificação ativa e você deve se lembrar que deixar um canal de codificação ativo no estado "Em execução" incorrerá em cobranças.  É recomendável parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra.

> [!NOTE]
> Este tópico discute os atributos de canais que estão habilitados para executar codificação ao vivo (tipo de codificação **Padrão** ). Para obter informações sobre como trabalhar com canais que não estão habilitados a realizar a codificação ativa, confira [Transmissão ao vivo com codificadores locais que criam transmissões de múltiplas taxas de bits](media-services-live-streaming-with-onprem-encoders.md).
> 
> Certifique-se de examinar a seção [Considerações](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Implicações de cobrança
Um canal de codificação ativo começará a ser cobrado assim que seu estado mudar para "Em execução" por meio da API.   Você também pode exibir o estado no Portal do Azure ou na ferramenta Gerenciador de Serviços de Mídia do Azure (https://aka.ms/amse).

A tabela a seguir mostra como os estados de Canal são mapeados para os estados de cobrança na API e no Portal do Azure. Os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Em execução" por meio da API ou no estado "Pronto" ou "Streaming" no Portal do Azure, a cobrança estará ativa.
Para parar a cobranças adicionais do Canal, você terá de Parar o Canal por meio da API ou no Portal do Azure.
Você é responsável por parar seus canais quando terminar com o canal de codificação ativo.  A falha ao interromper um canal de codificação resultará em cobrança contínua.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Os estados de canal e como eles são mapeados para o modo de cobrança
O estado atual de um canal. Os valores possíveis incluem:

* **Parado**. Esse é o estado inicial do canal após sua criação (a menos que AutoStart tenha sido selecionado no Portal). Nenhuma cobrança ocorre nesse estado. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
* **Iniciando**. O canal está sendo iniciado. Não há cobrança nesse estado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
* **Em execução**. O canal é capaz de processar transmissões ao vivo. Agora o uso está sendo cobrado. Você deve parar o canal para evitar a cobrança adicional. 
* **Parando**. O canal está sendo parado. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.
* **Excluindo**. O canal está sendo excluído. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança. 

| Estado de canal | Indicadores da interface do usuário do portal | Trata-se de cobrança? |
| --- | --- | --- |
| Iniciando |Iniciando |Nenhum (estado transitório) |
| Executando |Pronto (nenhum programa em execução)<br/>ou<br/>Streaming (há pelo menos um programa em execução) |YES |
| Parando |Parando |Nenhum (estado transitório) |
| Parado |Parado |Não |

### <a name="automatic-shut-off-for-unused-channels"></a>Desligamento automático para canais não usados
A partir de 25 de janeiro de 2016, os Serviços de Mídia distribuíram uma atualização que interrompe automaticamente um canal (com codificação ativa desabilitada) depois que ele tiver sido executado em um estado não utilizado por um longo período. Isso se aplica aos canais que não tem programas ativos e que não receberam um feed de contribuição de entrada por um período de tempo estendido.

O limite para um período não utilizado é de 12 horas, mas está sujeito a mudanças.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho da codificação ativa
O diagrama a seguir representa um fluxo de trabalho de streaming ao vivo em que um canal recebe um fluxo de taxa de bits única em um dos seguintes protocolos: RTMP ou Smooth Streaming ; em seguida, ele codifica o fluxo em um fluxo de múltiplas taxas de bits. 

![Fluxo de trabalho ao vivo][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Cenário comum de transmissão ao vivo
A seguir, as etapas gerais envolvidas na criação de aplicativos comuns de streaming ao vivo.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento ao vivo é de 8 horas.
>
> Há um impacto de cobrança para codificação ativa e você deve se lembrar que deixar um canal de codificação ativo no estado "Em execução" incorrerá em cobranças por hora. É recomendável parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra. 

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ao vivo local que pode gerar um fluxo de taxa de bits **única** em um dos seguintes protocolos: RTMP ou Smooth streaming. 

    Essa etapa também pode ser realizada após a criação do canal.
2. Crie e inicie um Canal. 
3. Recupere a URL de ingestão do canal. 

    A URL de ingestão é usada pelo codificador ao vivo para enviar o fluxo para o canal.
4. Recupere a URL de visualização do canal. 

    Use essa URL para verificar se o canal está recebendo corretamente o fluxo ao vivo.
5. Crie um programa. 

    Ao usar o Portal do Azure, a criação de um programa também cria um ativo. 

    Ao usar o SDK do .NET ou REST, você precisa criar um ativo e especificar o uso desse ativo durante a criação de um programa. 
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. O ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **Executando**. 

7. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.
8. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar um anúncio. O anúncio é inserido no fluxo de saída.
9. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.
10. Exclua o programa (e, opcionalmente, exclua o ativo).   

> [!NOTE]
> É muito importante não esquecer de interromper um canal de codificação ativa. Lembre-se de que há um impacto de cobrança por hora para codificação ativa e você deve se lembrar que deixar um canal de codificação ativo no estado "Em execução" incorrerá em cobranças.  É recomendável parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Configurações de entrada (ingestão) do canal
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Protocolo de streaming de ingestão
Se o **Tipo de codificador** está definido como **Standard**, as opções válidas são:

* **RTMP** de taxa de bits única
* **MP4 fragmentado** de taxa de bits única (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP de taxa de bits única
Considerações:

* O fluxo de entrada não pode conter vídeo com múltiplas taxas de bits
* O fluxo de vídeo deve ter uma taxa de bits média abaixo de 15 Mbps
* O fluxo de áudio deve ter uma taxa de bits média abaixo de 1 Mbps
* A seguir, a lista dos codecs com suporte:
* Vídeo MPEG-4 AVC / H.264
* Linha de base, Principal, Perfil Alto (8 bits 4:2:0)
* Perfil Alto 10 (10 bits 4:2:0)
* Perfil Alto 422 (10 bits 4:2:2)
* Áudio MPEG-2 AAC-LC
* Mono, Estéreo, Surround (5.1, 7.1)
* Taxa de amostragem de 44,1 kHz
* Empacotamento de ADTS estilo MPEG-2
* Os codificadores recomendados incluem:
* [Wirecast de Telestream](media-services-configure-wirecast-live-encoder.md)
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de taxa de bits única (Smooth Streaming)
Caso de uso típico:

Use codificadores ao vivo locais de fornecedores como Elemental Technologies, Ericsson, Ateme e Envivio para enviar o fluxo de entrada pela Internet aberta para um data center próximo do Azure.

Considerações:

O mesmo que o mencionado para [RTMP com taxa de bits única](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Outras considerações
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* A resolução máxima para o fluxo de entrada de vídeo é 1920x1080, e no máximo 60 campos/segundo se entrelaçado ou 30 quadros por segundo se progressivo.

### <a name="ingest-urls-endpoints"></a>URLs de ingestão (pontos de extremidade)
Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, de modo que o codificador possa enviar fluxos por push para seus canais.

Você pode obter as URLs de ingestão depois de criar um canal. Para obter essas URLs, o canal não precisa estar no estado **Executando** . Quando estiver pronto para começar a enviar dados por push para o canal, ele deve estar no estado **Executando** . Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir a transmissão ao vivo com MP4 fragmentado (Smooth Streaming) em uma conexão TLS. Para ingerir por TLS, certifique-se de atualizar a URL de inserção para HTTPS. Atualmente, o AMS não dá suporte a TLS com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para publicar vídeo para esse canal. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Visualização de canal
### <a name="preview-urls"></a>URLs de visualização
Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização quando você cria o canal. Para obter a URL, o canal não precisa estar no estado **Executando** .

Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

> [!NOTE]
> Atualmente, o fluxo de visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado.  Você pode usar um player hospedado no Portal do Azure para exibir a transmissão.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para conectar-se ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo de IPs usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IPs usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Configurações de codificação ao vivo
Esta seção descreve como as configurações para o codificador ativo no canal podem ser ajustadas, quando o **Tipo de codificação** de um canal é definido como **Standard**.

> [!NOTE]
> O feed de contribuição só pode conter uma única faixa de áudio – a inserção de várias faixas de áudio não é suportada atualmente. Ao realizar a codificação ativa com [a codificação ativa local](media-services-live-streaming-with-onprem-encoders.md), é possível enviar um feed de contribuição no protocolo de Smooth Streaming que contém várias faixas de áudio.
> 
> 

### <a name="ad-marker-source"></a>Origem do marcador de anúncio
Você pode especificar a origem para sinais de marcadores de anúncio. O valor padrão é **Api**, que indica que o codificador ativo no Canal deve escutar uma **API do marcador de anúncio** assíncrona.

### <a name="cea-708-closed-captions"></a>Legendas CEA 708
Um sinalizador opcional que informa o codificador ao vivo para ignorar quaisquer dados de legendas CEA 708 incorporados no vídeo de entrada. Quando o sinalizador é definido como false (padrão), o codificador vai detectar e inserir novamente os dados CEA 708 nos fluxos de vídeo de saída.

#### <a name="index"></a>Índice
É recomendável para enviar um fluxo de transporte de programa único (SPTS). Se o fluxo de entrada contém vários programas, o codificador ao vivo no canal analisa a PMT (tabela de mapa de programa) na entrada, identifica as entradas que têm um nome de tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3 System-B ou MPEG-2 Private PES ou áudio MPEG-1 ou áudio MPEG-2, e organiza-os na ordem especificada na PMT. O índice baseado em zero, em seguida, é usado para acompanhar a enésima entrada nesse arranjo.

#### <a name="language"></a>Idioma
O identificador de idioma do fluxo de áudio, em conformidade com ISO 639-2, como ENG. Se não estiver presente, o padrão é UND (indefinido).

### <a name="system-preset"></a><a id="preset"></a>Predefinição do sistema
Especifica a predefinição a ser usada pelo codificador ao vivo dentro deste canal. Atualmente, o único valor permitido é **Default720p** (padrão).

**Default720p** codificará o vídeo nas seis camadas a seguir.

#### <a name="output-video-stream"></a>Fluxo de vídeo de saída

| Taxa de bits | Largura | Altura | MáxFPS | Perfil | Nome do fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alto |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alto |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alto |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alto |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alto |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Fluxo de áudio de saída

O áudio é codificado para AAC-LC estéreo a 128 kbps, taxa de amostragem de 48 kHz.

## <a name="signaling-advertisements"></a>Sinalização de anúncios
Quando o canal está com codificação ao vivo habilitada, você tem um componente em seu pipeline de processamento de vídeo e pode manipulá-lo. Você pode sinalizar para o canal inserir slates e/ou anúncios no fluxo de saída de taxa de bits adaptável. Slates ainda são imagens que você pode usar para cobrir a transmissão de entrada ao vivo em certos casos (por exemplo, durante um intervalo comercial). Sinais de publicidade, são sinais sincronizados que você insere no fluxo de saída para informar o player de vídeo para realizar ações especiais – por exemplo, para alternar para um anúncio no momento apropriado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para uma visão geral do mecanismo de sinalização SCTE-35 usado para essa finalidade. Abaixo está um cenário típico que você poderia implementar em seu evento ao vivo.

1. Faça com que seus visualizadores obtenham uma imagem PRÉ-EVENTO antes do início do evento.
2. Faça com que seus visualizadores obtenham uma imagem PÓS-EVENTO após o término do evento.
3. Faça com que seus visualizadores obtenham uma imagem do EVENTO-DE-ERRO se houver um problema durante o evento (por exemplo, falta de energia no Estádio).
4. Envie uma imagem de INTERVALO-COMERCIAL para ocultar a transmissão do evento ao vivo durante um intervalo comercial.

A seguir estão as propriedades que você pode definir ao sinalizar anúncios. 

### <a name="duration"></a>Duration
A duração, em segundos, do intervalo comercial. Isso deve ser um valor positivo diferente de zero para iniciar o intervalo comercial. Quando um intervalo comercial está em andamento e a duração é definida como zero com o CueId correspondente ao intervalo comercial em curso, esse intervalo é cancelado.

### <a name="cueid"></a>CueId
Uma ID exclusiva para o intervalo comercial, para ser usado pelo aplicativo downstream para executar as ações apropriadas. Deve ser um número inteiro positivo. Você pode definir esse valor como qualquer inteiro positivo aleatório ou usar um sistema de upstream para acompanhar as IDs de indicação. Certifique-se normalizar quaisquer IDs para números inteiros positivos antes de enviá-las pela API.

### <a name="show-slate"></a>Mostrar slate
Opcional. Sinaliza o codificador dinâmico para alternar para a imagem [fixa padrão](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante um intervalo comercial e ocultar o feed de vídeo de entrada. O áudio também é desligado durante o slate. O padrão é **false**. 

A imagem usada será aquela especificada por meio da propriedade de ID do ativo de slate padrão no momento da criação do canal. O slate será estendido para ajustar-se ao tamanho da imagem de exibição. 

## <a name="insert-slate--images"></a>Inserir imagens fixas
O codificador ao vivo no canal pode ser sinalizado para alternar para uma imagem slate. Ele também pode ser sinalizado para encerrar um slate em curso. 

O codificador ao vivo pode ser configurado para alternar para uma imagem slate e ocultar o sinal de vídeo de entrada em determinadas situações – por exemplo, durante um intervalo comercial. Se um slate desse tipo não estiver configurado, o vídeo de entrada não é mascarado durante esse intervalo comercial.

### <a name="duration"></a>Duration
A duração do slate em segundos. Isso deve ser um valor positivo diferente de zero para iniciar o slate. Se houver um slate em andamento e uma duração de zero for especificada, esse slate será encerrado.

### <a name="insert-slate-on-ad-marker"></a>Inserir o slate no marcador de anúncio
Quando definida como true, essa configuração configura o codificador ao vivo para inserir uma imagem slate durante um intervalo comercial. O valor padrão é true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>ID de ativo de slate padrão

Opcional. Especifica a ID do ativo de Serviços de Mídia que contém a imagem do slate. O padrão é nulo. 


> [!NOTE] 
> Antes de criar o Canal, a imagem fixa com as restrições a seguir deverá ser carregada como um ativo dedicado (nenhum outro arquivo deve estar nesse ativo). Essa imagem é usada somente quando o codificador dinâmico estiver inserindo uma imagem fixa devido a um intervalo de anúncio, ou tiver sido assinalado explicitamente para inserir uma imagem fixa. Atualmente, não há opção para usar uma imagem personalizada quando o codificador dinâmico entrar em um estado 'sinal de entrada perdido'. Você pode votar para esse recurso [aqui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* No máximo 1920x1080 na resolução.
* No máximo 3 megabytes de tamanho.
* O nome do arquivo deve ter uma extensão *.jpg.
* A imagem deve ser carregada em um ativo como o único AssetFile que ativo e esse AssetFile deve ser marcado como o arquivo primário. Esse ativo não pode ser armazenado criptografado.

Se a **ID padrão do ativo de imagem fixa** não for especificada e **inserir imagem fixa no marcador de anúncio** estiver definido como **verdadeiro**, uma imagem padrão dos Serviços de Mídia do Azure será usada para ocultar a transmissão de vídeo de entrada. O áudio também é desligado durante o slate. 

## <a name="channels-programs"></a>Programas do canal
Um canal é associado a programas que permitem que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Os canais gerenciam os programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele Canal.

Você pode especificar o número de horas pelo qual você deseja manter o conteúdo gravado para o programa, definindo a duração da **Janela de Arquivo** . Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas. A duração da janela de arquivo morto também determina o número máximo de tempo que os clientes podem buscar na posição atual em tempo real. Os programas podem ser executados pelo período de tempo especificado, mas o conteúdo que estiver por trás da janela de tamanho será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blob de blocos na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner. Para publicar o programa para que seus clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um Canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Você não deve reutilizar os programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programação de aplicativos de streaming ao vivo.

Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento. 

Para excluir o conteúdo arquivado, interrompa e exclua o programa e, em seguida, exclua o ativo associado. Não é possível excluir um ativo se este for usado por um programa; o programa deve ser excluído primeiro. 

Mesmo depois que você parar e excluir o programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo.

Se desejar manter o conteúdo arquivado mas ele não está disponível para streaming, exclua o localizador de streaming.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obtenção uma visualização em miniatura de uma transmissão ao vivo
Quando a codificação ao vivo está habilitada, agora você pode obter uma visualização da transmissão ao vivo quando ela atinge o canal. Isso pode ser uma ferramenta valiosa para verificar se sua transmissão ao vivo está realmente chegando ao canal. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Estados de canal e como os estados são mapeados para o modo de cobrança
O estado atual de um canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
* **Iniciando**. O canal está sendo iniciado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
* **Em execução**. O canal é capaz de processar transmissões ao vivo.
* **Parando**. O canal está sendo parado. Nenhuma atualização ou streaming é permitido durante esse estado.
* **Excluindo**. O canal está sendo excluído. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança. 

| Estado de canal | Indicadores da interface do usuário do portal | Cobrado? |
| --- | --- | --- |
| Iniciando |Iniciando |Nenhum (estado transitório) |
| Executando |Pronto (nenhum programa em execução)<br/>ou<br/>Streaming (há pelo menos um programa em execução) |Sim |
| Parando |Parando |Nenhum (estado transitório) |
| Parado |Parado |Não |

> [!NOTE]
> Atualmente, a média de início de Canal é de cerca de 2 minutos, mas às vezes pode levar até 20 minutos ou mais. A redefinição de canal pode levar até 5 minutos.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Considerações
* Quando um Canal do tipo de codificação **Standard** perde um feed de fonte/contribuição de entrada, ele compensa isso substituindo o vídeo/áudio de origem por uma imagem fixa de erro e silêncio. O Canal continuará emitindo uma imagem fixa até que o feed de entrada/contribuição seja retomado. É recomendável que um canal ao vivo não seja deixado em tal estado por mais de 2 horas. Além desse ponto, o comportamento do canal na reconexão de entrada não será garantido, nem seu comportamento em resposta a um comando Reset. Será necessário parar o Canal, excluí-lo e criar um novo.
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa interromper o programa. Antes de redefinir o canal, reinicie o programa.
* Um canal pode ser interrompido somente quando estiver no estado Executando e todos os programas no canal tiverem sido interrompidos.
* Por padrão, você pode adicionar somente 5 canais à sua conta de Serviços de Mídia. Essa é uma cota flexível em todas as novas contas. Para obter mais informações, consulte [cotas e limitações](media-services-quotas-and-limitations.md).
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* Você será cobrado apenas quando o canal estiver no estado **Executando** . Para obter mais informações, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) seção.
* Atualmente, a duração máxima recomendada de um evento ao vivo é de 8 horas. 
* Verifique se o ponto de extremidade de streaming do qual você deseja transmitir nosso conteúdo está no estado **Executando**.
* A predefinição de codificação usa a noção de "taxa de quadros máxima" de 30 fps. Portanto, se a entrada é 60fps/59,94i, os quadros de entrada são descartados/divididos para 30/29,97 fps. Se a entrada é 50fps/50i, os quadros de entrada são descartados/divididos para 25 fps. Se a entrada é 25 fps, a saída permanece em 25 fps.
* Não se esqueça de PARAR SEUS CANAIS quando terminar. Caso contrário, a cobrança continuará.

## <a name="known-issues"></a>Problemas conhecidos
* O tempo de inicialização do canal foi aprimorado para uma média de 2 minutos, mas em momentos de maior demanda pode ainda levar até 20 minutos ou mais.
* As imagens fixas devem estar de acordo com as restrições descritas [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se você tentar criar um Canal com uma imagem fixa padrão maior que 1920x1080, a solicitação eventualmente será um erro.
* Mais uma vez... não se esqueça de PARAR SEUS CANAIS quando concluir o streaming. Caso contrário, a cobrança continuará.

## <a name="need-help"></a>Precisa de ajuda?

Abra um tíquete de suporte navegando até [Nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Próxima etapa

Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Entregando eventos de transmissão ao vivo com os serviços de mídia do Azure](media-services-overview.md)

[Criar canais que realizam codificação dinâmica de um fluxo com taxa de bits única para fluxo com taxa de bits adaptável com o Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Criar canais que realizam codificação dinâmica de um fluxo com taxa de bits única para fluxo com taxa de bits adaptável com o SDK do .NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerenciar canais com a API REST](/rest/api/media/operations/channel)

[Conceitos dos serviços de mídia](media-services-concepts.md)

[Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
