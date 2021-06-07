---
title: Conexão remota a um nó de Cluster Service Fabric do Azure
description: Saiba como se conectar remotamente a uma instância de conjunto de dimensionamento (que é um nó de cluster do Service Fabric).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012537"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Em um cluster do Service Fabric em execução no Azure, cada tipo de nó de cluster que você definir [configura um dimensionamento de máquina virtual separada](service-fabric-cluster-nodetypes.md).  Você pode conectar remotamente a instâncias de definição de dimensionamento específico (nós do cluster).  Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de dimensionamento da VM não recebem um endereço IP virtual próprio. Isso pode ser complicado quando você um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Para localizar um endereço IP e porta que você pode usar para se conectar remotamente a uma instância específica, conclua as etapas a seguir.

1. Obter regras NAT de entrada para o protocolo RDP (Remote Desktop).

    Normalmente, cada tipo de nó definido em seu cluster tem seu próprio endereço IP virtual e um balanceador de carga dedicado. Por padrão, o balanceador de carga para um tipo de nó é nomeado com o seguinte formato: *LB-{nome do cluster}-{tipo de nó}*; por exemplo, *LB-mycluster-FrontEnd*. 
    
    Na página do balanceador de carga no portal do Azure, selecione **configurações**  >  **regras de NAT de entrada**: 

    ![Captura de tela de uma página do balanceador de carga no portal do Azure. No menu à esquerda, em configurações, as regras de NAT de entrada são selecionadas.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    A captura de tela a seguir mostra as regras NAT de entrada para um tipo de nó chamado front-end: 

    ![Captura de tela mostrando as regras NAT de entrada para um balanceador de carga. O nome, a versão do IP, o destino, o destino e o serviço são listados para cada regra.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nó, o endereço IP aparece na coluna **DESTINO**, a coluna **DESTINO** fornece a instância do conjunto de dimensionamento e a coluna **SERVIÇO** fornece o número da porta. Para a conexão remota, as portas são alocadas para cada nó em ordem crescente, começando pela porta 3389.

    Você também pode encontrar as regras NAT de entrada na seção `Microsoft.Network/loadBalancers` do modelo do Gerenciador de Recursos para o cluster.
    
2. Para confirmar a porta de entrada para o mapeamento de porta de destino para um nó, você pode clicar em sua regra e examinar o valor da **porta de destino**. A captura de tela a seguir mostra a regra NAT de entrada para o nó **front-end (Instância 1)** na etapa anterior. Observe que, embora o número de porta seja 3390 (entrada), a porta de destino é mapeada para a porta 3389, a porta para o serviço RDP no destino.  

    ![Mapeamento de porta de destino](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Por padrão, para clusters do Windows, a porta de destino é a porta 3389, que mapeia para o serviço RDP no nó de destino. Para clusters do Linux, a porta de destino é a porta 22, que mapeia para o serviço do Secure Shell (SSH).

3. Conectar-se remotamente ao nó (instância do conjunto de dimensionamento) específico. Você pode usar o nome de usuário e senha que você definiu quando criou o cluster ou outras credenciais que você configurou. 

    A captura de tela a seguir mostra usando a Conexão de área de trabalho remota para conectar-se ao nó **front-end (Instância 1)** em um cluster do Windows:
    
    ![Conexões de Área de Trabalho Remota](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Em nós do Linux, você pode se conectar com SSH (o exemplo a seguir reutiliza o mesmo endereço IP e porta para fins de brevidade):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para as próximas etapas, leia os seguintes artigos:
* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nas VMs de cluster após a implantação
* [Alterar o nome de usuário administrador e a senha](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para as VMs do cluster

