---
title: Otimizar a CDN do Azure para o tipo de distribuição de conteúdo
description: Otimizar a CDN do Azure para o tipo de distribuição de conteúdo
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
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: da8f17da9225da1d2b92bd8515d645bce9a1bbaa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593644"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Otimizar a CDN do Azure para o tipo de distribuição de conteúdo

Quando você distribui conteúdo para um público global grande, é essencial garantir a distribuição otimizada do conteúdo. A [CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-overview.md) pode otimizar a experiência de distribuição de acordo com o tipo de conteúdo existente. O conteúdo pode ser um site, uma transmissão ao vivo, um vídeo ou um arquivo grande para download. Quando você cria um ponto de extremidade CDN, você especifica um cenário na opção **Otimizado para**. Sua escolha determina qual otimização é aplicada ao conteúdo distribuído do ponto de extremidade CDN.

As opções de otimização foram projetadas para usar os comportamentos de melhores práticas, a fim de melhorar o desempenho de distribuição de conteúdo e obter um melhor descarregamento da origem. Suas escolhas de cenário afetam o desempenho modificando as configurações de cache parcial, o agrupamento de objeto e a política de repetição de falha da origem. 

Este artigo fornece uma visão geral dos diversos recursos de otimização e quando eles devem ser usados. Para obter mais informações sobre os recursos e as limitações, consulte os respectivos artigos em cada tipo de otimização individual.

> [!NOTE]
> Quando você cria um ponto de extremidade da CDN, as opções **Otimizado para** podem variar de acordo com o tipo de perfil em que o ponto de extremidade é criado. Os provedores de CDN do Azure aplicam a melhoria de maneiras diferentes, dependendo do cenário. 

## <a name="provider-options"></a>Opções de provedor

Os perfis da **CDN do Azure Standard da Microsoft** são compatíveis com as otimizações a seguir:

