---
title: Usar o Azure Media Clipper no Portal | Microsoft Docs
description: Criar clipes usando o Azure Media Clipper no Portal do Azure
services: media-services
keywords: clipe; subclipe; codificação; mídia
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465808"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Criar clipes com o Azure Media Clipper no portal  

Use o Azure Media Clipper no portal para criar clipes com base em ativos em suas contas dos serviços de mídia. Para começar, navegue para sua conta dos serviços de mídia no portal. Em seguida, selecione a guia **Subclipe**.

Na guia **Subclipe**, você pode começar a compor clipes. No portal, o Clipper carrega MP4s de taxa de bits única, MP4s de múltiplas taxas de bits e arquivos mortos dinâmicos que são publicados com um localizador de streaming válido. Os ativos não publicados não são carregados.

O Clipper está atualmente em visualização pública. Para acessar o Clipper no portal do Azure, vá até esta [página de visualização pública](https://portal.azure.com/?feature.subclipper=true).

A imagem a seguir ilustra a página de aterrissagem do Clipper em sua conta de serviços de mídia: ![Azure Media Clipper no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Produzindo clipes
Para criar um clipe, arraste e solte um ativo para a interface do clipe. Se os tempos de marca forem conhecidos, insira-os manualmente na interface. Caso contrário, reproduza o ativo ou arraste o playhead para localizar o tempo de marca de entrada e de saída desejado. Se nenhum tempo de marca de entrada e de saída for fornecido, o clipe começará desde o início ou continuará até o final do ativo de entrada, respectivamente.

Para navegar com a precisão de quadro/precisão de GOP, use os botões de avanço de quadro/avanço de GOP ou recuo de quadro/recuo de GOP. Para o recorte em vários ativos, arraste e solte vários ativos na interface do clipe por meio do painel de seleção de ativo. Selecione e reordene os ativos na interface na ordem desejada. O painel de seleção de ativo fornece a duração do ativo, o tipo e os metadados de resolução de cada ativo. Ao concatenar vários ativos, considere a resolução de origem de cada arquivo de entrada. Se as resoluções de origem forem diferentes, a entrada com a resolução inferior será atualizada para atender à resolução do ativo com a resolução mais alta. Para visualizar a saída do trabalho de recorte, selecione o botão de visualização e o clipe será reproduzido a partir dos tempos de marca selecionados.

## <a name="producing-dynamic-manifest-filters"></a>Produzindo os filtros de manifesto dinâmico
Os [filtros de manifesto dinâmico](https://azure.microsoft.com/blog/dynamic-manifest/) descrevem um conjunto de regras baseado nos atributos do manifesto e na linha do tempo do ativo. Essas regras determinam como o ponto de extremidade de streaming manipula a playlist de saída (manifesto). O filtro pode ser usado para alterar quais segmentos são transmitidos para reprodução. Os filtros produzidos pelo Clipper são filtros locais e são específicos ao ativo de origem. Ao contrário de clipes renderizados, os filtros não são ativos novos e não exigem um trabalho de codificação para serem produzidos. Eles podem ser criados rapidamente por meio do [SDK do .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) ou da [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest); no entanto, eles são apenas precisos para GOP. Normalmente, os ativos codificados para streaming têm um tamanho de GOP igual a dois segundos.

Para criar um filtro dinâmico de manifesto, navegue até a guia **Ativos** e selecione o ativo desejado. Clique em **Subclipe** no menu superior. Selecione o filtro de manifesto dinâmico como o modo de recorte no menu de configurações avançadas. Siga o mesmo processo para produzir um clipe renderizado para criar o filtro. Os filtros só podem ser produzidos em relação a um único ativo.

A imagem a seguir ilustra o Clipper no modo de filtro de manifesto dinâmico no portal do Azure: ![Azure Media Clipper no modo de filtro de manifesto dinâmico no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Enviando trabalhos de recorte
Quando terminar de compor o clipe, selecione o botão de envio do trabalho para iniciar o trabalho de recorte ou a chamada de manifesto dinâmico correspondente.

## <a name="next-steps"></a>Próximas etapas
Para começar a usar o Azure Media Clipper, leia o artigo de [introdução](media-services-azure-media-clipper-getting-started.md) para obter detalhes sobre como implantar o widget.
