---
title: "Grupos de segurança de rede no Azure | Microsoft Docs"
description: "Saiba como isolar e controlar o fluxo de tráfego dentro de suas redes virtuais usando o firewall distribuído no Azure com os Grupos de Segurança de Rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: fac6ee69b5f0377e0515ac9abeb28788cbef9b79
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="filter-network-traffic-with-network-security-groups"></a>Filtrar o tráfego de rede com grupos de segurança de rede

Um NSG (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a VNets (redes virtuais) do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou interfaces de rede individuais (NIC) anexadas a VMs (Resource Manager). Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. O tráfego pode ser restritao também associando um NSG a uma VM ou NIC.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso de ambos os modelos, mas a Microsoft recomenda as mais novas implantações usem o modelo do Gerenciador de Recursos.

## <a name="nsg-resource"></a>Recurso NSG
Os NSGs contêm as seguintes propriedades:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| Nome |Nome do NSG |Deve ser exclusivo na região.<br/>Pode conter letras, números, sublinhados, pontos e hifens.<br/>Deve começar com uma letra ou número.<br/>Deve terminar com uma letra, número ou sublinhado.<br/>Não pode exceder 80 caracteres. |Como talvez seja necessário criar vários NSGs, use uma convenção de nomenclatura que facilite a identificação da função dos NSGs. |
| Região |[Região](https://azure.microsoft.com/regions) do Azure em que o NSG é criado. |Os NSGs só podem ser associados a recursos na mesma região que o NSG. |Para saber mais sobre quantos NSGs você pode ter por região, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#virtual-networking-limits-classic).|
| Grupo de recursos |O [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) em que o NSG existe. |Embora um NSG exista em um grupo de recursos, ele pode ser associado a recursos em qualquer grupo de recursos, desde que o recurso faça parte da mesma região do Azure que o NSG. |Os grupos de recursos são usados para gerenciar vários recursos juntos, como uma unidade de implantação.<br/>Você pode considerar o agrupamento do NSG com recursos ao qual ele está associado. |
| Regras |Regras de entrada ou saída que definem o tráfego que é permitido ou negado. | |Confira a seção [Regras de NSG](#Nsg-rules) deste artigo. |

> [!NOTE]
> Não há suporte para grupos de segurança de rede e ACLs baseadas em ponto de extremidade na mesma instância de VM. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para saber como remover uma ACL, leia o artigo [Gerenciando ACLs (listas de controle de acesso) para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md).
> 

### <a name="nsg-rules"></a>Regras NSG
As regras NSG contêm as propriedades a seguir:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| **Nome** |Nome para a regra. |Deve ser exclusivo na região.<br/>Pode conter letras, números, sublinhados, pontos e hifens.<br/>Deve começar com uma letra ou número.<br/>Deve terminar com uma letra, número ou sublinhado.<br/>Não pode exceder 80 caracteres. |Você pode ter várias regras em um NSG, portanto, siga uma convenção de nomenclatura que permita a identificação da função da sua regra. |
| **Protocolo** |Protocolo para fazer a correspondência da regra. |TCP, UDP ou * |O uso de * como um protocolo inclui ICMP (somente tráfego Leste-Oeste), bem como TCP e UDP, e pode reduzir o número de regras necessárias.<br/>Ao mesmo tempo, o uso de * pode ser uma abordagem muito ampla. Portanto, é recomendável que você use * somente quando necessário. |
| **Intervalo de portas de origem** |Intervalo de portas de origem para fazer a correspondência da regra. |Número de porta única de 1 a 65535, intervalo de portas (exemplo: 1 a 65535) ou * (para todas as portas). |As portas de origem pode ser efêmeras. A menos que seu programa cliente esteja usando uma porta específica, use * na maioria dos casos.<br/>Tente usar o máximo possível de intervalos de portas para evitar a necessidade de várias regras.<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula. |
| **Intervalo de portas de destino** |Intervalo de portas de destino para fazer a correspondência da regra. |Número de porta única de 1 a 65535, intervalo de portas (exemplo: 1 a 65535) ou \* (para todas as portas). |Tente usar o máximo possível de intervalos de portas para evitar a necessidade de várias regras.<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula. |
| **Prefixo de endereço de origem** |Prefixo ou marca de endereço de origem para fazer a correspondência da regra. |Um único endereço IP (exemplo: 10.10.10.10), sub-rede de IP (exemplo: 192.168.1.0/24), [marca padrão](#default-tags) ou * (para todos os endereços). |Considere o uso de intervalos, marcas padrão e * para reduzir o número de regras. |
| **Prefixo de endereço de destino** |Prefixo ou marca de endereço de destino para fazer a correspondência da regra. | Um único endereço IP (exemplo: 10.10.10.10), sub-rede de IP (exemplo: 192.168.1.0/24), [marca padrão](#default-tags) ou * (para todos os endereços). |Considere o uso de intervalos, marcas padrão e * para reduzir o número de regras. |
| **Direção** |Direção do tráfego para fazer a correspondência da regra. |Entrada ou saída. |Regras de entrada e saída são processadas separadamente, com base na direção. |
| **Prioridade** |As regras são verificadas na ordem de prioridade. Depois que uma regra se aplica, outras regras não são testadas quanto à correspondência. | Número entre 100 e 4096. | Considere a criação de regras aumentando as prioridades em 100 para cada regra para deixar espaço para as novas regras que você criar no futuro. |
| **Access** |Tipo de acesso a ser aplicado se a regra for correspondente. | Permitir ou negar. | Lembre-se, se uma regra de permissão não for encontrada para um pacote, ele será descartado. |

Os NSGs contêm dois conjuntos de regras: entrada e saída. A prioridade de uma regra deve ser exclusiva em cada conjunto. 

![Processamento de regras do NSG](./media/virtual-network-nsg-overview/figure3.png) 

A figura anterior mostra como as regras de NSG são processadas.

### <a name="default-tags"></a>Marcas padrão
Marcas padrão são identificadores fornecidos pelo sistema para atender a uma categoria de endereços IP. Você pode usar marcas padrão nas propriedades **prefixo de endereço de origem** e **prefixo de endereço de destino** de qualquer regra. Há três marcas padrão que você pode usar:

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** para clássico): essa marca inclui o espaço de endereço de rede virtual (intervalos CIDR definidos no Azure), todos os espaços de endereço locais conectados e todas as conectados do Azure conectadas (redes locais).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** para clássico): essa marca denota o balanceador de carga de infraestrutura do Azure. A marca significa um IP de datacenter do Azure de onde se originam as investigações de integridade do Azure.
* **Internet** (Resource Manager) (**INTERNET** para clássico): essa marca denota o espaço de endereço IP que está fora da rede virtual e é acessível pela Internet pública. O intervalo inclui o [espaço IP público do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="default-rules"></a>Regras padrão
Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. 

As regras padrão permitem e impedem o tráfego da seguinte maneira:
- **Rede virtual:** o tráfego que começa e termina em uma rede virtual é permitido nas direções de entrada e saída.
- **Internet:** o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga:** o balanceador de carga do Azure permite investigar a integridade de suas VMs e instâncias de função. Se não estiver usando um conjunto de balanceamento de carga, você poderá substituir essa regra.

**Regras de entrada padrão**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVNetInBound |65000 | VirtualNetwork | * | VirtualNetwork | * | * | PERMITIR |
| AllowAzureLoadBalancerInBound | 65001 | AzureLoadBalancer | * | * | * | * | PERMITIR |
| DenyAllInBound |65500 | * | * | * | * | * | NEGAR |

**Regras de saída padrão**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVnetOutBound | 65000 | VirtualNetwork | * | VirtualNetwork | * | * | PERMITIR |
| AllowInternetOutBound | 65001 | * | * | Internet | * | * | PERMITIR |
| DenyAllOutBound | 65500 | * | * | * | * | * | NEGAR |

## <a name="associating-nsgs"></a>Associando NSGs
Você pode associar um NSG a VMs, NICs e sub-redes, dependendo do modelo de implantação que estiver usando, da seguinte maneira:

* **VM (apenas clássico):** regras de segurança são aplicadas a todo o tráfego de/para a VM. 
* **NIC (somente Resource Manager):** regras de segurança são aplicadas a todo o tráfego para/da NIC à quak o NSG está associado. Em uma VM com várias NICs, você pode aplicar diferentes NSGs (ou o mesmo) a cada NIC individualmente. 
* **Subrede (Resource Manager e clássico):** regras de segurança são aplicadas a qualquer tráfego de/para quaisquer recursos conectados à VNet.

É possível associar diferentes NSGs a uma VM (ou NIC, dependendo do modelo de implantação) e à sub-rede a qual uma NIC ou VM está conectada. Regras de segurança são aplicadas ao tráfego por prioridade, em cada NSG, na seguinte ordem:

- **Tráfego de entrada**

  1. **NSG aplicado à sub-rede:** se o NSG de uma sub-rede tiver uma regra correspondente para negar o tráfego, o pacote será descartado.

  2. **NSG aplicado a NIC** (Resource Manager) ou VM (clássico): se NSG VM\NIC tem uma regra de correspondência que nega o tráfego, os pacotes são descartados em VM\NIC, mesmo que um NSG de sub-rede tenha uma regra de correspondência que permita o tráfego.

- **Tráfego de saída**

  1. **NSG aplicadas a NIC** (Resource Manager) ou VM (clássico): se um NSG VM\NIC tem uma regra de correspondência que nega o tráfego, os pacotes são descartados.

  2. **NSG aplicado à sub-rede:** se um NSG de sub-rede tem uma regra de correspondência que nega o tráfego, os pacotes são descartados, mesmo que um NSG VM\NIC tenha uma regra de correspondência que permita o tráfego.

> [!NOTE]
> Embora seja possível associar apenas um único NSG a uma sub-rede, VM ou NIC, é possível associar o mesmo NSG a quantos recursos você desejar.
>

## <a name="implementation"></a>Implementação
Você pode implementar NSGs no Resource Manager ou modelos de implantação clássica usando as seguintes ferramentas:

| Ferramenta de implantação | Clássico | Gerenciador de Recursos |
| --- | --- | --- |
| Portal do Azure   | Sim | [Sim](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Sim](virtual-networks-create-nsg-classic-ps.md) | [Sim](virtual-networks-create-nsg-arm-ps.md) |
| CLI do Azure **V1**   | [Sim](virtual-networks-create-nsg-classic-cli.md) | [Sim](virtual-networks-create-nsg-cli-nodejs.md) |
| CLI do Azure **V2**   | Não | [Sim](virtual-networks-create-nsg-arm-cli.md) |
| Modelo do Azure Resource Manager   | Não  | [Sim](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Planejamento
Antes de implementar NSGs, você precisa responder às perguntas a seguir:

1. Para quais tipos de recursos você deseja filtrar o tráfego de entrada ou saída? Você pode se conectar a recursos, como NICs (Resource Manager), máquinas virtuais (clássico), serviços de nuvem, ambientes de serviço de aplicativos e Conjuntos de Dimensionamento de VMs. 
2. Os recursos em que você deseja filtrar o tráfego de entrada ou saída estão conectados a sub-redes em VNEts existentes?

Para saber mais sobre como planejar a segurança da rede no Azure, leia o artigo [Serviços de nuvem e segurança de rede](../best-practices-network-security.md). 

## <a name="design-considerations"></a>Considerações sobre o design
Depois de consultar as respostas para as perguntas na seção [Planejamento](#Planning) , examine as seguintes seções antes de definir os NSGs:

### <a name="limits"></a>limites
Há limites para o número de NSGs que você pode ter em uma assinatura e o número de regras por NSG. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

### <a name="vnet-and-subnet-design"></a>Design da rede virtual e da sub-rede
Uma vez que os NSGs podem ser aplicados às sub-redes, você pode minimizar o número de NSGs agrupando recursos por sub-rede e aplicando NSGs às sub-redes.  Se optar por aplicar NSGs às sub-redes, você pode descobrir que as redes virtuais e sub-redes existentes que não foram definidas com NSGs em mente. Talvez seja necessário definir novas redes virtuais e sub-redes para dar suporte a seu design de NSG e implantar novos recursos em novas sub-redes. Então será possível definir uma estratégia de migração para mover os recursos existentes para as novas sub-redes. 

### <a name="special-rules"></a>Regras especiais
Se você bloquear o tráfego permitido pelas seguintes regras, a infraestrutura não poderá se comunicar com os serviços do Azure essenciais:

* **IP virtual do nó do host:** serviços básicos de infraestrutura, como DHCP, DNS e integridade de monitoramento, são fornecidos pelo endereço IP virtualizado host 168.63.129.16. Este endereço IP público pertence à Microsoft e é o único endereço IP virtualizado usado em todas as regiões para essa finalidade. Esse endereço IP é mapeado para o endereço IP físico do computador do servidor (nó do host) que hospeda a VM. O nó do host atua como a retransmissão DHCP, o solucionador de DNS recursivo e a fonte de sonda para a investigação de integridade do balanceador de carga e a investigação de integridade da máquina. A comunicação com esse endereço IP não é um ataque.
* **Licenciamento (serviço de gerenciamento de chaves):** imagens do Windows em execução em VMs devem ser licenciadas. Para garantir o licenciamento, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. A solicitação é feita para a saída pela porta 1688.

### <a name="icmp-traffic"></a>Tráfego ICMP
As atuais regras do NSG permitem apenas os protocolos *TCP* ou *UDP*. Não há uma marca específica para o *ICMP*. No entanto, o tráfego ICMP é permitido em uma rede virtual pela regra padrão AllowVNetInBound, que permite o tráfego de e para qualquer porta e protocolo na VNet.

### <a name="subnets"></a>Sub-redes
* Considere o número de camadas exigidas pela carga de trabalho. Cada camada pode ser isolada por meio de uma sub-rede, com um NSG aplicado à sub-rede. 
* Se precisar implementar uma sub-rede para um gateway de VPN ou circuito de ExpressRoute, **não** aplique um NSG a essa sub-rede. Se você fizer isso, a conectividade entre VNet ou entre locais poderá falhar. 
* Se precisar implementar um NVA (dispositivo de rede virtual), conecte o NVA a sua própria sub-rede e crie rotas definidas pelo usuário (UDR) de e para o NVA. É possível implementar um NSG de nível de sub-rede para filtrar o tráfego que entra e sai dessa sub-rede. Para saber mais sobre UDRs, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Balanceadores de carga
* Considere as regras de balanceamento de carga de rede e conversão de endereço (NAT) para cada balanceador de carga usado por cada uma das suas cargas de trabalho. Regras NAT são vinculadas a um pool de back-end que contém instâncias de função de serviços de nuvem/máquinas virtuais (clássicas) ou NICs (Resource Manager). Considere a criação de um NSG para cada pool de back-end, permitindo apenas o tráfego mapeado por meio das regras implementadas nos balanceadores de carga. Criar um NSG para cada pool de back-end garante que o tráfego de entrada vindo para o pool de back-end diretamente (e não por meio do balanceador de carga) também seja filtrado.
* Em implantações clássicas, você cria pontos de extremidade que mapeiam portas em um balanceador de carga para portas nas VMs ou instâncias de função. Você também pode criar seu próprio balanceador de carga público individual por meio do Resource Manager. A porta de destino para tráfego de entrada é a porta real na instância de função ou VM, não a porta exposta por um balanceador de carga. A porta e o endereço de origem para a conexão com a VM é a porta e o endereço no computador remoto na Internet, não a porta e o endereço expostos pelo balanceador de carga.
* Quando você cria NSGs para filtrar o tráfego de entrada por meio de um balanceador de carga interno (ILB), o intervalo de portas e endereços de origem aplicados são do computador de origem, não do balanceador de carga. Os intervalos de porta e endereço de destino são aos do computador de destino, não do balanceador de carga.

### <a name="other"></a>outro
* Listas de controle de acesso baseado em ponto de extremidade (ACL) e NSGs não são suportados na mesma instância da VM. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para obter informações sobre como remover um ponto de extremidade ACL, confira o artigo [Gerenciar ACLs de ponto de extremidade](virtual-networks-acl-powershell.md).
* No Resource Manager, você pode usar um NSG associado a uma NIC para VMs com várias NICs para habilitar o gerenciamento (acesso remoto) por NIC. A associação de NSGs exclusivos a cada NIC permite a separação dos tipos de tráfego em NICs.
* De forma semelhante ao uso de balanceadores de carga, ao filtrar o tráfego de outras redes virtuais, você deve usar o intervalo de endereços de origem do computador remoto, não o gateway que se conecta às redes virtuais.
* Muitos serviços do Azure não podem ser conectados a redes virtuais. Se um recurso do Azure não estiver conectado a uma rede virtual, será possível usar um NSG para filtrar o tráfego para o recurso.  Leia a documentação dos serviços que você usa para determinar se o serviço pode ser conectado a uma VNet.

## <a name="sample-deployment"></a>Exemplo de implantação
Para ilustrar a aplicação das informações neste artigo, considere um cenário comum de um aplicativo de duas camadas mostrado na figura a seguir:

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

Como mostrado no diagrama, as VMs *Web1* e *Web2* estão conectados à sub-rede *FrontEnd*, e as VMs *DB1* e *DB2* estão conectadas à sub-rede *BackEnd*.  Ambas as sub-redes fazem parte da Rede Virtual *TestVNet* . Os componentes de aplicativos executados em uma VM do Azure conectada a uma VNet. O cenário tem os seguintes requisitos:

1. Separação do tráfego entre os servidores WEB e banco de dados.
2. As regras de balanceamento de carga que encaminham o tráfego do balanceador de carga a todos os servidores na porta 80.
3. Regras NAT do balanceador de caraga encaminham o tráfego de entrada do balanceador de carga na porta 50001 para a porta 3389 na VM WEB1.
4. Sem acesso às VMs de front-end ou back-end da Internet, exceto os requisitos 2 e 3.
5. Sem acesso à Internet de saída dos servidores WEB ou DB.
6. O acesso da sub-rede de front-end é permitido para a porta 3389 de qualquer servidor Web.
7. O acesso da sub-rede de front-end é permitido para a porta 3389 de qualquer servidor DB.
8. O acesso da sub-rede de front-end é permitido para a porta 1433 de todos os servidores DB.
9. Separação do tráfego de gerenciamento (porta 3389) e do tráfego de banco de dados (1433) nas diferentes NICs de servidores DB.

Os requisitos 1 a 6 (exceto os requisitos 3 e 4) são restritos aos espaços de sub-rede. Os seguir NSGs atendem aos requisitos anteriores, minimizando o número de NSGs necessários:

### <a name="frontend"></a>FrontEnd
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | PERMITIR | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | PERMITIR | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | NEGAR | 300 | Internet | * | * | * | TCP |

**Regras de saída**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |NEGAR |100 | * | * | Internet | * | * |

### <a name="backend"></a>BackEnd
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | NEGAR | 100 | Internet | * | * | * | * |

**Regras de saída**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | NEGAR | 100 | * | * | Internet | * | * |

Os NSGs a seguir são criados e associados a NICs nas seguintes VMs:

### <a name="web1"></a>WEB1
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | PERMITIR | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | PERMITIR | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> O intervalo de endereços de origem para as regras anteriores é a **Internet**, não o endereço IP virtual do balanceador de carga. A porta de origem é *, não 500001. As regras NAT para balanceadores de carga não são iguais às regras de segurança NSG. As regras de segurança de NSG sempre estão relacionadas à verdadeira origem e ao destino final do tráfego, **não** ao balanceador de carga entre os dois. 
> 
> 

### <a name="web2"></a>WEB2
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | NEGAR | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | PERMITIR | 200 | Internet | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>Servidores DB (NIC de gerenciamento)
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | PERMITIR | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>Servidores DB (tráfego NIC do banco de dados)
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | PERMITIR | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Como alguns dos NSGs são associados a NICs individuais, as regras são para os recursos implantados por meio do Resource Manager. As regras são combinadas para sub-rede e NIC, dependendo de como estão associados. 

## <a name="next-steps"></a>Próximas etapas
* [Implantar NSGs (Gerenciador de Recursos)](virtual-networks-create-nsg-arm-pportal.md).
* [Implantar NSGs (clássico)](virtual-networks-create-nsg-classic-ps.md).
* [Gerenciar logs do NSG](virtual-network-nsg-manage-log.md).
* [Solucionar problemas de NSGs] (virtual-network-nsg-troubleshoot-portal.md)
