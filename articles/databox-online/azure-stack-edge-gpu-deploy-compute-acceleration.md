---
title: Usar GPU de aceleração de computação ou VPU em dispositivos de borda Azure Stack para implantações de kubernetes | Microsoft Docs
description: Descreve como usar a GPU de aceleração de computação ou o VPU em seu Azure Stack Edge pro GPU, o Azure Stack Edge pro R ou o Azure Stack Edge mini ri para implantações do kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 3eb648af60a7be62d08f6b172347778d2358643c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440115"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Usar a aceleração de computação na Azure Stack GPU pro Edge para implantação do kubernetes

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como usar a aceleração de computação em dispositivos Azure Stack Edge ao usar implantações kubernetes. O artigo aplica-se a Azure Stack Edge pro GPU, Azure Stack Edge pro R e Azure Stack dispositivos mini R do Edge.


## <a name="about-compute-acceleration"></a>Sobre a aceleração de computação 

A CPU (unidade de processamento central) é a computação de uso geral padrão para a maioria dos processos em execução em um computador. Geralmente, um hardware de computador especializado é usado para executar algumas funções com mais eficiência do que executá-las no software em uma CPU. Por exemplo, uma GPU (unidade de processamento gráfico) pode ser usada para acelerar o processamento de dados de pixel.  

A aceleração de computação é um termo usado especificamente para dispositivos Azure Stack Edge em que uma GPU (unidade de processamento gráfico), uma VPU (unidade de processamento de visão) ou uma FPGA (matriz de porta programável) de campo é usada para aceleração de hardware. A maioria das cargas de trabalho implantadas em seu Azure Stack dispositivo de borda envolve tempo crítico, vários fluxos de câmera e/ou altas taxas de quadros, que exigem aceleração de hardware específica.

O artigo abordará a aceleração de computação somente usando GPU ou VPU para os seguintes dispositivos:

- **Azure Stack GPU pro Edge** -esses dispositivos podem ter 1 ou 2 NVIDIA T4 tensor Core GPU. Para obter mais informações, consulte [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge pro R** -esses dispositivos têm 1 GPU NVIDIA T4 tensor Core. Para obter mais informações, consulte [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack o Microsoft Edge mini R** -esses dispositivos têm 1 Intel Movidius infinidade X VPU. Para obter mais informações, consulte [Intel Movidius infinidade X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Usar GPU para implantação de kubernetes

O exemplo a seguir `yaml` pode ser usado para um Azure Stack uma GPU do Edge pro ou um dispositivo do Azure Stack Edge pro R com uma GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Usar o VPU para implantação do kubernetes

O exemplo a seguir `yaml` pode ser usado para uma Azure Stack dispositivo mini R de borda que tem um VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Próximas etapas

Saiba como [usar o kubectl para executar um aplicativo com estado kubernetes com um PersistentVolume em seu dispositivo de GPU pro do Azure Stack Edge](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