* [Entrega na Web geral](#general-web-delivery). Essa otimização também é usada para streaming de mídia e download de arquivos grandes.

> [!NOTE]
> Aceleração de site dinâmico da Microsoft é oferecida por meio [o serviço do Azure da frente](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

Os perfis da **CDN do Azure Standard da Verizon** e da **CDN do Azure Premium da Verizon** são compatíveis com as otimizações a seguir:

* [Entrega na Web geral](#general-web-delivery). Essa otimização também é usada para streaming de mídia e download de arquivos grandes.

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 


Os perfis da **CDN do Azure Standard da Akamai** são compatíveis com as otimizações a seguir:

* [Entrega na Web geral ](#general-web-delivery) 

* [Streaming de mídia geral](#general-media-streaming)

* [Streaming de mídia de vídeo sob demanda](#video-on-demand-media-streaming)

* [Download de arquivo grande](#large-file-download)

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 

A Microsoft recomenda testar as variações de desempenho entre diferentes provedores para escolher o provedor ideal para a distribuição.

## <a name="select-and-configure-optimization-types"></a>Selecionar e configurar os tipos de otimização

Quando você criar um ponto de extremidade de CDN, selecione um tipo de otimização que melhor corresponda ao cenário e ao tipo de conteúdo que você deseja que seja distribuído pelo ponto de extremidade. **Distribuição na Web geral** é a seleção padrão. Apenas para os pontos de extremidade existentes da **CDN do Azure Standard da Akamai**, é possível atualizar a opção de otimização a qualquer momento. Essa alteração não interrompe a distribuição da CDN do Azure. 

1. Em um perfil da **CDN do Azure Standard da Akamai**, selecione um ponto de extremidade.

    ![Seleção de ponto de extremidade](./media/cdn-optimization-overview/01_Akamai.png)

2. Em CONFIGURAÇÕES, selecione **Otimização**. Em seguida, selecione um tipo na lista suspensa **Otimizado para**.

    ![Otimização e seleção de tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Você pode otimizar o ponto de extremidade CDN para um desses cenários. 

### <a name="general-web-delivery"></a>Distribuição na Web geral

A distribuição na Web geral é a opção mais comum de otimização. Ela foi projetada para otimização de conteúdo da Web geral, como páginas da Web e aplicativos Web. Essa otimização também pode ser usada para downloads de arquivo e vídeo.

Um site típico contém conteúdo estático e dinâmico. O conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilo que podem ser armazenadas em cache e distribuídas para diferentes usuários. O conteúdo dinâmico é personalizado para um usuário individual, como itens de notícias adaptados a um perfil do usuário. O conteúdo dinâmico, como o conteúdo do carrinho de compras, não é armazenado em cache, pois é exclusivo para cada usuário. A distribuição na Web geral pode otimizar todo o site. 

> [!NOTE]
> Se você estiver usando o perfil da **CDN do Azure Standard da Akamai**, selecione este tipo de otimização se o tamanho médio do arquivo for menor que 10 MB. Caso contrário, se o tamanho médio do arquivo for maior que 10 MB, selecione **Download de arquivo grande** na lista suspensa **Otimizado para**.

### <a name="general-media-streaming"></a>Streaming de mídia geral

Se você precisar usar o ponto de extremidade para transmissão ao vivo e streaming de vídeo por demanda, selecione o tipo de otimização de streaming de mídia geral.

O streaming de mídia é sensível ao tempo, pois os pacotes que chegam tarde no cliente, como buffer frequente do conteúdo de vídeo, podem causar uma experiência de exibição degradada. A otimização de streaming de mídia reduz a latência da distribuição de conteúdo de mídia e fornece uma experiência de streaming suave para os usuários. 

Esse cenário é comum para clientes do serviço de mídia do Azure. Quando você usa os Serviços de Mídia do Azure, você pode obter um único ponto de extremidade de streaming, que pode ser usado para streaming sob demanda e ao vivo. Neste cenário, os clientes não precisam alternar para outro ponto de extremidade quando alteram de streaming sob demanda para ao vivo. A otimização de streaming de mídia geral dá suporte a esse tipo de cenário.

Para **a CDN Standard do Azure da Microsoft**, **a CDN Standard do Azure da Verizon**, e  **CDN Standard do Azure da Verizon**, use o tipo de otimização de entrega da web geral para entregar conteúdo de mídia de streaming geral.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming de mídia de vídeo por demanda

A otimização de streaming de mídia de vídeo por demanda melhora o conteúdo de streaming de vídeo por demanda. Se você usar um ponto de extremidade para streaming de vídeo por demanda, use essa opção.

Para os perfis da **CDN do Azure Standard da Microsoft**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, use o tipo de otimização de entrega da Web geral para entregar conteúdo de mídia de vídeo sob demanda.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto de extremidade da CDN fornecer principalmente o conteúdo de vídeo por demanda, use esse tipo de otimização. A principal diferença entre esse tipo de otimização e o tipo de otimização de streaming de mídia geral é o tempo limite de repetição de conexão. O tempo limite é muito menor para trabalhar com cenários de transmissão ao vivo.
>

### <a name="large-file-download"></a>Download de arquivos grandes

Para perfis da **CDN do Azure Standard da Akamai**, os downloads de arquivos grandes são otimizados para conteúdo de mais de 10 MB. Se o tamanho médio do arquivo for menor que 10 MB, use uma entrega de web geral. Se os tamanhos médios dos arquivos forem consistentemente maiores que 10 MB, talvez seja mais eficiente criar um ponto de extremidade separado para arquivos grandes. Por exemplo, atualizações de firmware ou software geralmente são arquivos grandes. Para entregar arquivos maiores do que 1,8 GB, a otimização de download de arquivo grande é necessária.

Para perfis da **CDN do Azure Standard da Microsoft**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, use o tipo de otimização de entrega da Web geral para entregar conteúdo de download de arquivo grande. Não há nenhuma limitação no tamanho de download do arquivo.

Para saber mais sobre a otimização de arquivos grandes, consulte [Otimização de arquivos grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmica

 A DSA (aceleração de site dinâmica) está disponível para os perfis da **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**. Essa otimização envolve um valor adicional para ser usado; para obter mais informações, consulte [Preço da Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Aceleração de site dinâmico da Microsoft é oferecida por meio [o serviço do Azure da frente](https://docs.microsoft.com/azure/frontdoor/front-door-overview) que é um objeto global [anycast](https://en.wikipedia.org/wiki/Anycast) aproveitando privada rede global da Microsoft para entregar suas cargas de trabalho do aplicativo de serviço.

A DSA inclui várias técnicas que trazem benefícios para a latência e o desempenho do conteúdo dinâmico. As técnicas incluem otimização de rota e de rede, otimização de TCP e muito mais. 

Use essa otimização para acelerar um aplicativo Web que inclui várias respostas que não são armazenáveis em cache. Entre os exemplos estão resultados da pesquisa, transações de check-out ou dados em tempo real. Continue usando as principais funcionalidades de cache da CDN do Azure para dados estáticos. 

Para obter mais informações sobre aceleração de site dinâmico, consulte [Aceleração de site dinâmico](cdn-dynamic-site-acceleration.md).



