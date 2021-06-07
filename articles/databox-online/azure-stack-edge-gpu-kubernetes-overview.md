---
title: Visão geral do cluster kubernetes no dispositivo pro Edge Microsoft Azure Stack | Microsoft Docs
description: Descreve como o kubernetes é implementado em seu dispositivo Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 72ba07090e6ce67501761d97876aa136f146d61c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437920"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes em seu dispositivo de GPU pro do Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

O kubernetes é uma plataforma de software livre popular para orquestrar aplicativos em contêineres. Este artigo fornece uma visão geral do kubernetes e, em seguida, descreve como o kubernetes funciona em seu dispositivo Azure Stack Edge pro. 

## <a name="about-kubernetes"></a>Sobre o kubernetes 

O kubernetes fornece uma plataforma fácil e confiável para gerenciar aplicativos baseados em contêiner e seus componentes de rede e armazenamento associados. Você pode criar, entregar e dimensionar rapidamente aplicativos em contêineres com o kubernetes.

Como uma plataforma aberta, você pode usar o kubernetes para criar aplicativos com sua linguagem de programação preferida, bibliotecas de sistema operacional ou barramento de sistema de mensagens. Para agendar e implantar versões, o kubernetes pode se integrar com as ferramentas de integração contínua e fornecimento contínuo existentes.

