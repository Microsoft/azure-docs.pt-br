---
title: Entender a rede kubernetes no dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como o kubernetes Networking funciona em um dispositivo Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 001304ad6eda27db2285aaa9ad8b28929e2a04f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899326"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Rede kubernetes em seu dispositivo de GPU pro Azure Stack Edge

Em seu dispositivo Azure Stack Edge pro, um cluster kubernetes é criado quando você configura a função de computação. Depois que o cluster kubernetes é criado, os aplicativos em contêineres podem ser implantados no cluster kubernetes em pods. Há maneiras distintas de que a rede é usada para o pods em seu cluster kubernetes. 

Este artigo descreve a rede em um cluster kubernetes em geral e, especificamente, no contexto de seu dispositivo Azure Stack Edge pro. 

## <a name="networking-requirements"></a>Requisitos de rede

Aqui está um exemplo de um aplicativo típico de duas camadas que é implantado no cluster kubernetes.

- O aplicativo tem um front-end do servidor Web e um aplicativo de banco de dados no back-end. 
- Cada pod é atribuído a um IP, mas esses IPs podem ser alterados na reinicialização e no failover do pod. 
- Cada aplicativo é composto por vários pods e deve haver balanceamento de carga do tráfego em todas as réplicas de Pod. 

![Requisitos de rede kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

O cenário acima resulta nos seguintes requisitos de rede:

 - Há a necessidade de o aplicativo externo para ser acessado por um usuário de aplicativo fora do cluster kubernetes por meio de um nome ou endereço IP. 
 - Os aplicativos no cluster kubernetes, por exemplo, front-end e os pods de back-end aqui devem ser capazes de se comunicar entre si.

Para resolver as necessidades acima, apresentamos um serviço kubernetes. 


## <a name="networking-services"></a>Serviços de rede

Há dois tipos de serviços Kubernetess: 

- **Serviço de IP de cluster** -considere isso como fornecer um ponto de extremidade constante para o pods de aplicativo. Qualquer Pod associado a esses serviços não pode ser acessado de fora do cluster kubernetes. O endereço IP usado com esses serviços é proveniente do espaço de endereço na rede privada. 
- **IP do balanceador de carga** como o serviço IP do cluster, mas o IP associado vem da rede externa e pode ser acessado de fora do cluster kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Rede kubernetes no Azure Stack Edge pro

Calico, Metallb e DNS principal são todos os componentes que estão instalados para rede em seu Azure Stack Edge pro. 

- O **Calico** atribui um endereço IP de um intervalo de IP privado para cada pod e configura a rede para esses pods para que o pod em um nó possa se comunicar com o pod em outro nó. 
- O **Metallb** é executado em um pod no cluster e atribui o endereço IP aos serviços do balanceador de carga de tipo. Os endereços IP do balanceador de carga são escolhidos do intervalo de IP do serviço fornecido por meio da interface do usuário local. 
- **DNS principal** -este complemento configura o nome do serviço de mapeamento de registros DNS para o endereço IP do cluster.

Os endereços IP usados para nós kubernetes e os serviços externos são fornecidos por meio da página de **rede de computação** na interface do usuário local do dispositivo.

![Atribuição de IP kubernetes na interface do usuário local](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

A atribuição de IP é para:

- **IPS do nó kubernetes**: esse intervalo de IP é usado para os nós mestre e de trabalho do kubernetes. Esses IP são usados quando os nós kubernetes se comunicam entre si.

- **IPS de serviço externo kubernetes**: esse intervalo de IP é usado para serviços externos (também conhecidos como serviços de Load Balancer) que são expostos fora do cluster kubernetes.  


## <a name="next-steps"></a>Próximas etapas

Para configurar a rede kubernetes em seu Azure Stack Edge pro, consulte:

- [Expor um aplicativo sem estado externamente em seu Azure Stack Edge pro via IOT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Expor um aplicativo sem estado externamente em seu Azure Stack Edge pro via kuebctl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
