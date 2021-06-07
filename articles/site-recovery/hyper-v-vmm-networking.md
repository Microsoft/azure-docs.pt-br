---
title: Configurar o endereçamento IP após o failover para um site secundário com Azure Site Recovery
description: Descreve como configurar o endereçamento de IP para conectar-se a VMs em um site local secundário após recuperação de desastre e failover com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 43942c20a353ff69383f3e721679e4c95ab9d230
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495936"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Configurar o endereçamento IP para conectar a um site local secundário após failover

Depois de executar o failover de máquinas virtuais do Hyper-V nas nuvens do System Center Virtual Machine Manager (VMM) para um site secundário, você precisará ser capaz de se conectar às VMs replicadas. Este artigo ajuda você a fazer isso. 

## <a name="connection-options"></a>Opções de conexão

Após o failover, há algumas maneiras de lidar com o endereçamento IP para máquinas virtuais replicadas: 

- **Manter o mesmo endereço IP após o failover**: neste cenário, a VM replicada tem o mesmo endereço IP da VM primária. Isso simplifica problemas relacionados à rede após o failover, mas requer algum trabalho de infraestrutura.
- **Usar um endereço IP diferente após failover**: nesse cenário, a VM obtém um novo endereço IP após o failover. 
 

## <a name="retain-the-ip-address"></a>Manter o endereço IP

Se você deseja manter os endereços IP do site primário, após o failover para o site secundário, você pode:

- Implantar uma sub-rede ampliada entre os sites primário e secundário.
- Executar um failover de sub-rede completo do site principal para o site secundário. Você precisa atualizar as rotas para indicar o novo local dos endereços IP.


### <a name="deploy-a-stretched-subnet"></a>Implantar uma sub-rede ampliada

Em uma configuração ampliada, a sub-rede fica disponível simultaneamente nos sites primário e secundário. Em uma sub-rede ampliada, quando você move uma máquina e sua configuração de endereço IP (camada 3) para o site secundário, a rede automaticamente roteia o tráfego para o novo local. 

- Do ponto de vista da Camada 2 (camada de link de dados), você precisa de equipamento de rede que possa gerenciar uma VLAN ampliada.
- Ao ampliar a VLAN, o domínio de falha potencial se estende para ambos os sites. Isso se torna um ponto único de falha. Embora seja improvável, nesse cenário você pode não ser capaz de isolar um incidente, como uma tempestade de transmissão. 


### <a name="fail-over-a-subnet"></a>Failover de uma sub-rede

Você pode executar um failover de toda a sub-rede para obter as vantagens da sub-rede ampliada, sem realmente ampliá-la. Nesta solução, uma sub-rede está disponível no site de origem ou destino, mas não em ambos ao mesmo tempo.

- Para manter o espaço de endereço IP no caso de um failover, você pode organizar por meio de programação para a infra-estrutura do roteador mover sub-redes de um site para outro.
- Quando ocorre um failover, as sub-redes se movem com as VMs associadas.
- A principal desvantagem desa abordagem é que, em caso de falha, você precisa mover toda a sub-rede.

#### <a name="example"></a>Exemplo

Aqui está um exemplo de failover de sub-rede completo. 

- Antes do failover, o site principal tem aplicativos em execução na sub-rede 192.168.1.0/24.
- Durante o failover, todas as VMs nessa sub-rede fazem failover para o site secundário e mantêm seus endereços IP. 
- As rotas entre todos os sites precisam ser modificadas para refletir o fato de que todas as máquinas virtuais VM na sub-rede 192.168.1.0/24 agora foram movidas para o site secundário.

Os gráficos a seguir ilustram as sub-redes antes e após o failover.


**Antes do failover**

![Diagrama mostrando as sub-redes antes do failover.](./media/hyper-v-vmm-networking/network-design2.png)

**Depois do failover**

![Diagrama mostrando as sub-redes após o failover.](./media/hyper-v-vmm-networking/network-design3.png)

Após o failover, o Site Recovery aloca um endereço IP para cada interface de rede na máquina virtual. O endereço é alocado do pool de endereços IP estáticos na rede relevante, para cada instância de VM.

- Se o pool de endereços IP no site secundário é o mesmo que no site de origem, o Site Recovery aloca o mesmo endereço IP (da VM de origem) para a VM de réplica. O endereço IP está reservado no VMM, mas não está definido como o endereço IP de failover no host Hyper-V. O endereço IP de failover em um host do Hyper-V é definido logo antes do failover.
- Se o mesmo endereço IP não estiver disponível, o Site Recovery alocará outro endereço IP do pool.
- Se as VMs usam DHCP, o Site Recovery não gerencia os endereços IP. Você precisa verificar se o servidor DHCP no site secundário pode alocar endereços do mesmo intervalo que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de habilitar a proteção para uma VM, você pode usar o script de exemplo a seguir para verificar o endereço atribuído à VM. Esse endereço IP é definido como o endereço IP de failover e atribuído à VM no momento do failover:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Usar um endereço IP diferente

Neste cenário, os endereços IP das VMs que fazem failover são alterados. A desvantagem dessa solução é a manutenção necessária.  Entradas de DNS e cache talvez precisem ser atualizadas. Isso pode resultar em tempo de inatividade, o que pode ser atenuado da seguinte maneira:

- Use valores TTL baixos para aplicativos de intranet.
- Use o script a seguir em um plano de recuperação do Site Recovery para atualizar o servidor DNS em tempo hábil. O script não será necessário se você usar o registro DNS dinâmico.

    ```powershell
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exemplo 

Neste exemplo, temos endereços IP diferentes em sites primários e secundários, e existe um terceiro site de onde os aplicativos hospedados no site primário ou de recuperação podem ser acessados.

- Antes do failover, os aplicativos são hospedados na sub-rede 192.168.1.0/24 no site primário.
- Após o failover, os aplicativos são configurados na sub-rede 172.16.1.0/24 no site secundário.
- Todos os três sites podem acessar uns aos outros.
- Após o failover, os aplicativos serão restaurados na sub-rede de recuperação.
- Nesse cenário, não é necessário fazer o failover da sub-rede inteira, e nenhuma alteração é necessária para reconfigurar as rotas de rede ou VPN. O failover e algumas atualizações DNS manterão os aplicativos acessíveis.
- Se o DNS está configurado para permitir atualizações dinâmicas, as VMs farão o registro por conta própria usando o novo endereço IP na inicialização depois do failover.

**Antes do failover**

![Diagrama mostrando endereços IP diferentes antes do failover.](./media/hyper-v-vmm-networking/network-design10.png)

**Depois do failover**

![Diagrama mostrando endereços IP diferentes após o failover.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Próximas etapas

[Executar um failover](hyper-v-vmm-failover-failback.md)

