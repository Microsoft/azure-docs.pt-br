---
title: Failover de teste (VMM para VMM) no Azure Site Recovery | Microsoft Docs
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: pratshar
ms.openlocfilehash: afc4790d5714ce7145c8f4291a05acc2e9882a9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Failover de teste (VMM para VMM) no Site Recovery


Este artigo fornece informações e instruções sobre como fazer um failover de teste ou uma simulação de DR (recuperação de desastre) de VMs (máquinas virtuais) e servidores físicos protegidos com o Azure Site Recovery. Você usará um site local gerenciado pelo System Center Virtual Machine Manager (VMM) como o site de recuperação.

Execute um failover de teste para validar sua estratégia de replicação ou realize uma simulação de DR sem nenhuma perda de dados ou tempo de inatividade. Um failover de teste não afeta a replicação em andamento nem o ambiente de produção. Execute-o em uma máquina virtual ou um [plano de recuperação](site-recovery-create-recovery-plans.md). Quando estiver disparando um failover de teste, você precisa especificar a rede à qual as máquinas virtuais de teste se conectarão. Acompanhe o progresso do failover de teste na página **Trabalhos**.  

Em caso de comentários ou perguntas, poste-os ao final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>Preparar a infraestrutura para o failover de teste
Se desejar executar um failover de teste usando uma rede existente, prepare o Active Directory, o DHCP e o DNS nessa rede.

Se desejar executar um failover de teste usando a opção para criar redes VM automaticamente, adicione uma etapa manual antes do Grupo 1 no plano de recuperação que você pretende usar para o failover de teste. Em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover de teste.

### <a name="things-to-note"></a>Elementos a serem observados
Quando estiver replicando para um site secundário, o tipo de rede usado pelo computador de réplica não precisa corresponder ao tipo de rede lógica usado para o failover de teste, mas algumas combinações podem não funcionar. Quando a réplica usa isolamento baseado em VLAN e DHCP, a rede VM da réplica não precisa de um pool de endereços IP estáticos. Portanto, o uso da Virtualização de Rede do Windows para o failover de teste não funcionará porque nenhum pool de endereços está disponível. 

Além disso, o failover de teste não funcionará se a rede de réplica não usar nenhum isolamento e a rede de teste usar a Virtualização de Rede do Windows. Isso ocorre porque a rede sem isolamento não tem as sub-redes necessárias para criar uma rede da Virtualização de Rede do Windows.

O modo como as máquinas virtuais de réplica são conectadas às redes VM mapeadas após o failover depende de como a rede VM é configurada no console do VMM.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou com isolamento de VLAN
Se o DHCP for definido para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações especificadas para o site da rede na rede lógica associada. A máquina virtual recebe seu endereço IP do servidor DHCP disponível. 

Não é necessário definir um pool de endereços IP estáticos para a rede VM de destino. Se um pool de endereços IP estáticos for usado para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN por meio das configurações especificadas para o site da rede na rede lógica associada.

A máquina virtual receberá seu endereço IP do pool definido para a rede VM. Se um pool de endereços IP estáticos não for definido na rede VM de destino, a alocação do endereço IP falhará. Crie o pool de endereços IP nos servidores VMM de origem e destino que você usará para proteção e recuperação.

#### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com Virtualização de Rede do Windows
Se uma rede VM for configurada com a Virtualização de Rede do Windows, você deverá definir um pool estático para a rede VM de destino, independentemente de a rede VM de origem estar configurada para usar o DHCP ou um pool de endereços IP estáticos. 

Se você definir o DHCP, o servidor VMM de destino atuará como um servidor DHCP e fornecerá um endereço IP do pool definido para a rede VM de destino. Se o uso de um pool de endereços IP estáticos for definido para o servidor de origem, o servidor VMM de destino alocará um endereço IP do pool. Em ambos os casos, a alocação de endereço IP falhará se um pool de endereços IP estáticos não for definido.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvidas no failover de teste usarem o DHCP, crie um servidor DHCP de teste na rede isolada que é criada para fins de failover de teste.

