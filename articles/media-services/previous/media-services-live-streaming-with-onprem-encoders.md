---
title: Transmissão ao vivo com codificadores locais que criam fluxos com múltiplas taxas de bits – Azure | Microsoft Docs
description: 'Este tópico descreve como configurar um canal que recebe um fluxo ao vivo com múltiplas taxas de bits de um codificador local. Em seguida, o fluxo pode ser entregue para aplicativos de reprodução do cliente por meio de um ou mais pontos de extremidade de streaming, usando um destes protocolos de streaming adaptáveis: HLS, Smooth Streaming, DASH.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: da20e4601b75bcb22546d21f6ad218ac9ba2728b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463775"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Trabalhando com canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não darão mais suporte ao protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Faça a migração de RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Visão geral
Nos Serviços de Mídia do Azure, um *Canal* representa um pipeline para processamento de conteúdo de streaming ao vivo. Um canal recebe fluxos de entrada ao vivo de uma das duas maneiras a seguir:

* Um codificador ativo local envia um fluxo RTMP ou Smooth Streaming (MP4 fragmentado) com múltiplas taxas de bits para o canal que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam pelos canais sem qualquer processamento adicional. Esse método é chamado *passagem*. Um codificador ativo também pode enviar uma transmissão de taxa de bits única para um canal que não está habilitado para a codificação ativa, porém, não recomendamos isso. Os Serviços de Mídia enviam a transmissão aos clientes que a solicitam.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.


* Um codificador dinâmico local envia um fluxo de taxa de bits única para o canal que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Os Serviços de Mídia enviam a transmissão aos clientes que a solicitam.

A partir da versão 2.10 dos Serviços de Mídia, quando você cria um canal, pode especificar como deseja que o canal receba o fluxo de entrada. Você também pode especificar se quer que o canal execute a codificação ativa de seu fluxo. Você tem duas opções:

* **Passagem**: especifique esse valor caso pretenda usar um codificador dinâmico local que tem um fluxo de múltiplas taxas de bits (um fluxo de passagem) como saída. Nesse caso, o fluxo de entrada é transmitido para a saída sem qualquer codificação. Esse é o comportamento de um canal em versão anterior à 2.10. Este artigo fornece detalhes sobre como trabalhar com canais desse tipo.
* **Codificação Ativa**: escolha esse valor se você pretende usar os Serviços de Mídia para codificar sua transmissão ao vivo de taxa de bits única para uma transmissão de múltiplas taxas de bits. Deixar um canal de codificação ativa em um estado **Executando** incorre em encargos de cobrança. Recomendamos parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra. Os Serviços de Mídia enviam a transmissão aos clientes que a solicitam.

> [!NOTE]
> Este artigo aborda os atributos de canais que não estão habilitados para executar a codificação ativa. Para obter informações sobre como trabalhar com canais habilitados a realizar a codificação ativa, confira [Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).
>
>Para obter informações sobre como recomendado em codificadores local, consulte [recomendado em codificadores local](media-services-recommended-encoders.md).

O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo que usa um codificador ativo local para gerar fluxos RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming).

![Fluxo de trabalho ao vivo][live-overview]

## <a id="scenario"></a>Cenário comum de streaming ao vivo
As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de transmissão ao vivo.

