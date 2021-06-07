---
title: Conceitos-nuvens e clusters privados
description: Saiba mais sobre os principais recursos de data centers definidos pelo software da solução Azure VMware e clusters vSphere.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773906"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Conceitos de nuvem privada e de cluster da solução Azure VMware

A solução Azure VMware fornece nuvens privadas baseadas no VMware no Azure. Nuvens privadas contêm clusters criados com hosts do Azure dedicados e bare-metal. Eles são implantados e gerenciados por meio do portal do Azure, da CLI ou do PowerShell.  Os clusters provisionados em nuvens privadas incluem o software VMware vSphere, vCenter, vSAN e NSX. As implantações de hardware e software na nuvem privada da solução Azure VMware são totalmente integradas e automatizadas no Azure.

Há uma relação lógica entre as assinaturas do Azure, as nuvens privadas da solução Azure VMware, os clusters vSAN e os hosts. O diagrama mostra uma única assinatura do Azure com duas nuvens privadas que representam o ambiente de desenvolvimento e produção.  Em cada uma dessas nuvens privadas estão dois clusters. 

Este artigo descreve todos esses conceitos.

![Imagem de duas nuvens privadas em uma assinatura de cliente](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Nuvens privadas

Nuvens privadas contêm clusters vSAN criados com hosts do Azure dedicados e bare-metal. Cada nuvem privada pode ter vários clusters gerenciados pelo mesmo servidor vCenter e pelo NSX-T Manager. Você pode implantar e gerenciar nuvens privadas no portal, na CLI ou no PowerShell. 

Assim como ocorre com outros recursos, as nuvens privadas são instaladas e gerenciadas de dentro de uma assinatura do Azure. O número de nuvens privadas em uma assinatura é escalonável. Inicialmente, há um limite de uma nuvem privada por assinatura.

## <a name="clusters"></a>Clusters
Para cada nuvem privada criada, há um cluster vSAN por padrão. Você pode adicionar, excluir e dimensionar clusters usando o portal do Azure ou por meio da API.  Todos os clusters têm um tamanho padrão de três hosts e podem ser escalados verticalmente para 16 hosts. Você pode ter até quatro clusters por nuvem privada.

Os clusters de avaliação estão disponíveis para avaliação e limitados a três hosts. Há um único cluster de avaliação por nuvem privada. Você pode dimensionar um cluster de avaliação por um único host durante o período de avaliação.

Você usa o vSphere e o NSX-T Manager para gerenciar a maioria dos outros aspectos da configuração ou operação de cluster. Todo o armazenamento local de cada host em um cluster está sob o controle da vSAN.

## <a name="hosts"></a>Hosts

Os clusters de solução do Azure VMware são baseados na infraestrutura hiperconvergente e bare-metal. A tabela a seguir mostra a RAM, a CPU e as capacidades de disco do host.

| Tipo de host              |             CPU             |   RAM (GB)   |  Camada de cache do vSAN NVMe (TB, bruto)  |  tipo de capacidade vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  dual Intel 18 Core de 2,3 GHz  |     576      |                3.2               |                15,20               |

Os hosts usados para criar ou dimensionar clusters vêm de um pool isolado de hosts. Esses hosts passaram por testes de hardware e tiveram todos os dados excluídos com segurança. 

## <a name="vmware-software-versions"></a>Versões de software VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>Frequência de atualização

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>Manutenção do host e gerenciamento do ciclo de vida

A manutenção do host e o gerenciamento do ciclo de vida não afetam a capacidade ou o desempenho dos clusters de nuvem privada.  Exemplos de manutenção automatizada do host incluem atualizações de firmware e reparo ou substituição de hardware.

A Microsoft é responsável pelo gerenciamento do ciclo de vida de dispositivos NSX-T, como NSX-T Manager e NSX-T Edge. A Microsoft é responsável por inicializar a configuração de rede, como criar o gateway de camada 0 e habilitar o roteamento de North-South. Você é responsável pela configuração de SDN do NSX-T. Por exemplo, segmentos de rede, regras de firewall distribuídas, gateways de camada 1 e balanceadores de carga.

## <a name="backup-and-restoration"></a>Backup e restauração

As configurações do vCenter e do NSX-T na nuvem privada estão em uma agenda de backup por hora.  Os backups são mantidos por três dias. Se você precisar restaurar de um backup, abra uma [solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support) no portal do Azure para solicitar a restauração.

## <a name="next-steps"></a>Próximas etapas

Agora que você abordou os conceitos da nuvem privada da solução Azure VMware, talvez queira saber mais sobre: 

- [Conceitos de rede e interconectividade da solução do Azure VMware](concepts-networking.md).
- [Conceitos de armazenamento da solução VMware do Azure](concepts-storage.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

