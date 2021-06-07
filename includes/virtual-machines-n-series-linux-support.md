---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4bfac9be5041fdf4ebfe7ea56f064b8b85806703
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98860054"
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

### <a name="nvidia-cuda-drivers"></a>Drivers NVIDIA CUDA

Os drivers NVIDIA CUDA para VMs das séries NC, NCv2, NCv3, ND e NDv2 (opcional para a série NV) são suportados apenas nas distribuições Linux listadas na tabela a seguir. As informações sobre o driver CUDA são atuais no momento da publicação. Para obter os drivers CUDA e os sistemas operacionais com suporte mais recentes, visite o site do [NVIDIA](https://developer.nvidia.com/cuda-zone) . Certifique-se de instalar ou atualizar para os drivers mais recentes do CUDA para a sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Linux, você pode implantar uma imagem de [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Ubuntu 16.04 LTS ou CentOS 7.4 vem com drivers NVIDIA CUDA pré-instalados, a Biblioteca de Rede Neural Avançada CUDA Neural e outras ferramentas.


### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver de grade NVIDIA para VMs de série NVv3 e NV usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade em VMs do Azure NV, apenas em sistemas operacionais listados na tabela a seguir. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure. Você não precisa configurar um servidor de licença de software de vGPU NVIDIA.

Os drivers de grade redistribuídos pelo Azure não funcionam em VMs da série não NV, como as VMs NC, NCv2, NCv3, ND e série NDv2.

|Distribuição|Driver|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,7 a 7,9, 8,0, 8,1<br/><br/>SUSE Linux Enterprise Server 12 SP2 <br/><br/>SUSE Linux Enterprise Server 15 SP2 | NVIDIA GRID 12,0, ramificação do driver [R460](https://go.microsoft.com/fwlink/?linkid=874272)(. exe)|

Visite o [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) para obter a lista completa de todos os links de driver de grade NVIDIA anteriores.

> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>
