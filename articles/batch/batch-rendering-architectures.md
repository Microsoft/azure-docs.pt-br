---
title: Arquiteturas de referência de renderização do Azure - Lote do Azure
description: Arquiteturas para usar o Lote do Microsoft Azure e outros serviços do Azure para estender um farm de renderização local disparando na nuvem
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: fa2d59b2a6d2dea72276ab38a5cb1ca7bfb579a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323118"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquiteturas de referência para renderização do Azure

Este artigo mostra diagramas de arquitetura de alto nível para cenários para estender ou "disparar" um farm de renderização local para o Azure. Os exemplos mostram opções diferentes para serviços de armazenamento, rede e computação do Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido com NFS ou CFS

O diagrama a seguir mostra um cenário híbrido que inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - local: Azure ExpressRoute ou VPN. Azure: VNet do Azure.

* **Armazenamento** - Arquivos de entrada e saída: NFS ou CFS usando VMs do Azure, sincronizados com armazenamento local por meio da Sincronização de Arquivos do Azure e RSync. Como alternativa: Avere vFXT para os arquivos entrada ou saída a partir dos dispositivos NAS locais usando NFS.

  ![Intermitência de nuvem - Híbrido com NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido com Blobfuse

O diagrama a seguir mostra um cenário híbrido que inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - local: Azure ExpressRoute ou VPN. Azure: VNet do Azure.

* **Armazenamento** - Arquivos de entrada e saída: Armazenamento de Blobs, montado para computar recursos por meio do Azure Blobfuse.

  ![Intermitência de nuvem - Híbrido com Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Computação híbrida e armazenamento

O diagrama a seguir mostra um cenário híbrido totalmente conectado para computação e armazenamento e inclui os serviços do Azure a seguir:

* **Computação** - Pool do Lote do Azure ou Conjunto de Dimensionamento de Máquinas Virtuais.

* **Rede** - local: Azure ExpressRoute ou VPN. Azure: VNet do Azure.

* **Armazenamento** - Entre instalações: Avere vFXT. Arquivamento opcional dos arquivos locais via Azure Data Box para o armazenamento de Blobs ou Avere FXT local para a aceleração do NAS.

  ![Intermitência de nuvem - Computação híbrida e armazenamento](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como usar [Gerenciadores de renderização](batch-rendering-render-managers.md) com o Lote do Azure.

* Saiba mais sobre as opções para [Renderizar no Azure](batch-rendering-service.md).