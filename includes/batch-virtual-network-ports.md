---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550168"
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

**Recursos de rede adicionais** - o lote aloca automaticamente os recursos de rede adicionais no grupo de recursos que contém a VNet. Para cada 50 nós dedicados (ou cada 20 nós de baixa prioridade), o lote aloca: Balanceador de carga 1, 1 endereço IP público e o grupo de segurança de rede de 1 (NSG). Esses recursos são limitados pelas [cotas de recursos](../articles/azure-subscription-service-limits.md) da assinatura. Para os pools grandes, você precisará solicitar um aumento de cota para um ou mais recursos.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos. Para os pools na configuração da Máquina Virtual, o Lote adiciona NSGs no nível das interfaces de rede (NICs) anexadas às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

* Tráfego TCP de entrada nas portas 29876 e 29877 a partir dos endereços IP com função de serviço de Lote. 
* Tráfego TCP de entrada na porta 22 (nós do Linux) ou na porta 3389 (nós do Windows) para permitir o acesso remoto. Para determinados tipos de tarefas de várias instâncias no Linux (como MPI), você precisará também permita o tráfego da porta 22 SSH para IPs na sub-rede que contém os nós de computação do lote.
* Tráfego de saída em qualquer porta para a rede virtual.
* Tráfego de saída em qualquer porta para a internet.

> [!IMPORTANT]
> Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**.

Você não precisa especificar os NSGs no nível da sub-rede porque o Lote configura seus próprios NSGs. No entanto, se a sub-rede especificada associou Grupos de Segurança de Rede (NSGs) e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) somente se você precisar permitir o acesso remoto para as VMs do pool de fontes externas. Não é necessário para o pool de VMs ser usado. Observe que você precisará habilitar o tráfego de sub-rede da rede virtual na porta 22 para Linux usando certos tipos de tarefas de várias instâncias, como MPI.

**Regras de segurança de entrada**

| Endereços IP da fonte | Marca de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement` [Marca de serviço](../articles/virtual-network/security-overview.md#service-tags) | * | Qualquer | 29876-29877 | TCP | PERMITIR |
| Usuário IPs de origem para acessar remotamente os nós de computação e/ou sub-rede do nó de computação para tarefas de várias instâncias do Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | PERMITIR |

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Marca de serviço de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | 443 | [Marca do serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (na mesma região que sua conta do lote e a rede virtual)  | Qualquer | PERMITIR |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools na configuração dos Serviços de Nuvem

**VNets com Suporte** - VNets clássicas apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - a `MicrosoftAzureBatch` entidade de serviço deve ter a `Classic Virtual Machine Contributor` função RBAC (Controle de Acesso Baseado em Função) para a VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos.

Você não precisa especificar um NSG, porque o Lote configura comunicação de entrada apenas dos endereços IP de Lote para os nós do pool. No entanto, se a sub-rede especificada associou NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se você precisar permitir acesso RDP para nós do pool. Não é necessário para os nós do pool serem usados.

**Regras de segurança de entrada**

| Endereços IP da fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Embora isso exija efetivamente "permitir todos", o serviço do Lote aplica uma regra ACL no nível de cada nó que filtra todos os endereços IP que não são do serviço do Lote. | * | Qualquer | 10100, 20100, 30100 | TCP | PERMITIR |
| Opcional, para permitir o acesso RDP em nós de computação. | * | Qualquer | 3389 | TCP | PERMITIR |

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | PERMITIR |
