---
title: Solução do Azure VMware por CloudSimple-migrar VMs de carga de trabalho para nuvem privada
description: Descreve como migrar máquinas virtuais do vCenter local para o vCenter de nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899313"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrar VMs de carga de trabalho do vCenter local para o ambiente de vCenter de nuvem privada

Para migrar VMs de um datacenter local para sua nuvem privada do CloudSimple, várias opções estão disponíveis.  A nuvem privada fornece acesso nativo ao VMware vCenter, e as ferramentas com suporte do VMware podem ser usadas para migração de carga de trabalho. Este artigo descreve algumas das opções de migração do vCenter.

## <a name="prerequisites"></a>Pré-requisitos

A migração de VMs e dados do seu datacenter local requer conectividade de rede do datacenter para seu ambiente de nuvem privada.  Use um dos seguintes métodos para estabelecer a conectividade de rede:

* [Conexão VPN site a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre seu ambiente local e sua nuvem privada.
* ExpressRoute Alcance Global conexão entre o circuito do ExpressRoute local e um circuito do ExpressRoute CloudSimple.

O caminho de rede do ambiente do vCenter local para sua nuvem privada deve estar disponível para migração de VMs usando o vMotion.  A rede do vMotion no seu vCenter local deve ter capacidades de roteamento.  Verifique se o firewall permite todo o tráfego de vMotion entre o vCenter local e o vCenter de nuvem privada. (Na nuvem privada, o roteamento na rede do vMotion é configurado por padrão.)

## <a name="migrate-isos-and-templates"></a>Migrar ISOs e modelos

Para criar novas máquinas virtuais em sua nuvem privada, use ISOs e modelos de VM.  Para carregar o ISOs e os modelos em seu vCenter de nuvem privada e torná-los disponíveis, use o método a seguir.

1. Carregue o ISO para a nuvem privada vCenter da interface do usuário do vCenter.
2. [Publicar uma biblioteca de conteúdo](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) em seu vCenter de nuvem privada:

    1. Publique sua biblioteca de conteúdo local.
    2. Crie uma nova biblioteca de conteúdo no vCenter de nuvem privada.
    3. Assine a biblioteca de conteúdo local publicada.
    4. Sincronize a biblioteca de conteúdo para acesso ao conteúdo assinado.

## <a name="migrate-vms-using-powercli"></a>Migrar VMs usando o PowerCLI

Para migrar VMs do vCenter local para o vCenter de nuvem privada, use o VMware PowerCLI ou o utilitário de migração de carga de trabalho cruzada do vCenter disponível no VMware Labs.  O script de exemplo a seguir mostra os comandos de migração PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Para usar os nomes do servidor vCenter de destino e dos hosts ESXi, configure o encaminhamento de DNS do local para sua nuvem privada.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrar VMs usando a VPN da camada 2 do NSX

Essa opção permite a migração dinâmica de cargas de trabalho do ambiente VMware local para a nuvem privada no Azure.  Com essa rede de camada 2 ampliada, a sub-rede do local estará disponível na nuvem privada.  Após a migração, a nova atribuição de endereço IP não é necessária para as VMs.

[Migrar cargas de trabalho usando redes ampliadas de camada 2](migration-layer-2-vpn.md) descreve como usar uma VPN de camada 2 para ampliar uma rede de camada 2 do seu ambiente local para sua nuvem privada.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrar VMs usando ferramentas de backup e recuperação de desastre

A migração de VMs para a nuvem privada pode ser feita usando ferramentas de backup/restauração e ferramentas de recuperação de desastre.  Use a nuvem privada como um destino para restauração de backups criados com uma ferramenta de terceiros.  A nuvem privada também pode ser usada como um destino para a recuperação de desastre usando o VMware SRM ou uma ferramenta de terceiros.

Para obter mais informações sobre como usar essas ferramentas, consulte os seguintes tópicos:

* [Fazer backup de máquinas virtuais de carga de trabalho na nuvem privada do CloudSimple usando o Veeam B&R](backup-workloads-veeam.md)
* [Configurar a nuvem privada do CloudSimple como um site de recuperação de desastre para cargas de trabalho do VMware locais](disaster-recovery-zerto.md)
