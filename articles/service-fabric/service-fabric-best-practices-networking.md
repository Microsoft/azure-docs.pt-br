---
title: Práticas recomendadas de rede Service Fabric do Azure
description: Regras e considerações de design para gerenciar a conectividade de rede usando o Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: caba864e77822ccab649f694df7e63e0ee5d6e51
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732557"
---
# <a name="networking"></a>Rede

Conforme você cria e gerencia clusters do Azure Service Fabric, você fornece conectividade de rede para seus nós e seus aplicativos. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, balanceadores de carga e grupos de segurança de rede. Neste artigo, você aprenderá as melhores práticas para esses recursos.

Examine os [padrões de rede Service Fabric](./service-fabric-patterns-networking.md) do Azure para saber como criar clusters que usam os seguintes recursos: rede virtual ou sub-rede existente, endereço IP público estático, balanceador de carga somente interno ou balanceador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de infraestrutura
Maximize o desempenho da sua máquina virtual com rede acelerada, declarando a propriedade *enableAcceleratedNetworking* em seu modelo do Resource Manager, o trecho a seguir é de um NetworkInterfaceConfigurations do conjunto de dimensionamento de máquinas virtuais que permite a rede acelerada:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
O cluster do Service Fabric pode ser provisionado no [Linux com a Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md) e no [Windows com a Rede Acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md).

A rede acelerada tem suporte para SKUs da série de máquinas virtuais do Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e MS/MMS. A rede acelerada foi testada com êxito usando o Standard_DS8_v3 SKU em 01/23/2019 para um Cluster Service Fabric Windows e usando Standard_DS12_v2 em 01/29/2019 para um Cluster Service Fabric Linux. Observe que a rede acelerada requer pelo menos 4 vCPUs. 

Para habilitar a Rede Acelerada em um cluster existente do Service Fabric, primeiro você precisará [Expandir um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais](./virtual-machine-scale-set-scale-node-type-scale-out.md) para executar o seguinte:
1. Provisionar um NodeType com a Rede Acelerada habilitada
2. Migrar serviços e seus estados para o NodeType provisionado com a Rede Acelerada habilitada

