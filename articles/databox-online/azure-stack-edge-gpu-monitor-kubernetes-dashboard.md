---
title: Monitorar seu dispositivo pro Edge Azure Stack por meio do painel do kubernetes | Microsoft Docs
description: Descreve como acessar e usar o painel do kubernetes para monitorar seu dispositivo pro Edge Azure Stack.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443039"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Use o painel do kubernetes para monitorar seu dispositivo de GPU pro do Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como acessar e usar o painel do kubernetes para monitorar seu dispositivo de GPU pro do Azure Stack Edge. Para monitorar seu dispositivo, você pode usar gráficos no portal do Azure, exibir o painel do kubernetes ou executar `kubectl` comandos por meio da interface do PowerShell do dispositivo. 

Este artigo se concentra apenas nas tarefas de monitoramento que podem ser executadas no painel do kubernetes.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Acessar o painel do kubernetes em seu dispositivo
> * Exibir módulos implantados em seu dispositivo
> * Obter endereço IP para aplicativos implantados em seu dispositivo
> * Exibir logs de contêiner para módulos implantados em seu dispositivo


## <a name="about-kubernetes-dashboard"></a>Sobre o painel do kubernetes

O painel do Kubernetes é uma interface do usuário baseada na Web que pode ser usado para solucionar problemas dos aplicativos em contêineres. O painel do kubernetes é uma alternativa baseada na interface do usuário para a `kubectl` linha de comando kubernetes. Para obter mais informações, confira [Painel do Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

No dispositivo Azure Stack Edge pro, você pode usar o painel do kubernetes no modo *somente leitura* para obter uma visão geral dos aplicativos em execução no dispositivo pro Azure Stack Edge, exibir o status dos recursos de cluster do kubernetes e ver os erros que ocorreram no dispositivo.

## <a name="access-dashboard"></a>Painel do Access

O painel do kubernetes é *somente leitura* e é executado no nó mestre kubernetes na porta 31000. Siga estas etapas para acessar o painel: 

1. Na interface do usuário local do seu dispositivo, vá para **dispositivo** e vá para **pontos de extremidade do dispositivo**. 
1. Selecione **baixar configuração** para baixar um `kubeconfig` que permite que você acesse o painel. Salve o `config.json` arquivo no sistema local.
1. Selecione a URL do painel do kubernetes para abrir o painel em um navegador.

    ![URL do painel do kubernetes na página do dispositivo na interface do usuário local](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Na página de **entrada do painel do kubernetes** :
    
    1. Selecione **kubeconfig**. 
        ![Selecione a opção kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Selecione as reticências **...**. Procure e aponte para o `kubeconfig` que você baixou anteriormente no sistema local. Selecione **Entrar**.
        ![Navegue até o arquivo kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Agora você pode exibir o painel do kubernetes para seu dispositivo pro Edge Azure Stack no modo somente leitura.

    ![Página principal do painel kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Exibir status do módulo

Os módulos de computação são contêineres que têm uma lógica de negócios implementada. Você pode usar o painel para verificar se um módulo de computação foi implantado com êxito em seu dispositivo Azure Stack Edge pro.

Para exibir o status do módulo, siga estas etapas no painel:

1. No painel esquerdo do painel, vá para **namespace**. Filtre pelo namespace onde IoT Edge módulos são exibidos, nesse caso, **iotedge**.
1. No painel esquerdo, vá para cargas de **trabalho > implantações**.
1. No painel direito, você verá todos os módulos implantados em seu dispositivo. Nesse caso, um módulo GettingStartedWithGPU foi implantado no Azure Stack Edge pro. Você pode ver que o módulo foi implantado.

    ![Exibir implantação de módulo](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Obter endereço IP para serviços ou módulos

Você pode usar o painel para obter os endereços IP dos serviços ou módulos que você deseja expor fora do cluster kubernetes. 

Você atribui o intervalo de IP para esses serviços externos por meio da interface do usuário da Web local do dispositivo na página de **configurações de rede de computação** . Depois de implantar os módulos do IoT Edge, talvez você queira obter o endereço IP atribuído a um módulo ou serviço específico. 

Para obter o endereço IP, siga estas etapas no painel:

1. No painel esquerdo do painel, vá para **namespace**. Filtre pelo namespace em que um serviço externo é implantado, nesse caso, **iotedge**.
1. No painel esquerdo, vá para **descoberta e balanceamento de carga > serviços**.
1. No painel direito, você verá todos os serviços que estão em execução no `iotedge` namespace em seu dispositivo Azure Stack Edge pro.

    ![Obter o IP para serviços externos](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Exibir logs do contêiner

Há instâncias em que você precisa exibir os logs de contêiner. Você pode usar o painel para obter logs de um contêiner específico que você implantou em seu cluster kubernetes.

Para exibir os logs de contêiner, siga estas etapas no painel:

1. No painel esquerdo do painel, vá para **namespace**. Filtre pelo namespace em que os módulos de IoT Edge são implantados, nesse caso, **iotedge**.
1. No painel esquerdo, vá para cargas de **trabalho > pods**.
1. No painel direito, você verá todos os pods em execução no seu dispositivo. Identifique o Pod que está executando o módulo para o qual você deseja exibir os logs. Selecione as reticências verticais do pod que você identificou e, no menu de contexto, selecione **logs**.

    ![Exibir logs de contêiner 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Os logs são exibidos em um visualizador de logs que é incorporado ao painel. Você também pode baixar os logs.

    ![Exibir logs de contêiner 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Exibir CPU, uso de memória

O painel do kubernetes para o dispositivo Azure Stack Edge pro também tem um [complemento do servidor de métricas](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) que agrega o uso de CPU e de memória entre os recursos do kubernetes.
 
Por exemplo, você pode exibir a CPU e a memória consumidas entre implantações em todos os namespaces. 

![Exibir o uso de CPU e memória em todas as implantações](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Você também pode filtrar por um namespace específico. No exemplo a seguir, você pode exibir o consumo de CPU e memória somente para implantações de arco do Azure.  

![Exibir o uso de memória e CPU para implantações do Azure Arc](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

O servidor de métricas do kubernetes fornece pipelines de dimensionamento automático, como o [dimensionador automático de Pod horizontal](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Próximas etapas

Saiba como [solucionar problemas de dispositivo](azure-stack-edge-gpu-troubleshoot.md).
