---
title: Otimização de streaming de mídia com a CDN do Azure
description: Otimizar arquivos de mídia de streaming para a distribuição suave
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: c6ed546735058e330368151adb0df7323f943050
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593663"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Otimização de streaming de mídia com a CDN do Azure 
 
O uso de vídeo de alta definição está aumentando na internet, o que cria dificuldades para a distribuição eficiente de arquivos grandes. Os clientes esperam a reprodução suave de ativos de vídeo por demanda ou vídeo ao vivo em uma variedade de redes e clientes no mundo todo. Um mecanismo de distribuição rápido e eficiente para arquivos de streaming de mídia é crítico para garantir uma experiência do consumidor agradável e sem problemas.  

A mídia de transmissão ao vivo é especialmente difícil de ser entregue, devido a tamanhos grandes e ao número de espectadores simultâneos. Atrasos longos fazem com que os usuários saiam. Como os fluxos ao vivo não podem ser armazenados em cache antecipadamente e latências grandes não são aceitáveis para os espectadores, os fragmentos de vídeo devem ser entregues de maneira oportuna. 

Os padrões de solicitação de streaming também fornecem alguns novos desafios. Quando uma transmissão ao vivo popular ou uma nova série é liberada para vídeo por demanda, milhares a milhões de espectadores podem solicitar o fluxo ao mesmo tempo. Nesse caso, a consolidação de solicitações inteligentes é essencial para não sobrecarregar os servidores de origem quando os ativos ainda não estão em cache.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Otimizações de streaming de mídia para Azure CDN da Microsoft

Os pontos de extremidade da **CDN Standard do Azure da Microsoft** entregam ativos de mídia de streaming diretamente usando o tipo de otimização de entrega Web geral. 

A otimização de streaming de mídia para **CDN Standard do Azure da Microsoft** é eficaz para mídia de streaming de vídeo ao vivo ou de vídeo por demanda que usa fragmentos de mídia individuais para distribuição. Esse processo é diferente de um único ativo grande transferido por meio de download progressivo ou usando solicitações de intervalo de bytes. Para obter informações sobre esse estilo de distribuição de mídia, consulte [Otimização de download de arquivos grandes com a CDN do Azure](cdn-large-file-optimization.md).

Os tipos de otimização de distribuição de mídia geral ou de distribuição de mídia de vídeo por demanda usam uma CDN (Rede de Distribuição de Conteúdo) do Azure com otimizações de back-end para distribuir ativos de mídia mais rapidamente. Eles também usam configurações de ativos de mídia baseadas nas melhores práticas aprendidas ao longo do tempo.

### <a name="partial-cache-sharing"></a>Compartilhamento de cache parcial
O compartilhamento de cache parcial permite que a CDN forneça o conteúdo armazenado em cache parcialmente para novas solicitações. Por exemplo, se a primeira solicitação para a CDN resultar em uma perda no cache, a solicitação será enviada para a origem. Embora esse conteúdo incompleto seja carregado no cache da CDN, outras solicitações para a CDN podem começar a obter esses dados. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Otimizações de streaming de mídia para Azure CDN da Verizon

Os pontos de extremidade da **CDN Standard do Azure da Verizon** e **CDN Premium do Azure da Verizon** entregam ativos de mídia de streaming diretamente usando o tipo de otimização de entrega Web geral. Alguns recursos na CDN ajudam diretamente na entrega de ativos de mídia por padrão.

### <a name="partial-cache-sharing"></a>Compartilhamento de cache parcial

O compartilhamento de cache parcial permite que a CDN forneça o conteúdo armazenado em cache parcialmente para novas solicitações. Por exemplo, se a primeira solicitação para a CDN resultar em uma perda no cache, a solicitação será enviada para a origem. Embora esse conteúdo incompleto seja carregado no cache da CDN, outras solicitações para a CDN podem começar a obter esses dados. 

### <a name="cache-fill-wait-time"></a>Tempo de espera de preenchimento do cache

 O recurso de tempo de espera do preenchimento do cache força o servidor de borda a reter todas as solicitações posteriores para o mesmo recurso até os cabeçalhos de resposta HTTP chegarem do servidor de origem. Se os cabeçalhos de resposta HTTP da origem chegarem antes da expiação do temporizador, todas as solicitações que foram colocadas em espera serão atendidas fora do cache em crescimento. Ao mesmo tempo, o cache é preenchido por dados da origem. Por padrão, o tempo de espera de preenchimento de cache é definido como 3.000 milissegundos. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Otimizações de streaming de mídia para Azure CDN do Akamai
 