A expansão da infraestrutura é necessária para habilitar a Rede Acelerada em um cluster existente, pois a habilitação da Rede Acelerada in-loco causará tempo de inatividade, já que ela exige que todas as máquinas virtuais em um conjunto de disponibilidade sejam [paradas e desalocadas antes de habilitar a Rede Acelerada em uma NIC existente](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Rede de cluster

* Os clusters do Service Fabric podem ser implantados em uma rede virtual existente seguindo as etapas descritas em [Padrões de rede do Service Fabric](./service-fabric-patterns-networking.md).

* Os NSGs (grupos de segurança de rede) são recomendados para tipos de nó para restringir o tráfego de entrada e de saída para seu cluster. Verifique se as portas necessárias estão abertas no NSG. 

* O tipo de nó primário, que contém os serviços do sistema do Service Fabric, não precisa ser exposto por meio do balanceador de carga externo e pode ser exposto por um [balanceador de carga interno](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Use um [endereço IP público estático](./service-fabric-patterns-networking.md#static-public-ip-address-1) para seu cluster.

## <a name="network-security-rules"></a>Regras de Segurança de Rede

As regras básicas aqui são o mínimo para um bloqueio de segurança de um cluster de Service Fabric gerenciado do Azure. A falha em abrir as portas a seguir ou aprovar o IP/URL impedirá a operação adequada do cluster e poderá não ter suporte. Com esse conjunto de regras, é estritamente necessário usar [atualizações automáticas de imagem do sistema operacional](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md), caso contrário, as portas adicionais precisarão ser abertas.

### <a name="inbound"></a>Entrada 
|Prioridade   |Nome               |Porta        |Protocolo  |Fonte             |Destino       |Ação   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Allow
|3910       |Cliente             |19000       |TCP       |Internet           |VirtualNetwork    |Allow
|3920       |Cluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3930       |Efêmeras          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3940       |Aplicativo        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Negar
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Negar
|3980       |Ponto de extremidade personalizado    |80          |TCP       |Internet           |VirtualNetwork    |Allow
|4100       |Bloquear entrada      |443         |Qualquer       |Qualquer                |Qualquer               |Allow

Mais informações sobre as regras de segurança de entrada:

* **Azure**. Essa porta é usada pelo Service Fabric Explorer para procurar e gerenciar seu cluster e também é usada pelo provedor de recursos de Service Fabric para consultar informações sobre o cluster a fim de exibi-los na Portal de Gerenciamento do Azure. Se essa porta não estiver acessível do provedor de recursos de Service Fabric, você verá uma mensagem como ' nós não encontrados ' ou ' UpgradeServiceNotReachable ' no portal do Azure e sua lista de nós e aplicativos aparecerá vazia. Isso significa que, se você quiser ter visibilidade do cluster na Portal de Gerenciamento do Azure, o balanceador de carga deverá expor um endereço IP público e seu NSG deverá permitir o tráfego de entrada 19080.  

* **Cliente** do. O ponto de extremidade de conexão de cliente para APIs como REST/PowerShell/CLI. 

* **Cluster**. Usado para comunicação entre nós; Nunca deve ser bloqueado.

* **Efêmero**. O Service Fabric usa parte dessas portas como portas do aplicativo e o restante fica disponível para o SO. Ele também mapeia esse intervalo para o intervalo existente presente no sistema operacional, portanto, para todas as finalidades, você pode usar os intervalos fornecidos no exemplo aqui. Verifique se a diferença entre as portas de início e de fim é de pelo menos 255. Você poderá encontrar conflitos se a diferença for muito baixa, uma vez que esse intervalo é compartilhado com o sistema operacional. Para ver o intervalo de portas dinâmicas configurado, execute *netsh int IPv4 mostrar porta dinâmica TCP*. Essas portas não são necessárias para clusters do Linux.

* **Aplicativo**. O intervalo de portas do aplicativo deve ser amplo o bastante para cobrir o requisito de ponto de extremidade dos aplicativos. Esse intervalo deve ser exclusivo no intervalo de portas dinâmico no computador, isto é, o intervalo ephemeralPorts conforme definido na configuração. O Service Fabric usa essas portas sempre que novas portas são necessárias e cuida da abertura do firewall para essas portas nos nós.

* **SMB**. O protocolo SMB está em uso pelo serviço ImageStore para dois cenários. Essa porta é necessária para baixar os pacotes do ImageStore pelos nós, bem como para duplicá-los entre as réplicas. 

* **RDP**. Opcional, se o RDP for necessário na Internet ou no VirtualNetwork para cenários Jumpbox. 

* **SSH**. Opcional, se o SSH for necessário na Internet ou no VirtualNetwork para cenários Jumpbox.

* **Ponto de extremidade personalizado**. Um exemplo para seu aplicativo habilitar um ponto de extremidade acessível pela Internet.

### <a name="outbound"></a>Saída

|Prioridade   |Nome               |Porta        |Protocolo  |Fonte             |Destino       |Ação   
|---        |---                |---         |---       |---                |---               |---
|3900       |Rede            |Qualquer         |TCP       |VirtualNetwork     |VirtualNetwork    |Allow
|3910       |Provedor de recursos  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Allow
|3920       |Atualizar            |443         |TCP       |VirtualNetwork     |Internet          |Allow
|3950       |Bloquear saída     |Qualquer         |Qualquer       |Qualquer                |Qualquer               |Negar

Mais informações sobre as regras de segurança de saída:

* **Rede**. Canal de comunicação para sub-redes e para outras redes virtuais.

* **Provedor de recursos**. Conexão pelo UpgradeService para executar todas as implantações do ARM pelo provedor de recursos Service Fabric.

* **Atualização**. O serviço de atualização usando o endereço download.microsoft.com para obter os bits, isso é necessário para a instalação, refazer a imagem e as atualizações de tempo de execução. O serviço opera com endereços IP dinâmicos. No cenário de um balanceador de carga "somente interno", um balanceador de carga externo adicional deve ser adicionado ao modelo com uma regra que permita o tráfego de saída para a porta 443. Opcionalmente, essa porta pode ser bloqueada após uma configuração bem-sucedida, mas, nesse caso, o pacote de atualização deve ser distribuído para os nós ou a porta precisa ser aberta por um curto período de tempo, posteriormente, uma atualização manual é necessária.

Use o Firewall do Azure com o [log de fluxo do NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) e a análise de [tráfego](../network-watcher/traffic-analytics.md) para acompanhar problemas com o bloqueio de segurança. O modelo ARM [Service Fabric com NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) é um bom exemplo para começar. 


## <a name="application-networking"></a>Rede de aplicativo

* Para executar cargas de trabalho de contêiner do Windows, use o [modo de rede aberto](./service-fabric-networking-modes.md#set-up-open-networking-mode) para facilitar a comunicação de serviço a serviço.

* Use um proxy reverso, como o [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou o [proxy reverso do Service Fabric](./service-fabric-reverseproxy.md), para expor portas de aplicativo comuns, como 80 ou 443.

* Para contêineres do Windows hospedados em computadores gapped que não podem efetuar pull de camadas base do armazenamento em nuvem do Azure, substitua o comportamento da camada estrangeira usando o sinalizador [--Allow-unredistributable-artefatos](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
