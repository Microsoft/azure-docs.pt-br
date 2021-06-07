---
title: Compartilhamento de GPU no dispositivo Azure Stack GPU pro Edge
description: Descreve as abordagens para compartilhar GPUs no dispositivo Azure Stack GPU pro Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: ff1c7b79a49b0b659056c89af3c61f28b72ebc50
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645244"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Compartilhamento de GPU em seu dispositivo de GPU pro do Azure Stack Edge

A GPU (unidade de processamento gráfico) é um processador especializado projetado para acelerar a renderização de gráficos. As GPUs podem processar várias partes de dados simultaneamente, tornando-as úteis para aprendizado de máquina, edição de vídeo e aplicativos de jogos. Além da CPU para computação de uso geral, seus dispositivos de GPU pro Azure Stack Edge podem conter uma ou duas GPUs NVIDIA Tesla T4 para cargas de trabalho de computação intensiva, como inferência acelerado por hardware. Para obter mais informações, consulte [GPU do Tesla T4 da NVIDIA](https://www.nvidia.com/en-us/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>Sobre o compartilhamento de GPU

Muitos aprendizado de máquina ou outras cargas de trabalho de computação podem não precisar de uma GPU dedicada. As GPUs podem ser compartilhadas e compartilhar GPUs entre cargas de trabalho em contêiner ou VM ajudam a aumentar a utilização de GPU sem afetar significativamente os benefícios de desempenho da GPU.  

## <a name="using-gpu-with-vms"></a>Usando GPU com VMs

Em seu dispositivo Azure Stack Edge pro, uma GPU não pode ser compartilhada ao implantar cargas de trabalho de VM. Uma GPU só pode ser mapeada para uma VM. Isso implica que você só pode ter uma VM GPU em um dispositivo com uma GPU e duas VMs em um dispositivo equipado com duas GPUs. Há outros fatores que também devem ser considerados ao usar VMs GPU em um dispositivo que tenha o kubernetes configurado para cargas de trabalho em contêineres. Para obter mais informações, consulte [VMs de GPU e kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Usando GPU com contêineres

Se você estiver implantando cargas de trabalho em contêineres, uma GPU poderá ser compartilhada de mais de uma maneira na camada de hardware e software. Com a GPU Tesla T4 em seu dispositivo Azure Stack Edge pro, estamos limitados ao compartilhamento de software. Em seu dispositivo, as duas abordagens a seguir para o compartilhamento de software de GPU são usadas: 

- A primeira abordagem envolve o uso de variáveis de ambiente para especificar o número de GPUs que podem ser compartilhadas em tempo. Considere as seguintes advertências ao usar essa abordagem:

    - Você pode especificar uma ou ambas ou nenhuma GPUs com este método. Não é possível especificar o uso fracionário.
    - Vários módulos podem ser mapeados para uma GPU, mas o mesmo módulo não pode ser mapeado para mais de uma GPU.
    - Com a saída do SMI Nvidia, você pode ver a utilização geral da GPU, incluindo a utilização de memória.
    
    Para obter mais informações, consulte como [implantar um módulo IOT Edge que usa GPU](azure-stack-edge-gpu-configure-gpu-modules.md) em seu dispositivo.

- A segunda abordagem exige que você habilite o serviço de vários processos em suas GPUs NVIDIA. MPS é um serviço de tempo de execução que permite que vários processos usando o CUDA sejam executados simultaneamente em uma única GPU compartilhada. Os MPS permitem a sobreposição de operações de kernel e memcopy de diferentes processos na GPU para atingir o máximo de utilização. Para obter mais informações, consulte [serviço de vários processos](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Considere as seguintes advertências ao usar essa abordagem:
    
    - Os MPS permitem que você especifique mais sinalizadores na implantação de GPU.
    - Você pode especificar o uso fracionário por meio de MPS, limitando assim o uso de cada aplicativo implantado no dispositivo. Você pode especificar a porcentagem de GPU a ser usada para cada aplicativo na `env` seção do `deployment.yaml` adicionando o seguinte parâmetro: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Utilização de GPU
 
Quando você compartilha a GPU em cargas de trabalho em contêineres implantadas em seu dispositivo, pode usar a interface de gerenciamento do sistema NVIDIA (NVIDIA-SMI). NVIDIA-SMI é um utilitário de linha de comando que ajuda você a gerenciar e monitorar dispositivos NVIDIA GPU. Para obter mais informações, consulte [interface de gerenciamento do sistema NVIDIA](https://developer.nvidia.com/nvidia-system-management-interface).

Para exibir o uso da GPU, primeiro conecte-se à interface do PowerShell do dispositivo. Execute o `Get-HcsNvidiaSmi` comando e exiba a saída do SMI NVIDIA. Você também pode exibir como a utilização de GPU é alterada habilitando MPS e, em seguida, implantando várias cargas de trabalho no dispositivo. Para obter mais informações, consulte [habilitar serviço de vários processos](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Próximas etapas

- [Compartilhamento de GPU para implantações kubernetes em seu Azure Stack Edge pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Compartilhamento de GPU para implantações de IOT em seu Azure Stack Edge pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
