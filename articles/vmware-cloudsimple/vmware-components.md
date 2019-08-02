---
title: Solução de VMware do Azure por CloudSimple - componentes de VMware da nuvem privada
description: Descreve como os componentes de VMware são instalados na nuvem privada
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 62511118edb4f8b5061f90138bac2aa2b5d3cfe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165147"
---
# <a name="private-cloud-vmware-components"></a>Componentes de VMware da nuvem privada

Uma nuvem privada é uma pilha de VMware isolada (hosts ESXi, vCenter, vSAN e NSX) ambiente gerenciado por um servidor do vCenter em um domínio de gerenciamento.  O serviço CloudSimple permite que você implante VMware nativamente na infraestrutura bare metal do Azure em locais do Azure.  Nuvens privadas são integradas com o restante da nuvem do Azure.  Uma nuvem privada é implantada com os seguintes componentes de pilha do VMware:

* **VMware ESXi -** nós dedicados do hipervisor no Azure
* **VMware vCenter -** dispositivo para gerenciamento centralizado de ambiente do vSphere de nuvem privada
* **VMware vSAN -** solução de infraestrutura hiperconvergente
* **Centro de dados do VMware NSX -** virtualização e o Software de segurança de rede  

## <a name="vmware-component-versions"></a>Versões de componente do VMware

Uma pilha de nuvem privada do VMware é implantada com a seguinte versão de software.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | Avançado |

## <a name="esxi"></a>ESXi

VMware ESXi está instalado em nós de CloudSimple provisionados quando você cria uma nuvem privada.  ESXi fornece o hipervisor para implantar a carga de trabalho VMs (máquinas virtuais).  Nós fornecem infraestrutura hiperconvergida (computação e armazenamento) em sua nuvem privada.  Os nós são uma parte do cluster vSphere na nuvem privada.  Cada nó tem quatro interfaces de redes físicas conectadas à rede de base.  Duas interfaces de rede física são usados para criar uma **vSphere Distributed Switch (VDS)** no vCenter e duas são usados para criar um **NSX gerenciado distribuído comutador virtual (VDS N)** .  Interfaces de rede são configurados no modo ativo-ativo para alta disponibilidade.

Saiba mais sobre o VMware ESXi

## <a name="vcenter-server-appliance"></a>dispositivo de servidor do vCenter

dispositivo de servidor do vCenter (VCSA) fornece as funções de autenticação, gerenciamento e orquestração para solução CloudSimple do VMware. VCSA com embedded plataforma serviços de controlador (PSC) é implantado quando você cria sua nuvem privada.  VCSA é implantado no cluster do vSphere é criado quando você implanta sua nuvem privada.  Cada nuvem privada tem seu próprio VCSA.  Expansão de uma nuvem privada adiciona nós para o VCSA na nuvem privada.

### <a name="vcenter-single-sign-on"></a>logon único vCenter

