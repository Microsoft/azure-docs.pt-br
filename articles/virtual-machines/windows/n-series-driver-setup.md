---
title: Configuração do driver NVIDIA GPU da série N do Azure para Windows
description: Como configurar drivers de GPU NVIDIA para VMs da série N executando Windows Server ou Windows no Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557430"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalar drivers de GPU NVIDIA em VMs da série N que executam o Windows 

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que contam com GPUs da NVIDIA, é preciso instalar os drivers para GPU NVIDIA. A [Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) para saber quais são os sistemas operacionais compatíveis e as etapas de implantação.

Se você optar por instalar os drivers NVIDIA GPU manualmente, este artigo fornecerá os sistemas operacionais, drivers e etapas de instalação e verificação com suporte. As informações de instalação manual de driver também estão disponíveis para [VMs do Linux](../linux/n-series-driver-setup.md).

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se pela Área de Trabalho Remota a cada VM da série N.

2. Baixe, extraia e instale o driver com suporte para o sistema operacional Windows.

Uma reinicialização é necessária após a instalação do driver de GRID em uma VM. Uma reinicialização não é necessária após a instalação do driver de CUDA.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

Observe que o painel de controle NVIDIA só está acessível com a instalação do driver de grade. Se você tiver instalado drivers CUDA, o painel de controle nvidia não estará visível.

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra uma configuração bem-sucedida da placa Tesla K80 em uma VM NC do Azure.

![Propriedades do driver GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver.

1. Abra um prompt de comando e mude para o diretório **C:\Program Files\NVIDIA Corporation\NVSMI**.

2. Execute `nvidia-smi`. Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. O **GPU-Util** mostra **0%**, a menos que você esteja atualmente executando uma carga de trabalho de GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser ativada em VMs da série N com capacidade de RDMA, como o NC24r implementado no mesmo conjunto de disponibilidade ou em um único grupo de posicionamento em um conjunto de dimensionamento de máquina virtual. A extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. Para adicionar a extensão de VM a uma VM da série N habilitada para RDMA, use cmdlets do [Azure PowerShell](/powershell/azure/) para o Azure Resource Manager.

Para instalar a última extensão HpcVMDrivers versão 1.1 em uma VM compatível com RDMA existente chamada myVM na região Oeste dos EUA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [Recursos e extensões da máquina virtual para Windows](../extensions/features-windows.md).

A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com [Microsoft MPI](/message-passing-interface/microsoft-mpi) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Próximas etapas

* Desenvolvedores compilando aplicativos acelerados por GPU para GPUs NVIDIA Tesla também podem baixar e instalar o [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Para obter mais informações, consulte o [Guia de instalação do CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