**A CDN Standard do Azure da Akamai** oferece um recurso que oferece ativos de mídia de streaming de forma eficiente para usuários em todo o mundo em escala. O recurso reduz as latências porque reduz a carga nos servidores de origem. Esse recurso está disponível no tipo de preço standard da Akamai. 

A otimização de streaming de mídia para **CDN Standard do Azure da Akamai** é eficaz para mídia de streaming de vídeo ao vivo ou de vídeo por demanda que usa fragmentos de mídia individuais para distribuição. Esse processo é diferente de um único ativo grande transferido por meio de download progressivo ou usando solicitações de intervalo de bytes. Para obter informações sobre esse estilo de distribuição de mídia, consulte [Otimização de arquivos grandes](cdn-large-file-optimization.md).

Os tipos de otimização de distribuição de mídia geral ou de distribuição de mídia de vídeo por demanda usam uma CDN com otimizações de back-end para distribuir ativos de mídia mais rapidamente. Eles também usam configurações de ativos de mídia baseadas nas melhores práticas aprendidas ao longo do tempo.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Configure um ponto de extremidade da CDN da Akamai para otimizar streaming de mídia
 
Configure o ponto de extremidade CDN (rede de distribuição de conteúdo) para otimizar a distribuição de arquivos grandes pelo portal do Azure. Você também pode usar as APIs REST ou qualquer um dos SDKs cliente para fazer isso. As seguintes etapas mostram o processo através do Portal do Azure para um perfil **CDN Standard do Azure da Akamai**:

1. Para adicionar um novo ponto de extremidade, na página **Perfil CDN** da Akamai, selecione **Ponto de extremidade**.
  
    ![Novo ponto de extremidade](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Na lista suspensa **Otimizado para**, selecione **Streaming de mídia de vídeo por demanda** para ativos de vídeo por demanda. Se você fizer uma combinação do streaming de vídeo ao vivo e de vídeo por demanda, selecione **Streaming de mídia geral**.

    ![Streaming selecionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Depois de criar o ponto de extremidade, ele aplica a otimização a todos os arquivos que correspondem a determinados critérios. A seção a seguir descreve esse processo. 

### <a name="caching"></a>Cache

Se a **CDN Standard do Azure da Akamai** detectar que o ativo é um manifesto ou fragmento de streaming, ela usará diferentes tempos de expiração de cache da entrega Web geral. (Consulte a lista completa na tabela a seguir.) Como sempre, o controle de cache ou os cabeçalhos Expires enviados da origem são respeitados. Se o ativo não for um ativo de mídia, ela armazenará em cache usando os tempos de expiração da distribuição na Web geral.

O tempo de cache negativo curto é útil para descarregamento de origem quando muitos usuários solicitam um fragmento que ainda não existe. Um exemplo é um transmissão ao vivo em que os pacotes não estão disponíveis na origem nesse segundo. O intervalo de cache mais longo também ajuda a descarregar as solicitações da origem, já que o conteúdo de vídeo normalmente não é modificado.
 

|   | Distribuição na Web geral | Streaming de mídia geral | Streaming de mídia de vídeo por demanda  
--- | --- | --- | ---
Caching: Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |365 dias | 365 dias   
Caching: Negativo <br> HTTP 204, 305, 404, <br> e 405 | Nenhum | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Lidar com falhas de origem  

A entrega de mídia geral e entrega de mídia de vídeo por demanda também têm tempos limite de origem e um log de repetição baseado nas melhores práticas para padrões de solicitação típicos. Por exemplo, como a entrega de mídia geral é direcionada para a entrega de mídia de vídeo ao vivo e por demanda, ela usa um tempo limite de conexão menor devido à natureza sensível ao tempo da transmissão ao vivo.

Quando uma conexão atinge o tempo limite, a CDN repete a operação várias vezes antes de enviar um erro “504 – Tempo Limite do Gateway” para o cliente. 

Quando um arquivo corresponde ao tipo de arquivo e à lista de condições de tamanho, a CDN usa o comportamento de streaming de mídia. Caso contrário, ela usa a distribuição na Web geral.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condições para otimização de streaming de mídia 

A seguinte tabela lista o conjunto de critérios a serem atendidos para a otimização de streaming de mídia: 
 
Tipos de streaming com suporte | Extensões de arquivo  
--- | ---  
HLS da Apple | m3u8, m3u, m3ub, key, ts, aac
HDS da Adobe | f4m, f4x, drmmeta, bootstrap, f4f,<br>Estrutura de URL Seg-Frag <br> (expressão regular correspondente: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Streaming suave | /manifest/, /QualityLevels/Fragments/
  
 