Controlador de serviços de plataforma incorporado em VCSA está associado com um **domínio de logon único do vCenter**.  É o nome de domínio **cloudsimple.local**.  Um usuário padrão **CloudOwner@cloudsimple.com** é criada para você acessar o vCenter.  Você pode adicionar seu local/Azure active directory [fontes de identidade para o vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>armazenamento vSAN

Nuvens privadas são criadas com o armazenamento vSAN de todos os flash totalmente configurado, local para o cluster.  Mínimo três nós da mesma SKU são necessários para criar um cluster do vSphere com o repositório de dados vSAN.  Eliminação de duplicação e compactação estão habilitados no repositório de dados vSAN, por padrão.  Dois grupos de discos são criados em cada nó do cluster do vSphere. Cada grupo de disco contém um disco de cache e três discos de capacidade.

Uma política de armazenamento vSAN padrão é criada no cluster do vSphere e aplicada para o armazenamento vSAN.  Essa política determina como os objetos de armazenamento VM são provisionados e alocados no repositório de dados para garantir o nível de serviço necessário.  A política de armazenamento define a **falhas a tolerar (FTT)** e o **método de tolerância a falha**.  Você pode criar novas políticas de armazenamento e aplicá-los às VMs. Para manter o SLA, 25% da capacidade reserva deve ser mantida no repositório de dados vSAN.  

### <a name="default-vsan-storage-policy"></a>Política de armazenamento vSAN padrão

Tabela a seguir mostra o padrão de parâmetros de política de armazenamento vSAN.

| Número de nós no Cluster do vSphere | FTT | Método de tolerância a falhas |
|------------------------------------|-----|--------------------------|
| 3 e 4 nós | 1 | RAID 1 (espelhamento) - cria 2 cópias |
| 5 a 16 nós | 2 | RAID 1 (espelhamento) - cria 3 cópias |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center fornece virtualização de rede, segmentação micro e recursos de segurança de rede em sua nuvem privada.  Você pode configurar todos os serviços com suporte pelo NSX Data Center na nuvem privada por meio de NSX.  Quando você cria uma nuvem privada, os seguintes componentes NSX estiverem instalados e configurados.

* Gerenciador de NSXT
* Zonas de transporte
* Host e o perfil de Uplink de borda
* Comutador lógico para o transporte de borda, Ext1 e Ext2
* Pool de IP para o nó de transporte do ESXi
* Pool de IP para o nó de transporte de borda
* Nós de borda
* Regra de affinity DRS para VMs de borda e controlador
* Roteador de camada 0
* Habilitar o BGP no roteador Tier0

## <a name="vsphere-cluster"></a>cluster do vSphere

Hosts ESXi são configurados como um cluster para garantir a alta disponibilidade da nuvem privada.  Quando você cria uma nuvem privada, os componentes de gerenciamento do vSphere são implantados no primeiro cluster.  Um pool de recursos é criado para componentes de gerenciamento e todas as VMs de gerenciamento são implantadas nesse pool de recursos. O primeiro cluster não pode ser excluído para reduzir a nuvem privada.  cluster do vSphere fornece alta disponibilidade para VMs usando o **vSphere HA**.  Falhas a tolerar baseiam-se no número de nós disponíveis no cluster.  Você pode usar a fórmula ```Number of nodes = 2N+1``` onde ```N``` é o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster do vSphere

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada (primeiro cluster do vSphere) | 3 |
| Número máximo de nós em um vSphere do Cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número máximo de vSphere Clusters em uma nuvem privada | 21 |
| Número mínimo de nós em um Cluster do vSphere novo | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção de infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infraestrutura do VMware. Atualmente, esses intervalos podem ocorrer a cada 1 e 2 meses, mas espera-se que a frequência recusar ao longo do tempo. Esse tipo de manutenção geralmente pode ser feito sem interromper o consumo dos serviços CloudSimple normal. Durante um intervalo de manutenção do VMware, os serviços a seguir continuam a funcionar sem qualquer impacto:

* Aplicativos e o plano de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e Upgrades

CloudSimple é responsável pelo gerenciamento de ciclo de vida do software de VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

Atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs, lançadas pela VMware.
* **Atualizações**. Alteração de versão secundária de um componente de pilha do VMware.
* **Atualizações**. Alteração de versão principal de um componente de pilha do VMware.

CloudSimple testa um patch de segurança crítica, assim que ele se torna disponível do VMware. Por SLA, CloudSimple lança o patch de segurança para ambientes de nuvem privada dentro de uma semana.

CloudSimple fornece atualizações de manutenção trimestrais para componentes de software do VMware. Quando uma nova versão principal do software VMware está disponível, CloudSimple trabalha com clientes para coordenar uma janela de manutenção adequado para a atualização.  

## <a name="next-steps"></a>Próximas etapas

* [Atualizações e manutenção de CloudSimple](cloudsimple-maintenance-updates.md)