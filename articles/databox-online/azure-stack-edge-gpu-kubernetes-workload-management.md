---
title: Entender o gerenciamento de carga de trabalho kubernetes no dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como as cargas de trabalho do kubernetes podem ser gerenciadas em seu dispositivo Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: ef840b3d9db4e82eeecea37079a08ccb0858a77b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448530"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Gerenciamento de carga de trabalho do kubernetes no dispositivo pro Edge Azure Stack

Em seu dispositivo Azure Stack Edge pro, um cluster kubernetes é criado quando você configura a função de computação. Depois que o cluster kubernetes é criado, os aplicativos em contêineres podem ser implantados no cluster kubernetes em pods. Existem diferentes maneiras de implantar cargas de trabalho no cluster do Kubernetes. 

Este artigo descreve os vários métodos que podem ser usados para implantar cargas de trabalho em seu dispositivo Azure Stack Edge pro.

## <a name="workload-types"></a>Tipos de carga de trabalho

Os dois tipos comuns de cargas de trabalho que você pode implantar em seu dispositivo Azure Stack Edge pro são aplicativos sem estado ou aplicativos com estado.

- **Aplicativos sem estado** não preservam seu estado e não salvam dados no armazenamento persistente. Todos os dados de usuário e de sessão permanecem com o cliente. Alguns exemplos de aplicativos sem estado incluem front-ends da Web como Nginx e outros aplicativos Web.

    Você pode criar uma implantação do kubernetes para implantar um aplicativo sem estado no cluster. 

- **Os aplicativos com estado** exigem que seu estado seja salvo. Os aplicativos com estado usam armazenamento persistente, como volumes persistentes, para salvar dados para uso pelo servidor ou por outros usuários. Exemplos de aplicativos com estado incluem bancos de dados como o [Azure SQL Edge](../azure-sql-edge/overview.md) e o MongoDB.

    Você pode criar uma implantação do kubernetes para implantar um aplicativo com estado. 

## <a name="deployment-flow"></a>Fluxo de implantação

Para implantar aplicativos em um dispositivo Azure Stack Edge pro, você seguirá estas etapas: 
 
1. **Configurar o acesso**: primeiro, você usará o runspace do PowerShell para criar um usuário, criar um namespace e conceder acesso ao usuário para esse namespace.
2. **Configurar o armazenamento**: em seguida, você usará o Azure Stack recurso do Edge no portal do Azure para criar volumes persistentes usando o provisionamento estático ou dinâmico para os aplicativos com estado que serão implantados.
3. **Configurar a rede**: por fim, você usará os serviços para expor aplicativos externamente e dentro do cluster kubernetes.
 
## <a name="deployment-types"></a>Tipos de implantação

Há três maneiras principais de implantar suas cargas de trabalho. Cada uma dessas metodologias de implantação permite que você se conecte a um namespace distinto no dispositivo e, em seguida, implante aplicativos com ou sem estado.

![Implantação de carga de trabalho kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implantação local**: essa implantação é por meio da ferramenta de acesso de linha de comando, como a `kubectl` que permite implantar kubernetes `yamls` . Você acessa o cluster kubernetes no Azure Stack Edge pro por meio de um `kubeconfig` arquivo. Para obter mais informações, acesse [acessar um cluster kubernetes por meio de kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Implantação de IOT Edge**: isso ocorre por meio de IOT Edge, que se conecta ao Hub IOT do Azure. Você se conecta ao cluster kubernetes no dispositivo pro Edge Azure Stack por meio do `iotedge` namespace. Os agentes de IoT Edge implantados nesse namespace são responsáveis pela conectividade com o Azure. Você aplica a `IoT Edge deployment.json` configuração usando o CI/CD do DevOps do Azure. O namespace e o gerenciamento de IoT Edge são feitos por meio do operador de nuvem.

- **Implantação de kubernetes habilitada para Arc do Azure**: o kubernetes habilitado para Arc do Azure é uma ferramenta de gerenciamento híbrido que permitirá que você implante aplicativos em seus clusters do kubernetes. Você se conecta ao cluster kubernetes em seu dispositivo Azure Stack Edge pro por meio do `azure-arc namespace` . Os agentes implantados nesse namespace são responsáveis pela conectividade com o Azure. Você aplica a configuração de implantação usando o gerenciamento de configuração baseado em GitOps. 
    
    O kubernetes habilitado para Arc do Azure também permitirá que você use Azure Monitor para contêineres para exibir e monitorar o cluster. Para obter mais informações, acesse [o que é o Azure Arc habilitado kubernetes?](../azure-arc/kubernetes/overview.md).

## <a name="choose-the-deployment-type"></a>Escolher o tipo de implantação

Ao implantar aplicativos, considere as seguintes informações:

- **Tipos únicos ou múltiplos**: você pode escolher uma única opção de implantação ou uma combinação de opções de implantação diferentes.
- **Nuvem versus local**: dependendo de seus aplicativos, você pode escolher a implantação local via kubectl ou implantação de nuvem por meio do IOT Edge e do Arc do Azure. 
    - Ao escolher uma implantação local, você é restrito à rede na qual seu dispositivo Azure Stack Edge pro está implantado.
    - Se você tiver um agente de nuvem que possa implantar, você deve implantar seu operador de nuvem e usar o gerenciamento de nuvem.
- **IOT vs. arc do Azure**: a escolha da implantação também depende da intenção do seu cenário de produto. Se você estiver implantando aplicativos ou contêineres com integração mais profunda com o ecossistema IoT ou IoT, selecione IoT Edge para implantar seus aplicativos. Se você tiver implantações kubernetes existentes, o arco do Azure será a escolha preferida.


## <a name="next-steps"></a>Próximas etapas

Para implantar localmente um aplicativo por meio do kubectl, consulte:

- [Implante um aplicativo sem estado em seu Azure Stack Edge pro via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Para implantar um aplicativo por meio do IoT Edge, consulte:

- [Implante um módulo de exemplo em seu Azure Stack Edge pro via IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Para implantar um aplicativo por meio do Azure Arc, consulte:

- [Implantar um aplicativo usando o arco do Azure](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).