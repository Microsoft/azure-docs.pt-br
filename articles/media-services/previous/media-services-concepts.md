---
title: Conceitos dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico oferece uma visão geral dos conceitos de Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 3b7b821227478d8c6b5859f24c50407f9eb213fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235247"
---
# <a name="azure-media-services-concepts"></a>Conceitos dos Serviços de Mídia do Azure 

Este tópico fornece uma visão geral dos conceitos mais importantes dos Serviços de Mídia.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Ativos e armazenamento
### <a name="assets"></a>Ativos
Um [Ativo](https://docs.microsoft.com/rest/api/media/operations/asset) contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos fluxos de trabalho de codificação e streaming dos serviços de mídia.

Um ativo é mapeado para um contêiner de blob na conta de Armazenamento do Azure e os arquivos no ativo são armazenados como blobs de blocos nesse contêiner. Os blobs de páginas não são compatíveis com os Serviços de Mídia do Azure.

Ao decidir qual conteúdo de mídia para carregar e armazenar em um ativo, as seguintes considerações se aplicam:

* Um ativo deve conter apenas uma instância única e exclusiva do conteúdo de mídia. Por exemplo, uma única edição de um episódio de TV, filme ou anúncio.
* Um ativo não deve conter representações ou edições múltiplas de um arquivo audiovisual. Um exemplo de uso inapropriado de um Ativo é tentar armazenar mais de um episódio de TV, anúncio ou vários ângulos de câmera de uma única produção dentro do ativo. Armazenar representações ou edições diversas de um arquivo audiovisual em um ativo pode resultar em dificuldades ao enviar trabalhos de codificação, transmitir e assegurar a entrega do ativo posteriormente no fluxo de trabalho.  

### <a name="asset-file"></a>Arquivo de ativo
Um [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um arquivo de áudio ou vídeo real que é armazenado em um contêiner de blob. Um arquivo de ativo está sempre associado a um ativo, e um ativo pode conter um ou vários arquivos. A tarefa do Codificador dos serviços de mídia falha se um objeto de arquivo de ativo não estiver associado um arquivo digital em um contêiner de blob.

A instância de **AssetFile** e o arquivo de mídia real são dois objetos diferentes. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelos serviços de mídia sem o uso de APIs de serviços de mídia.

### <a name="asset-encryption-options"></a>Opções de criptografia de ativos
Dependendo do tipo de conteúdo que você deseja carregar, armazenar e fornecer, o Serviços de Mídia fornece várias opções de criptografia dentre as quais você pode escolher.

>[!NOTE]
>Nenhuma criptografia é usada. Esse é o valor padrão. Ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento.

Se você pretende enviar um MP4 usando o download progressivo, use essa opção para carregar seu conteúdo.

**StorageEncrypted** - use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregue-o para o armazenamento do Azure onde ele é armazenado, criptografado em rest. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco. 

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada (por exemplo, AES, PlayReady ou sem criptografia). 

**CommonEncryptionProtected** - use esta opção se você desejar criptografar conteúdo (ou carregar conteúdo que já foi criptografado) com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).

**EnvelopeEncryptionProtected** – Use esta opção se você deseja proteger conteúdo (ou carregar conteúdo já protegido) HLS (HTTP Live Streaming) criptografado com AES. Observe que, se você estiver carregando um HLS já criptografado com AES, ele deverá ter sido criptografado pelo Transform Manager.

### <a name="access-policy"></a>Política de acesso
Uma [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) define permissões (como leitura, gravação e lista) e a duração do acesso a um ativo. Normalmente, você passaria um objeto AccessPolicy para um localizador que deve ser usado para acessar os arquivos contidos em um ativo.

>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não carregamento). Para obter mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contêiner de blob
Um contêiner de blob fornece um agrupamento de um conjunto de blobs. Contêineres de blob são usados nos serviços de mídia como ponto limite para controle de acesso e localizadores de assinatura de acesso compartilhado (SAS) em ativos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contêineres de blob. Um contêiner pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelos serviços de mídia sem o uso de APIs de serviços de mídia.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Localizadores
Os [localizadores](https://docs.microsoft.com/rest/api/media/operations/locator) fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Uma política de acesso é usada para definir as permissões e a duração pela qual um cliente tem acesso a um determinado ativo. Os localizadores podem ter de uma a muitas relações com uma política de acesso, de modo que diferentes localizadores podem fornecer diferentes horas de início e tipos de conexão para diferentes clientes, ao mesmo tempo usando a mesma permissão e as mesmas configurações de duração; no entanto, devido a uma restrição de política de acesso compartilhado definida pelos serviços de armazenamento do Azure, você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 

Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: OnDemandOrigin, usado para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou baixar, de maneira progressiva, mídia; e localizadores de URL SAS, usados para carregar ou baixar arquivos de mídia para\do armazenamento do Azure. 

>[!NOTE]
>A permissão de lista (AccessPermissions.List) não deve ser usada ao criar um localizador OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Uma conta do Serviço de Mídia pode ser associada a uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 500 TB por conta de armazenamento.  Os Serviços de Mídia fornecem ferramentas de nível de SDK que permitem a você gerenciar várias contas de armazenamento e balancear a carga da distribuição de seus ativos durante o carregamento nessas contas com base em métricas ou na distribuição aleatória. Para saber mais, consulte Trabalhando com [Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas
Um [trabalho](https://docs.microsoft.com/rest/api/media/operations/job) normalmente é usado para processar (por exemplo, indexar ou codificar) uma apresentação de áudio/vídeo. Se você estiver processando vários vídeos, crie um trabalho para cada vídeo a ser codificado.

Um trabalho contém metadados sobre o processamento a ser realizado. Cada Trabalho contém uma ou mais [tarefas](https://docs.microsoft.com/rest/api/media/operations/task)que especificam uma tarefa de processamento atômica, seus ativos de entrada e ativos de saída, um processador de mídia e suas configurações associadas. Tarefas em um trabalho podem ser encadeadas, em que o ativo de saída de uma tarefa é determinado como o ativo de entrada para a próxima tarefa. Dessa forma, um trabalho pode conter todo o processamento necessário para uma apresentação de mídia.

## <a id="encoding"></a>Codificação
Os Serviços de Mídia do Azure fornecem várias opções para a codificação de mídia na nuvem.

Ao começar a usar os Serviços de Mídia é importante compreender a diferença entre codecs e formatos de arquivo.
Codecs são o software que implementa os algoritmos de compactação/descompactação. Já os formatos de arquivo são contêineres que armazenam o vídeo compactado.

Os Serviços de Mídia fornecem empacotamento dinâmico, o que permite distribuir seu conteúdo codificado em Smooth Streaming ou MP4 com taxa de bits adaptável em formatos de streaming com suporte dos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming), sem a necessidade de empacotar novamente nesses formatos de streaming.

Para aproveitar o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), você precisa codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável, além de ter pelo menos um ponto de extremidade de streaming Standard ou Premium no estado iniciado.

Os Serviços de Mídia dão suporte aos seguintes codificadores sob demanda descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de trabalho do Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Para saber mais sobre codificadores com suporte, consulte [Codificadores](media-services-encode-asset.md)

## <a name="live-streaming"></a>Transmissão ao vivo
Nos Serviços de Mídia do Azure, um Canal representa um pipeline para processamento de conteúdo de streaming ao vivo. Um Canal recebe transmissões de entrada ao vivo de uma das duas maneiras a seguir:

* Um codificador local ao vivo envia RTMP ou Smooth Streaming (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores dinâmicos a seguir, que produzem Smooth Streaming com múltiplas taxas de bits: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision e Tricaster. Os fluxos ingeridos passam por Canais sem qualquer transcodificação e codificação adicionais. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.
* Um fluxo de taxa de bits única (em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado)) é enviado para o Canal que está habilitado a realizar a codificação ativa com os Serviços de Mídia. O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

### <a name="channel"></a>Canal
Nos Serviços de Mídia, [Canais](https://docs.microsoft.com/rest/api/media/operations/channel)são responsáveis pelo processamento de conteúdo de transmissão ao vivo. Um Canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe transmissões ao vivo de entrada do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais StreamingEndpoints. Canais também fornecem um ponto de extremidade de visualização prévia (URL de visualização prévia) que você usa para visualizar e validar seu fluxo antes de processamento e da entrega.

Você pode obter a URL de ingestão e a URL de visualização prévia quando você cria o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando estiver pronto para começar a enviar dados de um transcodificador ao vivo para o canal, o canal deve ser iniciado. Depois que o transcodificador ao vivo inicia a ingestão de dados, você pode visualizar o fluxo.

Cada conta dos Serviços de Mídia pode conter vários canais, vários programas e vários StreamingEndpoints. Dependendo das necessidades de largura de banda e segurança, serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode executar pull de qualquer canal.

### <a name="program-event"></a>Programa (evento)
Um [Programa (evento)](https://docs.microsoft.com/rest/api/media/operations/program) permite que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Canais gerenciam Programas (eventos). A relação entre Canal e Programa é semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento regulado naquele canal.
Você pode especificar o número de horas pelo qual deseja manter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength** . Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode buscar de volta no tempo a partir da posição atual em tempo real. Programas podem ser executados sobre o período de tempo especificado, mas o conteúdo que sair do comprimento da janela será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa (evento) está associado a um ativo. Para publicar o programa, você deve criar um localizador para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

Para obter mais informações, consulte:

* [Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais](media-services-live-streaming-with-onprem-encoders.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Proteção de conteúdo
### <a name="dynamic-encryption"></a>Criptografia dinâmica
Os Serviços de Mídia do Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem distribuir o conteúdo criptografado dinamicamente com criptografia AES (usando chaves de criptografia de 128 bits) e CENC (criptografia comum) usando PlayReady e/ou Widevine DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças do PlayReady aos clientes autorizados.

Observe que, no momento, você pode criptografar os seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Não é possível criptografar downloads progressivos.

Se você desejar que os Serviços de Mídia criptografem um ativo, você precisa associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo e também configurar políticas de autorização para a chave.

Para transmitir por streaming de um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para especificar como você deseja distribuir seu ativo.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando criptografia de envelope (com AES) ou a criptografia comum (com PlayReady ou Widevine). Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

### <a name="token-restriction"></a>Restrição de token
A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta, restrição de token ou restrição de IP. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave (ou licença).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

Para obter mais informações, consulte os seguintes artigos:
- [Proteger a visão geral do conteúdo](media-services-content-protection-overview.md)
- [Proteger com AES-128](media-services-protect-with-aes128.md)
- [Proteger com PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Fornecimento
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Empacotamento dinâmico
Ao trabalhar com os Serviços de Mídia, é recomendado codificar seus arquivos de mezanino em uma conjunto MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>ponto de extremidade de streaming
Um StreamingEndpoint representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou para uma CDN (Rede de Distribuição de Conteúdo) para a distribuição (os Serviços de Mídia do Azure agora fornecem a integração com o Azure CDN). O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um Ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Os clientes de Serviços de Mídia escolhem um ponto de extremidade de streaming **Standard** ou um ou mais pontos de extremidade de streaming **Premium** de acordo com suas necessidades. O ponto de extremidade de streaming Standard é adequado para a maioria das cargas de trabalho de streaming. 

O Ponto de Extremidade de Streaming Standard é adequado para a maior parte de cargas de trabalho de streaming. Os Pontos de Extremidade de Streaming Standard proporcionam flexibilidade para distribuição de conteúdo a praticamente qualquer dispositivo por meio de empacotamento dinâmico em HLS, MPEG-DASH e Smooth Streaming, bem como a criptografia dinâmica para o Microsoft PlayReady, Google Widevine, Apple Fairplay e AES128.  Eles também são dimensionados para públicos-alvo muito pequenos para muito grandes com milhares de visualizadores simultâneos por meio da integração de CDN do Azure. Se você tiver uma carga de trabalho avançada, ou os requisitos de capacidade de streaming não se ajustarem às metas de taxa de transferência do ponto de extremidade de streaming padrão, ou se você quiser controlar a capacidade do serviço StreamingEndpoint para lidar com as crescentes necessidades de largura de banda, é recomendável alocar unidades de escala (também conhecidas como unidades de streaming Premium).

É recomendável usar empacotamento dinâmico e/ou criptografia dinâmica.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 

Para obter mais informações, consulte [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por padrão, você pode ter até dois pontos de extremidade de streaming em sua conta dos Serviços de Mídia. Para solicitar um limite superior, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).

Você será cobrado apenas quando seu StreamingEndpoint estiver em estado de execução.

### <a name="asset-delivery-policy"></a>Política de fornecimento de ativos
É a configuração de uma das etapas no fluxo de trabalho de distribuição de conteúdo dos serviços de mídia [políticas de entrega de ativos](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)que você deseja transmitir. A política de entrega de ativos informa aos serviços de mídia como você deseja que o ativo seja entregue: em que protocolo de fluxo seu ativo deve ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se você deseja criptografar dinamicamente seu ativo ou não e como (criptografia de envelope ou comum).

Se você tiver um ativo de armazenamento criptografado, antes que possa ser transmitido seu ativo, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Por exemplo, para entregar o ativo criptografado com chave de criptografia AES (criptografia avançada padrão), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a criptografia de armazenamento e transmitir o ativo claro, defina o tipo de política como NoDynamicEncryption.

### <a name="progressive-download"></a>Download progressivo
O download progressivo permite iniciar a reprodução da mídia antes do arquivo inteiro ter sido baixado. Você só pode baixar apenas progressivamente um arquivo MP4.

>[!NOTE]
>Será necessário descriptografar ativos criptografados se você desejar que eles estejam disponíveis para download progressivo.

Para fornecer aos usuários URLs de download progressivo, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo. Em seguida, é necessário acrescentar o nome do arquivo MP4. Por exemplo: 

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>URLs de streaming
Transmitindo seu conteúdo para clientes. Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir este conteúdo você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome de arquivo de manifesto (ISM). Em seguida, anexe um formato apropriado (se necessário) ao caminho do localizador.

Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS. Atualmente, o AMS não dá suporte ao SSL com domínios personalizados.  

>[!NOTE]
>Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming criados após 10 de setembro, a URL conterá "streaming.mediaservices.windows.net" (o novo formato). As URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não são compatíveis com SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.

A lista a seguir descreve os diferentes formatos de streaming e fornece exemplos:

* Smooth Streaming

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

