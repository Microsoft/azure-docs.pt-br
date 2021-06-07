---
title: Visão geral do Azure HPC Cache
description: Descreve o Azure HPC Cache, uma solução de acelerador de acesso a arquivos para computação de alto desempenho
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2085efc5f38b2252e40f4aeb1ebfa16bf38147b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256133"
---
# <a name="what-is-azure-hpc-cache"></a>O que é o Azure HPC Cache?

O Azure HPC Cache acelera o acesso a suas tarefas de HPC (computação de alto desempenho). Ao armazenar arquivos em cache no Azure, o Azure HPC Cache traz a escalabilidade da computação em nuvem para seu fluxo de trabalho existente. Esse serviço pode ser usado até mesmo para fluxos de trabalho em que seus dados são armazenados em links WAN, como no ambiente de NAS do datacenter local.

O Azure HPC Cache é fácil de iniciar e monitorar no portal do Azure. O armazenamento de NFS existente ou novos contêineres de blobs podem se tornar parte de seu namespace agregado, o que simplifica o acesso do cliente mesmo quando você altera o destino de armazenamento de back-end.

## <a name="overview-video"></a>Vídeo de visão geral

[![miniatura de vídeo: Visão geral do Azure HPC Cache – clique para visitar a página do vídeo](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Clique na imagem acima para assistir a uma [breve visão geral do Azure HPC Cache](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Casos de uso

O Azure HPC Cache é mais adequado para aumento de produtividade em fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a arquivos com leitura intensa
* Dados armazenados em um armazenamento acessível por NFS, Blob do Azure ou ambos
* Farms de computação com até 75.000 núcleos de CPU

O Azure HPC Cache pode ser adicionado a uma ampla variedade de fluxos de trabalho em diversos setores. Qualquer sistema em que um grande número de máquinas precisa acessar um conjunto de arquivos em escala e com baixa latência se beneficiará desse serviço. As seções a seguir fornecem exemplos específicos.

### <a name="visual-effects-vfx-rendering"></a>Renderização VFX (de efeitos visuais)

Em mídia e entretenimento, o Azure HPC Cache pode acelerar o acesso a dados em projetos de renderização com limitações de tempo. Fluxos de trabalho de renderização VFX geralmente exigem processamento de último minuto por um grande número de nós de computação. Os dados desses fluxos de trabalho normalmente estão localizados em um ambiente NAS local. O Azure HPC Cache pode armazenar em cache esses dados de arquivo na nuvem para reduzir a latência e aumentar a flexibilidade para renderização sob demanda.

### <a name="life-sciences"></a>Ciências da vida

Muitos fluxos de trabalho de ciências biológicas podem se beneficiar do cache de arquivos de expansão.

Um instituto de pesquisa que deseja portar seus fluxos de trabalho de análise de genoma para o Azure pode deslocá-los facilmente usando o Azure HPC Cache. Como o cache oferece acesso de arquivo POSIX, nenhuma alteração do lado do cliente é necessária para executar seu fluxo de trabalho de cliente existente na nuvem.

O Azure HPC Cache também pode ser aproveitado para aumentar a eficiência em tarefas como análise secundária, simulação farmacológica ou análise de imagens orientada por IA.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Uma implantação do Azure HPC Cache pode ajudar a acelerar cálculos de análise quantitativa, cargas de trabalho de análise de risco e simulações Monte Carlo para fornecer a empresas de serviços financeiros melhores insights para tomar decisões estratégicas.

## <a name="region-availability"></a>Disponibilidade de região

Acesse a página [Produtos de infraestrutura global do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) para saber onde o Azure HPC Cache está disponível.

O Azure HPC Cache reside em apenas uma região. Ele poderá acessar dados armazenados em outras regiões se você conectá-los a contêineres de blob localizados lá. O cache não armazena dados do cliente permanentemente.

## <a name="next-steps"></a>Próximas etapas

* Leia a [página do produto do Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para saber mais sobre seus recursos
* Saiba mais sobre os [pré-requisitos](hpc-cache-prerequisites.md) do produto
* [Crie um Azure HPC Cache](hpc-cache-create.md) no portal do Azure