### <a name="prepare-active-directory"></a>Preparar o Active Directory
Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Para obter mais informações, examine as [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparar o DNS
Prepare um servidor DNS para o failover de teste da seguinte forma:

* **DHCP**: se as máquinas virtuais usarem o DHCP, o endereço IP do DNS de teste deverá ser atualizado no servidor DHCP de teste. Quando você usa um tipo de rede da Virtualização de Rede do Windows, o servidor VMM atua como o servidor DHCP. Portanto, o endereço IP do DNS deve ser atualizado na rede de failover de teste. Nesse caso, as máquinas virtuais se registrarão no servidor DNS relevante.
* **Endereço estático**: se as máquinas virtuais usarem um endereço IP estático, o endereço IP do servidor DNS de teste deverá ser atualizado na rede de failover de teste. Talvez você precise atualizar o DNS com o endereço IP das máquinas virtuais de teste. É possível usar o seguinte script de exemplo para este fim:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Execute um teste de failover
Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você pode executar o failover de uma máquina virtual individual na guia **Máquinas Virtuais**.

![Folha Failover de teste](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Test Failover**.
1. Na folha **Failover de Teste** , especifique como as máquinas virtuais devem ser conectadas às redes após o failover de teste. Para obter mais informações, consulte as [opções de rede](#network-options-in-site-recovery).
1. Acompanhe o progresso do failover na guia **Trabalhos** .
1. Depois que o failover for concluído, verifique se as máquinas virtuais são iniciadas com êxito.
1. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. Essa etapa exclui as máquinas virtuais e redes virtuais que foram criadas durante o failover de teste.


## <a name="network-options-in-site-recovery"></a>Opções de rede na recuperação de site

Ao executar um failover de teste, você deverá selecionar as configurações de rede para os computadores de réplica de teste. Você tem várias opções.  

| **Opção de failover de teste** | **Descrição** | **Verificação do failover** | **Detalhes** |
| --- | --- | --- | --- |
| **Fazer failover em um site secundário do VMM – sem rede** |Não selecione uma rede VM. |O failover verifica se os computadores de teste são criados.<br/><br/>A máquina virtual de teste é criada no host em que a máquina virtual de réplica está localizada. Ela não é adicionada à nuvem na qual a máquina virtual de réplica está localizada. |<p>O computador com failover não é conectado a nenhuma rede.<br/><br/>O computador poderá ser conectado a uma rede VM depois que ela for criada. |
| **Fazer failover em um site secundário do VMM – com rede** |Selecione uma rede VM existente. |O failover verifica se as máquinas virtuais foram criadas. |A máquina virtual de teste é criada no host em que a máquina virtual de réplica está localizada. Ela não é adicionada à nuvem na qual a máquina virtual de réplica está localizada.<br/><br/>Crie uma rede VM isolada da rede de produção.<br/><br/>Se você estiver usando uma rede baseada em VLAN, recomendamos criar uma rede lógica separada (não usada em produção) no VMM para essa finalidade. Essa rede lógica é usada para criar redes VM para failovers de teste.<br/><br/>A rede lógica deve estar associada a, pelo menos, um dos adaptadores de rede de todos os servidores do Hyper-V que hospedam máquinas virtuais.<br/><br/>Para redes lógicas VLAN, os sites de rede adicionados à rede lógica devem ser isolados.<br/><br/>Se você está estiver usando uma rede lógica baseada na Virtualização de Rede do Windows, o Azure Site Recovery criará automaticamente redes de VM isoladas. |
| **Fazer failover em um site secundário do VMM – criar uma rede** |Uma rede de teste temporária é criada automaticamente com base na configuração especificada em **Rede Lógica** e seus sites de rede relacionados. |O failover verifica se as máquinas virtuais foram criadas. |Use esta opção se o plano de recuperação usa mais de uma rede VM. Caso esteja usando redes da Virtualização de Rede do Windows, essa opção pode ser usada para criar automaticamente redes VM com as mesmas configurações (sub-redes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são removidas automaticamente depois que o failover de teste é concluído.</p><p>A máquina virtual de teste é criada no host em que a máquina virtual de réplica está localizada. Ela não é adicionada à nuvem na qual a máquina virtual de réplica está localizada. |

> [!TIP]
> O endereço IP fornecido a uma máquina virtual durante o failover de teste é o mesmo endereço IP que a máquina virtual receberia em um failover planejado ou não planejado (supondo que o endereço IP esteja disponível na rede de failover de teste). Se o mesmo endereço IP não estiver disponível na rede de failover de teste, a máquina virtual receberá outro endereço IP que está disponível na rede de failover de teste.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Failover de teste em uma rede de produção em um site de recuperação
Recomendamos que, ao fazer um failover de teste, você escolha uma rede diferente da rede do site de recuperação de produção fornecida no [mapeamento de rede](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Mas se realmente desejar validar a conectividade de rede de ponta a ponta em uma máquina virtual com failover, observe os seguintes pontos:

* Verifique se a máquina virtual primária está desligada quando estiver fazendo o failover de teste. Se você não fizer isso, duas máquinas virtuais com a mesma identidade serão executadas na mesma rede ao mesmo tempo. Essa situação pode levar a consequências indesejadas.
* As alterações feitas nas máquinas virtuais de failover de teste são perdidas quando elas são limpas. Essas alterações não são replicadas novamente para a máquina virtual primária.
* Essa forma de realizar o teste resulta em um tempo de inatividade para o aplicativo de produção. Solicite aos usuários do aplicativo que não usem o aplicativo quando a simulação de DR estiver em andamento.  


## <a name="next-steps"></a>Próximas etapas
Depois de executar um failover de teste com êxito, você pode tentar um [failover](site-recovery-failover.md).