Para obter mais informações, consulte [como o kubernetes funciona](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes no Azure Stack Edge pro

Em seu dispositivo Azure Stack Edge pro, você pode criar um cluster kubernetes Configurando a computação. Quando a função de computação é configurada, o cluster kubernetes incluindo os nós mestre e de trabalho são todos implantados e configurados para você. Esse cluster é usado para implantação de carga de trabalho via `kubectl` , IOT Edge ou arco do Azure.

O dispositivo pro Edge Azure Stack está disponível como uma configuração de 1 nó que constitui o cluster de infraestrutura. O cluster kubernetes é separado do cluster de infraestrutura e é implantado na parte superior do cluster de infraestrutura. O cluster de infraestrutura fornece o armazenamento persistente para seu dispositivo Azure Stack Edge pro enquanto o cluster kubernetes é responsável exclusivamente pela orquestração de aplicativos. 

O cluster kubernetes, nesse caso, tem um nó mestre e um nó de trabalho. Os nós kubernetes em um cluster são máquinas virtuais que executam seus aplicativos e fluxos de trabalho de nuvem. 

O nó mestre kubernetes é responsável por manter o estado desejado para o cluster. O nó mestre também controla o nó de trabalho que, por sua vez, executa os aplicativos em contêineres. 

O diagrama a seguir ilustra a implementação do kubernetes em um dispositivo Azure Stack Edge pro de 1 nó. O dispositivo de 1 nó não está altamente disponível e, se o único nó falhar, o dispositivo ficará inoperante. O cluster kubernetes também fica inativo.

![Arquitetura kubernetes para um dispositivo pro Edge Azure Stack de 1 nó](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Para obter mais informações sobre a arquitetura de cluster kubernetes, acesse [conceitos principais do kubernetes](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Provisionamento de volume de armazenamento

Para dar suporte a cargas de trabalho de aplicativo, você pode montar volumes de armazenamento para dados persistentes em seus compartilhamentos de dispositivo do Azure Stack Edge pro. Tanto volumes estáticos quanto dinâmicos podem ser usados. 

Para obter mais informações, consulte Opções de provisionamento de armazenamento para aplicativos no [armazenamento kubernetes para seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Rede

A rede kubernetes permite que você configure a comunicação em sua rede kubernetes, incluindo rede de contêiner para contêiner, rede pod-para-Pod, rede de pod para serviço e rede de Internet para serviço. Para obter mais informações, consulte o modelo de rede em [rede kubernetes para seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Atualizações

À medida que novas versões do kubernetes se tornam disponíveis, o cluster pode ser atualizado usando as atualizações padrão disponíveis para seu dispositivo Azure Stack Edge pro. Para obter as etapas sobre como atualizar, consulte [aplicar atualizações para seu Azure Stack Edge pro](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Acesso, monitoramento

O cluster kubernetes no dispositivo Azure Stack Edge Pro permite o controle de acesso baseado em função do kubernetes (kubernetes RBAC). Para obter mais informações, consulte [controle de acesso baseado em função kubernetes em seu dispositivo de GPU Pro Azure Stack Edge](azure-stack-edge-gpu-kubernetes-rbac.md).

Você também pode monitorar a integridade do seu cluster e recursos por meio do painel do kubernetes. Os logs de contêiner também estão disponíveis. Para obter mais informações, consulte [usar o painel do kubernetes para monitorar a integridade do cluster do kubernetes em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor também está disponível como um complemento para coletar dados de integridade de contêineres, nós e controladores. Para obter mais informações, consulte [visão geral de Azure monitor](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Gerenciamento de aplicativos

Depois que um cluster kubernetes é criado em seu dispositivo Azure Stack Edge pro, você pode gerenciar os aplicativos implantados nesse cluster por meio de qualquer um dos seguintes métodos:

- Acesso nativo via `kubectl`
- IoT Edge 
- Azure Arc

Esses métodos são explicados nas seções a seguir.


### <a name="kubernetes-and-kubectl"></a>Kubernetes e kubectl

Depois que o cluster kubernetes for implantado, você poderá gerenciar os aplicativos implantados no cluster localmente de um computador cliente. Você usa uma ferramenta nativa, como *kubectl* , por meio da linha de comando para interagir com os aplicativos. 

Para obter mais informações sobre como implantar o cluster kubernetes, vá para [implantar um cluster kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Para obter informações sobre o gerenciamento, acesse [usar kubectl para gerenciar o cluster kubernetes em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes e IoT Edge

O kubernetes também pode ser integrado a cargas de trabalho de IoT Edge no dispositivo Azure Stack Edge pro, em que o kubernetes fornece escala e o ecossistema e o IoT fornecem o ecossistema centrado em IoT. A camada kubernetes é usada como uma camada de infraestrutura para implantar cargas de trabalho Azure IoT Edge. O tempo de vida do módulo e o balanceamento de carga de rede são gerenciados pelo kubernetes, enquanto a plataforma do aplicativo de borda é gerenciada pelo IoT Edge.

Para obter mais informações sobre como implantar aplicativos no cluster kubernetes por meio de IoT Edge, acesse: 

- [Expor aplicativos sem estado no dispositivo Azure Stack Edge pro via IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes e arco do Azure

O Arc do Azure é uma ferramenta de gerenciamento híbrido que permitirá que você implante aplicativos em seus clusters kubernetes. O arco do Azure também permite que você use Azure Monitor para contêineres para exibir e monitorar seus clusters. Para obter mais informações, acesse [o que é Azure-Arc habilitado kubernetes?](../azure-arc/kubernetes/overview.md). Para obter informações sobre preços de arco do Azure, vá para [preços de arco do Azure](https://azure.microsoft.com/services/azure-arc/#pricing).

A partir de março de 2021, o kubernetes habilitado para Arc do Azure estará disponível para os usuários e os encargos de uso padrão se aplicarem. Como um cliente de visualização com valor, a kubernetes habilitada para o Arc do Azure estará disponível para você sem custos por Azure Stack dispositivos de borda. Para obter a oferta de visualização, crie um [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. Em **Tipo de problema**, selecione **Cobrança**.
2. Em **Assinatura**, selecione sua assinatura.
3. Em **serviço**, selecione **meus serviços** e, em seguida, selecione **Azure Stack borda**.
4. Em **recurso**, selecione seu recurso.
5. Em **Resumo**, digite uma descrição do seu problema.
6. Em **tipo de problema**, selecione **encargos inesperados**.
7. Em **subtipo de problema**, selecione **ajude-me a entender encargos em minha avaliação gratuita**.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o armazenamento kubernetes no [dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md).
- Entenda o modelo de rede kubernetes no [dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-networking.md).
- Implantar o [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) no portal do Azure.
