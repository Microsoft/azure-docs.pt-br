---
title: "Especificação da ingestão dinâmica de MP4 fragmentado para os Serviços de Mídia do Azure | Microsoft Docs"
description: "Esta especificação descreve o protocolo e o formato para ingestão de transmissão ao vivo baseada em MP4 fragmentado para os Serviços de Mídia do Azure. Você pode usar os Serviços de Mídia do Azure para transmitir eventos ao vivo e transmitir conteúdo em tempo real usando o Azure como a plataforma de nuvem. Este documento também aborda as práticas recomendadas para criação de mecanismos robustos de ingestão dinâmica e altamente redundantes."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 6250b73504bec765b8299060a29e84e771791cc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificação da ingestão dinâmica de MP4 fragmentado para os Serviços de Mídia do Azure
Esta especificação descreve o protocolo e o formato para ingestão de transmissão ao vivo baseada em MP4 fragmentado para os Serviços de Mídia do Azure. Os Serviços de Mídia fornecem um serviço de transmissão ao vivo, que os clientes podem usar para transmitir eventos ao vivo e difundir conteúdo em tempo real usando o Azure como a plataforma de nuvem. Este documento também aborda as práticas recomendadas para criação de mecanismos robustos de ingestão dinâmica e altamente redundantes.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
As palavras-chave “DEVE”, “NÃO DEVE”, “OBRIGATÓRIO”, “RECOMENDADO”, “PODE” E “OPCIONAL” neste documento devem ser interpretadas conforme descritas na RFC 2119.

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O diagrama a seguir mostra a arquitetura de alto nível do serviço de transmissão ao vivo nos Serviços de Mídia:

1. Um codificador dinâmico envia por push feeds dinâmicos para os canais que são criados e provisionados pelo SDK dos Serviços de Mídia do Azure.
2. Os pontos de extremidade de canais, programas e streaming nos Serviços de Mídia do trata de todas as funcionalidades de transmissão ao vivo, incluindo ingestão, formatação, DVR na nuvem, segurança, escalabilidade e redundância.
3. Se preferirem, os clientes podem optar por implantar uma camada de Rede de Distribuição de Conteúdo entre o ponto de extremidade de streaming e os pontos de extremidade do cliente.
4. Os pontos de extremidade do cliente transmitem do ponto de extremidade de streaming usando protocolos de Streaming Adaptável HTTP. Os exemplos incluem Microsoft Smooth Streaming, Streaming adaptável dinâmico via HTTP (DASH ou MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![fluxo de ingestão][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato de fluxo de bits – MP4 fragmentado ISO 14496-12
O formato de conexão para ingestão de transmissão ao vivo abordado neste documento se baseia no [ISO-14496-12]. Para ver uma explicação detalhada do formato MP4 fragmentado e das extensões para arquivos de vídeo sob demanda e ingestão de transmissão ao vivo, consulte [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definições de formato de ingestão ao vivo
A lista a seguir descreve as definições de formato especial que se aplicam à ingestão dinâmica nos Serviços de Mídia do Microsoft Azure:

1. As caixas **ftyp**, **Live Server Manifest Box** e **moov** DEVEM ser enviadas com cada solicitação (HTTP POST). Essas caixas DEVEM ser enviada no início do fluxo e a qualquer momento o codificador deve se reconectar para retomar a ingestão do fluxo. Para saber mais, consulte a Seção 6 em [1].
2. A Seção 3.3.2 em [1] define uma caixa opcional chamada **StreamManifestBox** para ingestão dinâmica. Devido à lógica de roteamento do balanceador de carga do Azure, usar essa caixa é preterido. A caixa NÃO DEVE estar presente durante a ingestão nos Serviços de Mídia. Se essa caixa estiver presente, os Serviços de Mídia vão ignorá-la silenciosamente.
3. A caixa **TrackFragmentExtendedHeaderBox** definida em 3.2.3.2 em [1] DEVE estar presente em cada fragmento.
4. A versão 2 da caixa **TrackFragmentExtendedHeaderBox** DEVE ser usada para gerar segmentos de mídia com URLs idênticas em vários datacenters. O campo do índice de fragmento é OBRIGATÓRIO para failover entre datacenters de formatos de streaming baseados em índice, como Apple HLS e MPEG-DASH baseado em índice. Para habilitar o failover entre datacenters, o índice de fragmento DEVE ser sincronizado em vários codificadores e aumentado em incrementos de 1 para cada fragmento de mídia sucessivo, mesmo entre reinícios ou falhas do codificador.
5. A Seção 3.3.6 em [1] define uma caixa chamada **MovieFragmentRandomAccessBox** (**mfra**) que PODE ser enviada no fim da ingestão dinâmica para indicar o fim do fluxo (EOS) para o canal. Devido à lógica de ingestão dos Serviços de Mídia, o uso do EOS foi preterido e a caixa **mfra** para ingestão dinâmica NÃO DEVE ser enviada. Se enviada, os Serviços de Mídia vão ignorá-la silenciosamente. Para redefinir o estado do ponto de ingestão, recomendamos que você use [Redefinir canal](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Também é recomendável que você use [Parar programa](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para encerrar uma apresentação e o fluxo.
6. A duração de fragmento de MP4 DEVE ser constante, para reduzir o tamanho dos manifestos do cliente. Uma duração constante de fragmento de MP4 também melhora a heurística de download do cliente por meio do uso de marcas de repetição. A duração PODE flutuar para compensar as taxas de quadro de números não inteiros.
7. A duração do fragmento MP4 DEVE estar entre segundos 2 e 6 segundos, aproximadamente.
8. Os carimbos de data/hora e índices do fragmento MP4 (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e `fragment_index`) DEVEM chegar na ordem crescente. Embora os Serviços de Mídia sejam resilientes a fragmentos duplicados, sua capacidade de reordenar os fragmentos de acordo com a linha do tempo da mídia é limitada.

## <a name="4-protocol-format--http"></a>4. Formato de protocolo – HTTP
A ingestão dinâmica com base no MP4 fragmentado ISO para Serviços de Mídia usa uma solicitação HTTP POST padrão de longa execução para transmitir ao serviço dados de mídia codificados que são empacotados no formato MP4 fragmentado. Cada HTTP POST envia um fluxo de bits (“fluxo”) MP4 fragmentado completo, começando do início com as caixas de cabeçalho (caixas **ftyp**, **Live Server Manifest Box** e **moov**) e continuando com uma sequência de fragmentos (caixas **moof** e **mdat**). Para obter a sintaxe de URL para a solicitação HTTP POST, consulte a seção 9.2 em [1]. Veja um exemplo de URL de POST: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisitos
Veja os requisitos em detalhes:

1. O codificador DEVE iniciar a difusão enviando uma solicitação HTTP POST com um “corpo” vazio (comprimento de conteúdo zero) usando a mesma URL de ingestão. Isso pode ajudar o codificador a detectar rapidamente se o ponto de extremidade de ingestão dinâmica é válido e se há alguma autenticação ou outras condições obrigatórias. De acordo com o protocolo HTTP, o servidor não pode enviar uma resposta HTTP de volta até que a solicitação inteira, incluindo o corpo do POST, seja recebida. Devido à natureza de longa execução do evento ao vivo, sem essa etapa, o codificador pode não ser capaz de detectar algum erro até que termine de enviar todos os dados.
2. O codificador DEVE tratar os erros ou desafios de autenticação por causa de (1). Se (1) for bem-sucedido com uma resposta 200, continue.
3. O codificador DEVE iniciar uma nova solicitação HTTP POST com o fluxo de MP4 fragmentado. A carga DEVE começar com as caixas de cabeçalho seguidas pelos fragmentos. Observe que as caixas **ftyp**, **Live Server Manifest Box** e **moov** (nessa ordem) DEVEM ser enviadas com cada solicitação, mesmo que o codificador tenha que se reconectar porque a solicitação anterior foi encerrada antes do fim do fluxo. 
4. O codificador DEVE usar a codificação de transferência em bloco para carregamento, pois é impossível prever o comprimento de conteúdo inteiro do evento ao vivo.
5. Quando o evento tiver acabado, após envio do último fragmento, o codificador DEVERÁ encerrar normalmente a sequência de mensagens da codificação de transferência em bloco (a maioria das pilhas de cliente HTTP a trata automaticamente). O codificador DEVE aguardar o serviço retornar o código da resposta final e, em seguida, encerrar a conexão. 
6. O codificador NÃO DEVE usar o substantivo `Events()`, conforme descrito na seção 9.2 em [1] para ingestão dinâmica nos Serviços de Mídia.
7. Se a solicitação HTTP POST termina ou o tempo limite é ultrapassado com um erro TCP antes do término do fluxo, o codificador DEVE emitir uma nova solicitação POST usando uma nova conexão e seguindo os requisitos anteriores. Além disso, o codificador DEVE reenviar os dois fragmentos de MP4 anteriores para cada faixa no fluxo e continuar sem introduzir uma descontinuidade na linha do tempo da mídia. O reenvio dos dois últimos fragmentos MP4 de cada faixa garante que não haja perda de dados. Em outras palavras, se um fluxo contiver uma faixa de áudio e vídeo, e a solicitação POST atual falhar, o codificador deverá se reconectar e reenviar os últimos dois fragmentos da faixa de áudio, que anteriormente foram enviadas com êxito, e os dois últimos fragmentos da faixa de vídeo, que anteriormente foram enviadas com êxito, para garantir que não haja nenhuma perda de dados. O codificador DEVE manter um buffer de “avanço” da mídia de fragmentos, que ele reenvia quando se reconecta.

## <a name="5-timescale"></a>5. Escala de tempo
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descreve o uso da escala de tempo para **SmoothStreamingMedia** (Seção 2.2.2.1), **StreamElement** (Seção 2.2.2.3), **StreamFragmentElement** (2.2.2.6) e **LiveSMIL** (Seção 2.2.7.3.1). Se o valor da escala de tempo não estiver presente, o valor padrão usado será 10.000.000 (10 MHz). Embora a especificação de formato Streaming Suave não bloqueie o uso de outros valores de escala de tempo, a maioria das implementações de codificador usa esse valor padrão (10 MHz) para gerar dados de ingestão de Streaming Suave. Devido ao recurso [Empacotamento Dinâmico de Mídia do Azure](media-services-dynamic-packaging-overview.md), é recomendável que você use a escala de tempo de 90 kHz para fluxos de vídeo e 44,1 KHz ou 48,1 KHz para fluxos de áudio. Se diferentes valores de escala de tempo forem usados para diferentes fluxos, a escala de tempo do nível de fluxo DEVERÁ ser enviada. Para obter mais informações, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definição de “fluxo”
Fluxo é a unidade básica da operação na ingestão dinâmica para compor apresentações ao vivo e tratar do failover de streaming e cenários de redundância. Fluxo é definido como um único fluxo de bits MP4 fragmentado exclusivo que pode conter uma única faixa ou várias faixas. Uma apresentação ao vivo completa pode conter um ou mais fluxos, dependendo da configuração dos codificadores dinâmicos. Os exemplos a seguir ilustram as várias opções de uso de fluxos para compor uma apresentação ao vivo completa.

**Exemplo:** 

Um cliente deseja criar uma apresentação para transmissão ao vivo que inclui as seguintes taxas de bits de áudio/vídeo:

Vídeo – 3000 kbps, 1500 kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas em um fluxo
Nessa opção, um único codificador gera todas as faixas de áudio/vídeo e, em seguida, empacota-los em um fluxo de bits MP4 fragmentado. O fluxo de bits MP4 fragmentado é enviado por meio de uma única conexão de HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação ao vivo.

![Fluxos de uma faixa][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa em um fluxo separado
Nessa opção, os codificadores colocam uma faixa em cada fluxo de bits MP4 fragmentado e postam todos os fluxos em conexões HTTP separadas. Isso pode ser feito com um codificador ou com vários codificadores. A ingestão dinâmica enxerga esta apresentação ao vivo como sendo composta por quatro fluxos.

![Fluxos de faixas separadas][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agrupar faixas de áudio com a faixa de vídeo de taxa de bits mais baixa em um fluxo
Nessa opção, o cliente escolhe agrupar a faixa de áudio com a faixa de vídeo de taxa de bits mais baixa em um único fluxo de bits MP4 fragmentado e deixar as outras duas faixas de vídeo como fluxos separados. 

![Fluxos com faixas de áudio e vídeo][image4]

### <a name="summary"></a>Resumo
Essa não é uma lista completa de todas as opções possíveis de ingestão para este exemplo. Na verdade, qualquer agrupamento de faixas em fluxos tem suporte da ingestão dinâmica. Os clientes e fornecedores de codificadores podem escolher suas próprias implementações com base na complexidade de engenharia, na capacidade do codificador e nas considerações de failover e redundância. No entanto, na maioria dos casos, há apenas uma faixa de áudio para toda a apresentação ao vivo. Portanto, é importante garantir a integridade do fluxo de ingestão que contém a faixa de áudio. Geralmente, essa consideração resulta na colocação da faixa de áudio em seu próprio fluxo (como na Opção 2) ou no agrupamento dela com a faixa de vídeo de taxa de bits mais baixa (como na Opção 3). Além disso, para melhor redundância e tolerância a falhas, enviar a mesma faixa de áudio em dois fluxos diferentes (Opção 2 com faixas de áudio redundantes) ou agrupar a faixa de áudio com pelo menos duas faixas de vídeo de taxa de bits mais baixa (Opção 3 com áudio agrupado em pelo menos dois fluxos de vídeo) é altamente recomendável para ingestão dinâmica nos Serviços de Mídia.

## <a name="7-service-failover"></a>7. Failover de serviço
Dada a natureza da transmissão ao vivo, o bom suporte ao failover é essencial para garantir a disponibilidade do serviço. Os Serviços de Mídia foram desenvolvidos para tratar os vários tipos de falha, incluindo erros de rede, erros de servidor e problemas de armazenamento. Quando usado em conjunto com a lógica apropriada de failover do lado do codificador dinâmico, os clientes podem obter um serviço de transmissão ao vivo altamente confiável da nuvem.

Nesta seção, abordamos os cenários de failover de serviço. Nesse caso, a falha ocorre em algum lugar dentro do serviço e ela se manifesta como um erro de rede. Veja algumas recomendações de implementação do codificador para tratamento de failover de serviço:

1. Use um tempo limite de 10 segundos para estabelecer a conexão TCP. Se uma tentativa de estabelecer a conexão levar mais de 10 segundos, anule a operação e tente novamente. 
2. Use um tempo limite curto para o envio de partes da mensagem de solicitação HTTP. Se a duração do fragmento MP4 de destino for de N segundos, use um tempo limite de envio entre N e 2 N segundos; por exemplo, se a duração do fragmento MP4 for de 6 segundos, use um tempo limite de 6 a 12 segundos. Na ocorrência de um tempo limite, redefina a conexão, abra uma nova conexão e retome a ingestão de fluxo na nova conexão. 
3. Mantenha um buffer progressivo que tenha os dois últimos fragmentos, para cada trilha, que foram enviados com êxito e por completo ao serviço.  Se a solicitação HTTP POST para um fluxo for encerrada ou o tempo limite se esgotar antes do fim do fluxo, abra uma nova conexão e comece outra solicitação HTTP POST, reenvie os cabeçalhos do fluxo, reenvie os dois últimos fragmentos de cada faixa e retome o fluxo sem introduzir uma descontinuidade na linha do tempo da mídia. Isso reduz a chance de perda de dados.
4. É recomendável que o codificador NÃO limite o número de tentativas para estabelecer uma conexão ou retomar o streaming após a ocorrência de um erro TCP.
5. Após um erro TCP:
  
    a. A conexão atual DEVERÁ ser fechada e uma nova conexão DEVERÁ ser criada para uma nova solicitação HTTP POST.

    b. A nova URL de HTTP POST DEVERÁ ser a mesma URL do POST inicial.
  
    c. O novo HTTP POST DEVERÁ incluir cabeçalhos (caixas **ftyp**, **Live Server Manifest Box** e **moov**) idênticos aos cabeçalhos do fluxo no POST inicial.
  
    d. Os dois últimos fragmentos enviados para cada faixa deverão ser reenviados e o streaming retomado sem introdução de uma descontinuidade na linha do tempo da mídia. Os carimbos de data/hora do fragmento MP4 devem aumentar continuamente, mesmo entre as solicitações HTTP POST.
6. O codificador DEVERÁ encerrar a solicitação HTTP POST se os dados não estiverem sendo enviados a uma taxa proporcional à duração do fragmento MP4.  Uma solicitação HTTP POST que não envia dados pode impedir que os Serviços de Mídia se desconectem rapidamente do codificador no caso de uma atualização de serviço. Por esse motivo, o HTTP POST para faixas esparsas (sinal de anúncio) DEVE ser curto, terminando assim que o fragmento esparso é enviado.

## <a name="8-encoder-failover"></a>8. Failover do codificador
O failover de codificador é o segundo tipo de cenário de failover que precisa ser resolvido para entrega de transmissão ao vivo completa. Nesse cenário, a condição de erro ocorre no lado do codificador. 

![failover do codificador][image5]

As expectativas a seguir aplicam-se do ponto de extremidade da ingestão dinâmica quando ocorre o failover do codificador:

1. Uma nova instância do codificador DEVE ser criada para continuar o streaming, conforme ilustrado no diagrama (fluxo de vídeo de 3.000 k com linha tracejada).
2. O novo codificador DEVE usar a mesma URL para solicitações HTTP POST da instância com falha.
3. A solicitação POST do novo codificador DEVE incluir as mesmas caixas do cabeçalho MP4 fragmentado da instância com falha.
4. O novo codificador DEVE ser sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação ao vivo de modo a gerar as amostras de áudio/vídeo sincronizadas com os limites de fragmento alinhados.
5. O novo fluxo DEVE ser semanticamente equivalente ao fluxo anterior e intercambiável nos níveis de cabeçalho e fragmento.
6. O novo codificador DEVE tentar minimizar a perda de dados. O `fragment_absolute_time` e o `fragment_index` dos fragmentos de mídia DEVEM aumentar do ponto em que o codificador parou pela última vez. O `fragment_absolute_time` e o `fragment_index` DEVEM aumentar de forma contínua, mas é permitida a introdução de uma descontinuidade se necessário. Os Serviços de Mídia ignoram os fragmentos já foram recebidos e processados, então é melhor errar no reenvio de fragmentos do que introduzir descontinuidades na linha do tempo da mídia. 

## <a name="9-encoder-redundancy"></a>9. Redundância do codificador
Para determinados eventos ao vivo críticos que demandam disponibilidade e qualidade de experiência ainda mais altas, é recomendável utilizar codificadores redundantes ativo-ativo para atingir um failover contínuo sem perda de dados.

![redundância do codificador][image6]

Conforme ilustrado neste diagrama, dois grupos de codificadores enviam por push duas cópias de cada fluxo simultaneamente ao serviço dinâmico. Essa configuração tem suporte porque os Serviços de Mídia podem filtrar fragmentos duplicados com base na ID do fluxo e no carimbo de data/hora do fragmento. O fluxo ao vivo e arquivamento resultantes são uma única cópia de todos os fluxos que é a melhor agregação possível de duas fontes. Por exemplo, em um caso extremamente hipotético, desde que haja um codificador (não precisa ser o mesmo) em execução em qualquer dado momento para cada fluxo, o fluxo ao vivo resultante do serviço é contínuo e sem perda de dados. 

Os requisitos para esse cenário são quase os mesmos que os requisitos no caso de “Failover de Codificador”, com a exceção de que o segundo conjunto de codificadores é executado ao mesmo tempo que os codificadores primários.

## <a name="10-service-redundancy"></a>10. Redundância de serviço
Para distribuição global altamente redundante, às vezes, você precisa ter backup entre regiões para lidar com desastres regionais. Ao expandir a topologia “Redundância do codificador”, os clientes podem optar por ter uma implantação de serviço redundante em uma região diferente, que seja conectada com o segundo conjunto de codificadores. Os clientes também podem trabalhar com um provedor de Rede de Distribuição de Conteúdo para implantar um Gerenciador de Tráfego Global na frente das duas implantações de serviços para rotear diretamente o tráfego do cliente. Os requisitos para os codificadores são o mesmo que o caso de "Redundância de codificador". A única exceção é que o segundo conjunto de codificadores precisa ser apontado para outro ponto de extremidade de ingestão dinâmica. O diagrama a seguir mostra essa configuração:

![redundância de serviço][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta seção aborda tipos especiais de formatos de ingestão dinâmica que foram desenvolvidos para tratar cenários específicos.

### <a name="sparse-track"></a>Faixa esparsa
Ao realizar uma apresentação para transmissão ao vivo com uma experiência de cliente rica, muitas vezes, é necessário transmitir eventos sincronizados com o tempo ou sinais em banda com os principais dados de mídia. Um exemplo disso é a inserção dinâmica de anúncios em tempo real. Esse tipo de sinalização de evento é diferente do streaming regular de áudio/vídeo devido à sua natureza esparsa. Em outras palavras, os dados de sinalização normalmente não acontecem de modo contínuo e pode ser difícil prever o intervalo. O conceito de faixas esparsas foi desenvolvido para ingestão e difusão de dados de sinalização em banda.

As etapas a seguir são uma implementação recomendada para ingestão de faixa esparsa:

1. Crie um fluxo de bits MP4 fragmentado separado que contenha apenas faixas esparsas sem faixas de áudio/vídeo.
2. Em **Live Server Manifest Box**, conforme definido na Seção 6 em [1], use o parâmetro *parentTrackName* para especificar o nome da faixa pai. Para saber mais, consulte a Seção 4.2.1.2.1.2 em [1].
3. Em **Live Server Manifest Box**, **manifestOutput** DEVE ser definido como **true**.
4. Devido à natureza esparsa do evento de sinalização, recomendamos o seguinte:
   
    a. No início do evento ao vivo, o codificador envia as caixas de cabeçalho inicial ao serviço, o que permite ao serviço registrar a faixa esparsa no manifesto do cliente.
   
    b. O codificador DEVE encerrar a solicitação HTTP POST quando os dados não estiverem sendo enviados. Um HTTP POST de execução longa que não envia dados pode impedir que os Serviços de Mídia se desconectem rapidamente do codificador no caso de uma atualização de serviço ou reinicialização do servidor. Nesses casos, o servidor de mídia está temporariamente bloqueado em uma operação de recebimento no soquete.
   
    c. Durante o tempo em que os dados de sinalização não estiverem disponíveis, o codificador DEVE fechar a solicitação HTTP POST. Enquanto a solicitação POST estiver ativa, o codificador DEVE enviar dados.

    d. Ao enviar fragmentos esparsos, o codificador pode definir um cabeçalho de comprimento de conteúdo explícito, se disponível.

    e. Durante o envio de fragmentos esparsos com uma nova conexão, o codificador DEVE começar a enviar caixas cabeçalho seguidas pelos novos fragmentos. Isso é para casos em que o failover ocorreu no meio tempo e a nova conexão esparsa está sendo estabelecida para um novo servidor que não tenha visto a faixa esparsa antes.

    f. O fragmento de faixa esparso é disponibilizado para o cliente quando o fragmento da faixa pai correspondente que tiver um valor de carimbo de data/hora igual ou maior for disponibilizado para o cliente. Por exemplo, se o fragmento esparso tiver um carimbo de data/hora de t=1000, espera-se que depois que o cliente veja o carimbo de data/hora do fragmento de vídeo (supondo que o nome da faixa pai seja “vídeo”) de 1000 ou superior, ele possa baixar o fragmento esparso t-1000. Observe que o sinal real pode ser usado para uma posição diferente na linha do tempo da apresentação para os fins pretendidos. No exemplo acima, é possível que o fragmento esparso de t=1000 tenha uma carga XML que seja para inserção de um anúncio em uma posição que esteja alguns segundos depois.

    g. A carga de fragmentos de faixa esparsa pode ser em formatos diferentes (como XML, texto ou binário), dependendo do cenário.

### <a name="redundant-audio-track"></a>Faixa de áudio redundante
Em um cenário normal de HTTP de streaming adaptável (por exemplo, Streaming Suave ou DASH), muitas vezes, há apenas uma faixa de áudio em toda a apresentação. Diferentemente das faixas de vídeo que têm vários níveis de qualidade para o cliente escolher em condições de erro, a faixa de áudio poderá ser um ponto único de falha se a ingestão do fluxo que contém a faixa de áudio for quebrada. 

Para resolver esse problema, os Serviços de Mídia oferecem suporte à ingestão dinâmica de faixas de áudio redundantes. A ideia é a de que a mesma faixa de áudio possa ser enviada várias vezes em diferentes fluxos. Embora o serviço registre a faixa de áudio apenas uma vez no manifesto do cliente, ele pode usar faixas de áudio redundantes como backups para recuperação de fragmentos de áudio se a faixa de áudio primária tiver problemas. Para ingestão de faixas de áudio redundantes, o codificador precisa:

1. Criar a mesma faixa de áudio em vários fluxos de bits MP4 fragmentado. As faixas de áudio redundantes DEVEM ser semanticamente equivalentes com os mesmos carimbos de data/hora do fragmento e intercambiáveis nos níveis de cabeçalho e fragmento.
2. Verifique se a entrada de “áudio” no Live Server Manifest (Seção 6 em [1]) é a mesma para todas as faixas de áudio redundantes.

A implementação a seguir é recomendada para faixas de áudio redundantes:

1. Envie cada faixa de áudio exclusiva em um fluxo por si só. Envie também um fluxo redundante para cada um desses fluxos de faixa de áudio, onde o segundo fluxo difere do primeiro apenas no identificador na URL de HTTP POST: {protocolo}://{endereço do servidor}/{caminho do ponto de publicação}/Fluxos({identificador}).
2. Use fluxos separados para enviar as duas taxas de bits de vídeo mais baixas. Cada um desses fluxos também DEVE conter uma cópia de cada faixa de áudio exclusiva. Por exemplo, quando há suporte para vários idiomas, esses fluxos DEVEM conter faixas de áudio para cada idioma.
3. Use instâncias de servidor (codificador) separadas para codificar e enviar os fluxos redundantes mencionados em (1) e (2). 

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
