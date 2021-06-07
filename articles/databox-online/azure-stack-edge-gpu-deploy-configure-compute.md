---
title: Tutorial para filtrar e analisar dados com computação em uma GPU do Azure Stack Edge Pro | Microsoft Docs
description: Saiba como configurar a função de computação em uma GPU do Azure Stack Edge Pro e usá-la para transformar dados antes de enviá-los para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 626c4b192eeeeb8a07130d0a9ecf6754eebbe068
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065677"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Tutorial: Configurar a computação no dispositivo de GPU Azure Stack Edge Pro

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como configurar uma função de computação e criar um cluster do Kubernetes no dispositivo Azure Stack Edge Pro. 

Esse procedimento pode levar cerca de 20 a 30 minutos para ser concluído.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Obter pontos de extremidade do Kubernetes

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo Azure Stack Edge Pro, verifique o seguinte:

- Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Lembre-se de seguir as instruções em [Habilitar a rede de computação](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) e:
    - Habilitar um adaptador de rede para computação.
    - Atribuir IPS de nó do Kubernetes e IPs de serviço externos do Kubernetes.

## <a name="configure-compute"></a>Configurar a computação

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Obter pontos de extremidade do Kubernetes

Para configurar um cliente para acessar o cluster do Kubernetes, você precisará do ponto de extremidade do Kubernetes. Siga estas etapas para obter o ponto de extremidade da API do Kubernetes da IU local do dispositivo Azure Stack Edge Pro.

1. Na IU da Web local do dispositivo, acesse a página **Dispositivos**.
2. Nos **Pontos de extremidade do dispositivo**, copie o ponto de extremidade do **Serviço de API do Kubernetes**. Esse ponto de extremidade é uma cadeia de caracteres no seguinte formato: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Página de dispositivo na IU local](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Salve a cadeia de caracteres do ponto de extremidade. Você usará essa cadeia de caracteres de ponto de extremidade após configurar um cliente para acesso ao cluster do Kubernetes por meio do kubectl.

4. Enquanto você estiver na IU da Web local, poderá:

    - Acessar a API do Kubernetes, selecionar **configurações avançadas** e baixar um arquivo de configuração avançada para o Kubernetes. 

        ![Página de dispositivo na IU local 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Se você tiver recebido uma chave da Microsoft (usuários selecionados podem obtê-la), será possível usar esse arquivo de configuração.

        ![Página de dispositivo na IU local 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Você também pode acessar o ponto de extremidade do **painel do Kubernetes** e baixar um arquivo de configuração `aseuser`. 
    
        ![Página de dispositivo na IU local 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Você pode usar esse arquivo de configuração para entrar no painel do Kubernetes ou depurar problemas no cluster do Kubernetes. Para obter mais informações, confira [Acessar o painel do Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a computação
> * Obter pontos de extremidade do Kubernetes


Para saber como administrar seu dispositivo Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Usar IU da Web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
