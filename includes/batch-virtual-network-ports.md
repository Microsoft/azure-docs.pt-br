---
title: incluir arquivo
description: incluir arquivo
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 02/16/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: baf146bdd89d45c5d7e1ed359822a35d383b7b6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561900"
---
### <a name="general-requirements"></a>Requisitos gerais

* A VNet deve estar na mesma assinatura e região da conta do Lote que você usa para criar o pool.

* A sub-rede especificada para o pool deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o pool, ou seja, a soma das propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do pool. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o pool alocará parcialmente os nós de computação e ocorrerá um erro de redimensionamento.

* O ponto de extremidade do Armazenamento do Azure precisa ser resolvido por servidores DNS personalizados que atendem sua rede VNet. Especificamente, as URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net` devem poder ser resolvidas.

* Vários pools podem ser criados na mesma VNet ou na mesma sub-rede (desde que ela tenha espaço de endereço suficiente). Um pool não pode existir em várias VNets ou sub-redes.

Os requisitos da VNet adicionais diferem, dependendo do pool do Lote estar na configuração da Máquina Virtual ou na configuração dos Serviços de Nuvem. Para as novas implantações de pool em uma VNet, recomenda-se a configuração da Máquina Virtual.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools na configuração da Máquina Virtual

**VNets com Suporte** - VNets baseadas no Azure Resource Manager apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Permissões** - verifique se suas políticas de segurança ou bloqueios no grupo de recursos ou na assinatura da VNet restringem as permissões de um usuário para gerenciar a VNet.

**Recursos de rede adicionais** – o Lote cria automaticamente os recursos de rede adicionais no grupo de recursos que contém a VNet.

> [!IMPORTANT]
> Para cada 100 nós dedicados ou de baixa prioridade, o Lote cria: um NSG (grupo de segurança de rede), um endereço IP público e um balanceador de carga. Esses recursos são limitados pelas [cotas de recursos](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) da assinatura. Para pools grandes, talvez você precise solicitar um aumento de cota para um ou mais desses recursos.

#### <a name="network-security-groups-batch-default"></a>Grupos de segurança de rede: Padrão do Lote

A sub-rede deve permitir a comunicação de entrada do serviço do Lote para conseguir agendar tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou com outros recursos, conforme a necessidade da carga de trabalho. Para pools na configuração da Máquina Virtual, o Lote adiciona NSGs no nível das NICs (interfaces de rede) anexadas ao nós de computação. Esses NSGs são configurados com as seguintes regras adicionais:

* Tráfego TCP de entrada nas portas 29876 e 29877 dos endereços IP do serviço de Lote que correspondem à marca de serviço `BatchNodeManagement`.
* Tráfego TCP de entrada na porta 22 (nós do Linux) ou na porta 3389 (nós do Windows) para permitir o acesso remoto. Para determinados tipos de tarefas de várias instâncias no Linux (como a MPI), você também precisará permitir o tráfego na porta SSH 22 para IPs na sub-rede que contém os nós de computação do Lote. Isso pode ser bloqueado pelas regras de NSG no nível da sub-rede (veja abaixo).
* Tráfego de saída em qualquer porta para a rede virtual. Isso pode ser corrigido pelas regras de NSG no nível da sub-rede (veja abaixo).
* Tráfego de saída em qualquer porta para a Internet. Isso pode ser corrigido pelas regras de NSG no nível da sub-rede (veja abaixo).

> [!IMPORTANT]
> Tenha cuidado se você modificar ou adicionar regras de entrada ou de saída nos NSGs configurados pelo Lote. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço do Lote definirá o estado dos nós de computação como **inutilizável**. Além disso, nenhum bloqueio de recurso deve ser aplicado a recursos criados pelo Lote, uma vez que isso pode impedir a limpeza de recursos como resultado de ações iniciadas pelo usuário, como a exclusão de um pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Grupos de segurança de rede: Especificar regras no nível da sub-rede

Não é necessário especificar NSGs no nível da sub-rede da rede virtual, pois o Lote configura os próprios NSGs (veja acima). Se tiver um NSG associado à sub-rede em que os nós de computação do Lote estão implantados ou se quiser aplicar regras de NSG personalizadas para substituir os padrões aplicados, você precisará configurar esse NSG com, pelo menos, as regras de segurança de entrada e saída, conforme mostrado nas tabelas a seguir.

Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) somente se precisar permitir o acesso remoto aos nós de computação proveniente de fontes externas. Talvez seja necessário habilitar regras da porta 22 no Linux se você precisar de suporte para tarefas de várias instâncias com determinados runtimes do MPI. Permitir o tráfego nessas portas não é estritamente necessário para que os nós de computação do pool sejam utilizáveis.

> [!WARNING]
> Os endereços IP do serviço de lote podem mudar ao longo do tempo. Portanto, é altamente recomendável usar a marca de serviço `BatchNodeManagement` (ou uma variante regional) para as regras de NSG indicadas nas tabelas a seguir. Evite popular as regras de NSG com endereços IP específicos do serviço do Lote.

**Regras de segurança de entrada**

| Endereços IP da fonte | Marca de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | [Marca de serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) `BatchNodeManagement` (se estiver usando uma variante regional na mesma região em que sua conta do Lote) | * | Qualquer | 29876-29877 | TCP | Allow |
| IPs de origem do usuário para acessar remotamente nós de computação e/ou a sub-rede do nó de computação para tarefas de várias instâncias de Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Allow |

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Marca de serviço de destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| Qualquer | * | [Marca do serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (se estiver usando a variante regional na mesma região em que sua conta do Lote) | 443 | TCP | Allow |
| Qualquer | * | [Marca do serviço](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (se estiver usando a variante regional na mesma região em que sua conta do Lote) | 443 | TCP | Allow |

A saída para `BatchNodeManagement` é necessária para contatar o serviço de Lote dos nós de computação, como para tarefas do Gerenciador de Trabalho.

### <a name="pools-in-the-cloud-services-configuration"></a>Pools na configuração dos Serviços de Nuvem

> [!WARNING]
> Os pools de configuração do serviço de nuvem foram preteridos. Em vez disso, use pools de configuração de máquina virtual.

**VNets com Suporte** - VNets clássicas apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Permissões** – a entidade de serviço `Microsoft Azure Batch` deve ter a função do Azure `Classic Virtual Machine Contributor` para a VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos.

Você não precisa especificar um NSG, porque o Lote configura comunicação de entrada apenas dos endereços IP de Lote para os nós do pool. No entanto, se a sub-rede especificada associou NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço do Lote definirá o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para o Windows se precisar permitir o acesso RDP aos nós do pool. Isso não é necessário para que os nós do pool possam ser usados.

**Regras de segurança de entrada**

| Endereços IP da fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Embora isso exija efetivamente "permitir todos", o serviço do Lote aplica uma regra ACL no nível de cada nó que filtra todos os endereços IP que não são do serviço do Lote. | * | Qualquer | 10100, 20100, 30100 | TCP | Allow |
| Opcional, para permitir o acesso RDP aos nós de computação. | * | Qualquer | 3389 | TCP | Allow |

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Allow |