1. Conecte uma câmera de vídeo a um computador. Inicie e configure um codificador ativo local que gere um fluxo RTMP com múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming). Para obter mais informações, consulte [Suporte RTMP dos Serviços de Mídia do Azure e Codificadores ao Vivo](https://go.microsoft.com/fwlink/?LinkId=532824).

    Essa etapa também pode ser realizada após a criação do canal.
2. Crie e inicie um Canal.

3. URL de ingestão de recuperação do canal.

    O codificador ativo usa a URL de ingestão para enviar o fluxo para o canal.
4. Recupere a URL de visualização do canal.

    Use essa URL para verificar se o canal está recebendo corretamente o fluxo ao vivo.
5. Crie um programa.

    Ao usar o Portal do Azure, a criação de um programa também cria um ativo.

    Ao usar o SDK do .NET ou REST, você precisa criar um ativo e especificar o uso desse ativo durante a criação de um programa.
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando sua conta dos Serviços de Mídia do Azure é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. O ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **Executando**.

7. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.

8. Opcionalmente, o codificador ao vivo pode ser sinalizado para iniciar um anúncio. O anúncio é inserido no fluxo de saída.

9. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.

10. Exclua o programa (e, opcionalmente, exclua o ativo).     

## <a id="channel"></a>Descrição de um canal e seus componentes relacionados
### <a id="channel_input"></a>Configurações de entrada de canal (ingestão)
#### <a id="ingest_protocols"></a>Protocolo de streaming de ingestão
Os Serviços de Mídia oferecem suporte à ingestão de feeds ao vivo usando MP4 fragmentado de múltipla taxas de bits e RTMP de múltiplas taxas de bits como protocolos de streaming. Quando o protocolo de transmissão de ingestão RTMP é selecionado, dois pontos de extremidade de ingestão (entrada) são criados para o canal:

* **URL principal**: especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP principal do canal.
* **URL secundária** (opcional): especifica a URL totalmente qualificada do ponto de extremidade de ingestão de RTMP secundário do canal.

Use a URL secundária se você quiser melhorar a durabilidade e a tolerância a falhas do fluxo de ingestão (bem como o failover e a tolerância a falhas do codificador), especialmente para os cenários a seguir:

- Codificador único enviando duplamente por push para URLs primária e secundária:

    O objetivo principal deste cenário é fornecer mais flexibilidade ás flutuações de rede e jitters. Alguns codificadores RTMP não tratam bem da desconexão de rede. Quando ocorre uma desconexão de rede, um codificador pode parar de codificar e não envia os dados armazenados no buffer após a reconexão. Isso provoca descontinuidades e perdas de dados. As desconexões da rede podem ocorrer devido a problemas na rede ou uma manutenção no lado do Azure. As URLs primária/secundária reduzem os problemas de rede e também fornecem um processo controlado de atualização. Sempre que ocorrer uma desconexão da rede agendada, os serviços de mídia gerenciam a desconexão primária e secundária e proporcionam uma desconexão de atraso entre os dois. Assim, os codificadores têm tempo para continuar enviando dados e reconectar novamente. A ordem de desconexão pode ser aleatória, mas sempre haverá um atraso entre as URLs primária/secundária ou URLs secundária/primária. Neste cenário, o codificador ainda é o único ponto de falha.

- Vários codificadores, com cada codificador enviando por push para um ponto dedicado:

    Este cenário fornece redundância de codificador e de ingestões. Neste cenário, o condificador1 envia para a URL primária, e o codificador2 envia para a URL secundária. Quando um codificador falha, o outro codificador pode continuar enviando dados. A redundância de dados pode ser mantida porque os Serviços de Mídia não desconectam as URLs primária e secundária ao mesmo tempo. Este cenário pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.  

- Múltiplos codificadores enviando duplamente por push as URLs primária e secundária:

    Neste cenário, os codificadores enviam dados para as URLs primária e secundária. Isso oferece maior confiabilidade e tolerância a falhas, bem como redundância de dados. Este cenário pode tolerar falhas do codificador e desconexões, mesmo se um codificador para de funcionar. Ele pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.  

Para obter informações sobre codificadores ao vivo de RTMP, consulte [Suporte a RTMP dos Serviços de Mídia do Azure e codificadores ao vivo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>URLs de ingestão (pontos de extremidade)
Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, assim, o codificador pode enviar fluxos a seus canais.   

Você pode obter as URLs de ingestão ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado **Executando**. Quando você estiver pronto para começar a enviar dados por push para o canal, ele deve estar no estado **Executando** . Após o canal iniciar a ingestão de dados, você poderá visualizar o fluxo por meio da URL de visualização.

Você tem a opção de ingerir uma transmissão ao vivo de MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS. No momento, você não pode ingerir RTMP sobre SSL.

#### <a id="keyframe_interval"></a>Intervalo de quadro-chave
Quando você usa um codificador ativo local para gerar um fluxo com múltiplas taxas de bits, o intervalo de quadro-chave especifica a duração de GOP (grupo de imagens), conforme usado pelo codificador externo. Depois que o canal recebe esse fluxo de entrada, você pode enviar sua transmissão ao vivo para os aplicativos cliente de reprodução em qualquer um dos seguintes formatos: Smooth Streaming, DASH (Dynamic Adaptive Streaming sobre HTTP) e HLS (HTTP Live Streaming). Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (fragmentos por segmento) com base no intervalo de quadros-chave que é recebido do codificador ativo.

A tabela a seguir mostra como a duração do segmento é calculada:

| Intervalo de quadro-chave | Taxa de empacotamento de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menor ou igual a três segundos |3:1 |Se KeyFrameInterval (ou GOP) for de dois segundos, a proporção de empacotamento de segmento HLS padrão será de três para um. Isso cria um segmento HLS de seis segundos. |
| 3 a 5 segundos |2:1 |Se KeyFrameInterval (ou GOP) for de quatro segundos, a proporção de empacotamento de segmento HLS padrão será de dois para um. Isso cria um segmento HLS de oito segundos. |
| Maior do que cinco segundos |1:1 |Se KeyFrameInterval (ou GOP) for de seis segundos, a proporção de empacotamento de segmento HLS padrão será de um para um. Isso cria um segmento HLS de seis segundos. |

Você pode alterar a proporção de fragmentos por segmento configurando a saída do canal e definindo FragmentsPerSegment em ChannelOutputHls.

Você também pode alterar o valor do intervalo de quadro-chave definindo a propriedade KeyFrameInterval em ChannelInput. Se você definir explicitamente o KeyFrameInterval, o FragmentsPerSegment da proporção de empacotamento do segmento HLS será calculado usando as regras descritas anteriormente.  

Se você definir explicitamente KeyFrameInterval e FragmentsPerSegment, os Serviços de Mídia usarão os valores definidos por você.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para publicar vídeo para esse canal. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Um intervalo de IPs que usa um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo de IPs que usa um endereço IP e uma máscara de sub-rede de valor decimal (por exemplo, 10.0.0.1(255.255.252.0))

Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Visualização de canal
#### <a name="preview-urls"></a>URLs de visualização
Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização quando você cria o canal. Para obter a URL, o canal não precisa estar no estado **Executando** . Depois que o canal inicia a ingestão de dados, você pode visualizar o fluxo.

Atualmente, o fluxo de visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Use o player [Smooth Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) para testar o streaming suave. Você também pode usar um player hospedado no Portal do Azure para exibir a transmissão.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para conectar-se ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Um intervalo de IPs que usa um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo de IPs que usa um endereço IP e uma máscara de sub-rede de valor decimal (por exemplo, 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Saída do canal
Para saber mais sobre a saída do canal, consulte a seção [Intervalo de quadro-chave](#keyframe_interval).

### <a name="channel-managed-programs"></a>Programas gerenciados por canal
Um canal é associado a programas que podem ser usados para controlar a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciam programas. A relação entre canal e programa é semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo um evento cronometrado nesse canal.

Você pode especificar o número de horas pelo qual você deseja manter o conteúdo gravado para o programa, definindo a duração da **Janela de Arquivo** . Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas. A duração da janela de arquivo morto também determina o número máximo de tempo que os clientes podem buscar na posição atual em tempo real. Os programas podem ser executados pelo período de tempo especificado, mas o conteúdo que estiver por trás da janela de tamanho será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blob de blocos na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs nesse contêiner. Para publicar o programa para que seus clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Use esse localizador para criar uma URL de transmissão que você pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, imagine que o requisito se sua empresa seja arquivar seis horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar seis horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Você não deve reutilizar os programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Interrompa o programa sempre que você deseja parar o streaming e o arquivamento do evento.

Para excluir o conteúdo arquivado, interrompa e exclua o programa e, em seguida, exclua o ativo associado. Um ativo não pode ser excluído se um programa o utilizar. O programa deve ser excluído primeiro.

Mesmo após a interrupção e exclusão do programa, os usuários poderão transmitir seu conteúdo arquivado como vídeo sob demanda até você excluir o ativo. Se você quiser manter o conteúdo arquivado mas ele não está disponível para streaming, exclua o localizador de streaming.

## <a id="states"></a>Estados de canal e cobrança
Os valores possíveis para o estado atual de um canal incluem:

* **Parado**: este é o estado inicial do canal após sua criação. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
* **Iniciando**: o canal está sendo iniciado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retornará ao estado **Parado**.
* **Executando**: o canal pode processar transmissões ao vivo.
* **Parando**: o canal está sendo parado. Nenhuma atualização ou streaming é permitido durante esse estado.
* **Excluindo**: o canal está sendo excluído. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança.

| Estado de canal | Indicadores de interface do usuário do portal | Cobrado? |
| --- | --- | --- |
| **Iniciando** |**Iniciando** |Nenhum (estado transitório) |
| **Executando** |**Pronto** (nenhum programa em execução)<p><p>ou o<p>**Streaming** (há pelo menos um programa em execução) |Sim |
| **Parando** |**Parando** |Nenhum (estado transitório) |
| **Interrompido** |**Interrompido** |Não  |

## <a id="cc_and_ads"></a>Legendagem oculta e inserção de anúncios
A tabela a seguir demonstra os padrões com suporte de legendagem oculta e inserção de anúncios.

| Standard | Observações |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |CEA-708 e EIA-608 são padrões de legendagem oculta para os Estados Unidos e o Canadá.<p><p>Atualmente, as legendas têm suporte somente se incluídas no fluxo de entrada codificado. Você precisa usar um codificador de mídia ativo que possa inserir legendas 608 ou 708 no fluxo codificado que é enviado aos Serviços de Mídia. Os Serviços de Mídia entregam o conteúdo com legendas inseridas a seus usuários. |
| TTML em ismt (faixas de texto de Smooth Streaming) |O empacotamento dinâmico dos Serviços de Mídia habilita os clientes a transmitir conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas em .ismt (faixas de texto de Smooth Streaming), você pode entregar o fluxo apenas a clientes de Smooth Streaming. |
| SCTE-35 |SCTE-35 é um sistema de sinalização digital usado para a inserção de anúncios de indicação. Receptores de downstream usam o sinal para unir a publicidade ao fluxo pelo tempo alocado. SCTE-35 deve ser enviado como uma faixa esparsa no fluxo de entrada.<p><p>No momento, o único formato de fluxo de entrada com suporte que transporta sinais de anúncios é o MP4 fragmentado (Smooth Streaming). O único formato de saída com suporte também é Smooth Streaming. |

## <a id="considerations"></a>Considerações
Ao se usar um codificador ao vivo local para enviar um fluxo com múltiplas taxas de bits a um canal, as seguintes restrições são aplicáveis:

* Verifique se que você tem conectividade com a Internet livre suficiente para enviar dados aos pontos de ingestão.
* O uso de uma URL de ingestão secundária requer largura de banda adicional.
* O fluxo com múltiplas taxas de bits de entrada pode ter no máximo 10 níveis de qualidade de vídeo (camadas) e no máximo cinco faixas de áudio.
* A taxa de bits média mais alta para qualquer um dos níveis de qualidade de vídeo deve estar abaixo de 10 Mbps.
* A agregação das taxas de bits médias para todos os fluxos de vídeo e áudio deve estar abaixo de 25 Mbps.
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se você precisar de protocolos diferentes, você deve criar canais separados para cada protocolo de entrada.
* Você pode ingerir uma taxa de bits única em seu canal. Porém, como o canal não processa o fluxo, os aplicativos cliente também receberão um fluxo de taxa de bits única. (Não recomendamos essa opção.)

Confira outras considerações relacionadas ao trabalho com canais e componentes relacionados:

* Sempre que você reconfigurar o codificador ao vivo, chame o método **Redefinir** no canal. Antes de redefinir o canal, você precisa interromper o programa. Antes de redefinir o canal, reinicie o programa.

  > [!NOTE]
  > Ao reiniciar o programa, será necessário associá-lo a um novo ativo e criar um novo localizador. 
  
* Um canal pode ser interrompido somente quando estiver no estado **Executando** e todos os programas no canal tiverem sido interrompidos.
* Por padrão, você pode adicionar apenas cinco canais à sua conta dos Serviços de Mídia. Para saber mais, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).
* Você será cobrado apenas quando o canal estiver no estado **Executando**. Para obter mais informações, consulte a seção [Estados do canal e cobrança](media-services-live-streaming-with-onprem-encoders.md#states).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Recomendado em codificadores local](media-services-recommended-encoders.md)

[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)

[Visão geral e cenários comuns do Serviços de Mídia do Azure](media-services-overview.md)

[Conceitos dos Serviços de Mídia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
