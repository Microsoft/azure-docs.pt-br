---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/16/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: c8b25858556538835d6a84bf0d6699f9906f1438
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322645"
---
### <a name="general-requirements"></a>Requisitos gerais

* A VNet deve estar na mesma assinatura e região da conta do Lote que você usa para criar o pool.

* O pool usando a VNet pode ter um máximo de 4.096 nós.

* A sub-rede especificada para o pool deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o pool, ou seja, a soma das propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do pool. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o pool alocará parcialmente os nós de computação e ocorrerá um erro de redimensionamento. 

* O ponto de extremidade do Armazenamento do Azure precisa ser resolvido por servidores DNS personalizados que atendem sua rede VNet. Especificamente, as URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net` devem poder ser resolvidas. 

Os requisitos da VNet adicionais diferem, dependendo do pool do Lote estar na configuração da Máquina Virtual ou na configuração dos Serviços de Nuvem. Para as novas implantações de pool em uma VNet, recomenda-se a configuração da Máquina Virtual.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools na configuração da Máquina Virtual

**VNets com Suporte** - VNets baseadas no Azure Resource Manager apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - verifique se suas políticas de segurança ou bloqueios no grupo de recursos ou na assinatura da VNet restringem as permissões de um usuário para gerenciar a VNet.

**Recursos de rede adicionais** - o lote aloca automaticamente os recursos de rede adicionais no grupo de recursos que contém a VNet. Para cada um dos nós dedicados 50 (ou cada um dos 20 nós de baixa prioridade), o lote é alocado: 1 grupo de segurança de rede (NSG), 1 endereço IP público e 1 balanceador de carga. Esses recursos são limitados pelas [cotas de recursos](../articles/azure-subscription-service-limits.md) da assinatura. Para os pools grandes, você precisará solicitar um aumento de cota para um ou mais recursos.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos. Para os pools na configuração da Máquina Virtual, o Lote adiciona NSGs no nível das interfaces de rede (NICs) anexadas às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

* Tráfego TCP de entrada nas portas 29876 e 29877 a partir dos endereços IP com função de serviço de Lote. 
* Tráfego TCP de entrada na porta 22 (nós do Linux) ou na porta 3389 (nós do Windows) para permitir o acesso remoto. Para determinados tipos de tarefas de várias instâncias no Linux (como MPI), você também precisará permitir o tráfego da porta do SSH 22 para IPs na sub-rede que contém os nós de computação do lote.
* Tráfego de saída em qualquer porta para a rede virtual.
* Tráfego de saída em qualquer porta para a internet.

> [!IMPORTANT]
> Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**.

Você não precisa especificar os NSGs no nível da sub-rede porque o Lote configura seus próprios NSGs. No entanto, se a sub-rede especificada associou Grupos de Segurança de Rede (NSGs) e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) somente se você precisar permitir o acesso remoto às VMs do pool de fontes externas. Não é necessário para o pool de VMs ser usado. Observe que você precisará habilitar o tráfego de sub-rede da rede virtual na porta 22 para Linux se estiver usando determinados tipos de tarefas de várias instâncias, como MPI.

**Regras de segurança de entrada**

| Endereços IP da fonte | Marca de serviço de origem | Portas de origem | Destino | Portas de destino | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[Marca de serviço](../articles/virtual-network/security-overview.md#service-tags) | * | Any | 29876-29877 | TCP | Allow |
| IPs de origem do usuário para acessar remotamente nós de computação e/ou sub-rede do nó de computação para tarefas de várias instâncias do Linux, se necessário. | N/D | * | Any | 3389 (Windows), 22 (Linux) | TCP | Allow |

**Regras de segurança da saída**

| Origem | Portas de origem | Destino | Marca de serviço de destino | Portas de destino | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [Marca do serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(na mesma região que sua conta do lote e VNet) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools na configuração dos Serviços de Nuvem

**VNets com Suporte** - VNets clássicas apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - a `Microsoft Azure Batch` entidade de serviço deve ter a `Classic Virtual Machine Contributor` função RBAC (Controle de Acesso Baseado em Função) para a VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos.

Você não precisa especificar um NSG, porque o Lote configura comunicação de entrada apenas dos endereços IP de Lote para os nós do pool. No entanto, se a sub-rede especificada associou NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se você precisar permitir o acesso RDP aos nós do pool. Não é necessário para os nós do pool serem usados.

**Regras de segurança de entrada**

| Endereços IP da fonte | Portas de origem | Destino | Portas de destino | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />Embora isso exija efetivamente "permitir todos", o serviço do Lote aplica uma regra ACL no nível de cada nó que filtra todos os endereços IP que não são do serviço do Lote. | * | Any | 10100, 20100, 30100 | TCP | Allow |
| Opcional, para permitir o acesso RDP a nós de computação. | * | Any | 3389 | TCP | Allow |

**Regras de segurança da saída**

| Origem | Portas de origem | Destino | Portas de destino | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | Allow |
