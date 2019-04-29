---
title: Executar uma simulação de recuperação de desastre do Hyper-V para um site secundário usando o Azure Site Recovery | Microsoft Docs
description: Saiba como executar uma simulação de recuperação de desastres de VMs do Hyper-V em nuvens do VMM para um datacenter local secundário usando o Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: dc8deb16f7d124c5fb11568f25050eee99a245b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865504"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Executar uma simulação de recuperação de desastres para as VMs do Hyper-V em um site secundário


Este artigo descreve como realizar uma simulação de recuperação de desastres (DR) para VMs do Hyper-V gerenciadas em nuvens do System Center VMM (Virtual Machine Manager) para um site secundário local usando o [Azure Site Recovery](site-recovery-overview.md).

Execute um failover de teste para validar sua estratégia de replicação e realize uma simulação de DR sem nenhuma perda de dados ou tempo de inatividade. Um failover de teste não afeta a replicação em andamento nem o ambiente de produção. 

## <a name="how-do-test-failovers-work"></a>Como failovers de teste funcionam?

Executa-se um failover de teste do site principal para o site secundário. Se você simplesmente deseja verificar se uma VM faz failover, você pode executar um teste de failover sem configurar nada no site secundário. Se você quiser verificar se o failover de aplicativo funciona conforme o esperado, você precisará configurar a rede e a infraestrutura no local secundário.
- Você pode executar um failover de teste em uma única VM ou em um [plano de recuperação](site-recovery-create-recovery-plans.md).
- Você pode executar um failover de teste sem uma rede, com uma rede existente ou com uma rede criada automaticamente. Mais detalhes sobre essas opções são fornecidos na tabela a seguir.
    - Você pode executar um failover de teste sem uma rede. Essa opção será útil se você simplesmente deseja verificar se uma VM foi capaz de fazer o failover, mas você não será capaz de verificar nenhuma configuração de rede.
    - Execute o failover com uma rede existente. Recomendamos que você não use uma rede de produção.
    - Execute o failover e permita que o Site Recovery crie automaticamente uma rede de teste. Nesse caso, o Site Recovery vai criar a rede automaticamente e vai limpá-la quando o failover de teste for concluído.
- Você precisa selecionar um ponto de recuperação para o failover de teste: 
    - **Mais recente processado**: Essa opção executa failover de uma VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
    - **Último consistente com o aplicativo**: Essa opção executa failover de uma VM para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery. 
    - **Mais recente**: Essa opção primeiro processa todos os dados que foram enviados ao serviço do Site Recovery, para criar um ponto de recuperação para cada VM antes do failover da VM. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM criada após o failover tem todos os dados replicados para o Site Recovery quando o failover for disparado.
    - **Multi-VMs mais recentes processadas**: Disponível para os planos de recuperação que incluem uma ou mais VMs que tenham consistência de várias VMs ativadas. As VMs com a configuração habilitaram o failover para o último ponto de recuperação consistente com várias VMs em comum. Outras VMs fazem failover para o último ponto de recuperação processado.
    - **Multi-VMs mais recentes consistentes com o aplicativo**: Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VM habilitada. As VMs que fazem parte de um failover do grupo de replicação para o ponto de recuperação comum mais recente de várias VMs consistente com aplicativo. Outras VMs fazem failover para seus últimos pontos de recuperação consistentes com aplicativo.
    - **Personalizado**: Use essa opção para fazer failover de uma VM específica para um ponto de recuperação específico.



## <a name="prepare-networking"></a>Preparar a rede

Ao executar um failover de teste, você deverá selecionar as configurações de rede para os computadores de réplica de teste, como resumido na tabela.

| **Opção** | **Detalhes** | |
| --- | --- | --- |
| **Nenhum** | A VM de teste é criada no host no qual está localizada a VM de réplica. Ela não é adicionada à nuvem e não está conectada a nenhuma rede.<br/><br/> Você pode conectar o computador a uma rede de VMs depois que ela for criada.| |
| **Usar existente** | A VM de teste é criada no host no qual está localizada a VM de réplica. Não é adicionada à nuvem.<br/><br/>Crie uma rede VM isolada da rede de produção.<br/><br/>Se você estiver usando uma rede baseada em VLAN, recomendamos criar uma rede lógica separada (não usada em produção) no VMM para essa finalidade. Essa rede lógica é usada para criar redes VM para failovers de teste.<br/><br/>A rede lógica deve estar associada a, pelo menos, um dos adaptadores de rede de todos os servidores do Hyper-V que hospedam máquinas virtuais.<br/><br/>Para redes lógicas VLAN, os sites de rede adicionados à rede lógica devem ser isolados.<br/><br/>Se você está estiver usando uma rede lógica baseada na Virtualização de Rede do Windows, o Azure Site Recovery criará automaticamente redes de VM isoladas. | |
| **Criar uma rede** | Uma rede de teste temporária é criada automaticamente com base na configuração especificada em **Rede Lógica** e seus sites de rede relacionados.<br/><br/> O failover verifica se as VMs são criadas.<br/><br/> Você deve usar esta opção se o plano de recuperação usa mais de uma rede de VMs.<br/><br/> Caso esteja usando redes da Virtualização de Rede do Windows, essa opção pode ser usada para criar automaticamente redes VM com as mesmas configurações (sub-redes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são removidas automaticamente depois que o failover de teste é concluído.<br/><br/> A VM de teste é criada no host no qual existe a máquina virtual de réplica. Não é adicionada à nuvem.|

### <a name="best-practices"></a>Práticas recomendadas

- O teste de uma rede de produção resulta em tempo de inatividade para as cargas de trabalho de produção. Peça aos usuários que não usem aplicativos relacionados quando a análise detalhada da recuperação de desastre estiver em andamento.

- A rede de teste não precisa ser compatível com o tipo de rede lógica do VMM usado para o failover de teste. Porém, algumas combinações não funcionam:

     - Quando a réplica usa isolamento baseado em VLAN e DHCP, a rede VM da réplica não precisa de um pool de endereços IP estáticos. Portanto, o uso da Virtualização de Rede do Windows para o failover de teste não funcionará porque nenhum pool de endereços está disponível. 
        
     - O failover de teste não funcionará se a rede de réplica não usar nenhum isolamento e a rede de teste usar a Virtualização de Rede do Windows. Isso ocorre porque a rede sem isolamento não tem as sub-redes necessárias para criar uma rede da Virtualização de Rede do Windows.
        
- É recomendável que você não use a rede que selecionou para o mapeamento de rede e para o failover de teste.

- O modo como as máquinas virtuais de réplica são conectadas às redes VM mapeadas após o failover depende de como a rede VM é configurada no console do VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento ou com isolamento de VLAN

Se uma rede de VMs é configurada no VMM sem isolamento, ou isolamento de VLAN, observe o seguinte:

- Se o DHCP for definido para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações especificadas para o site da rede na rede lógica associada. A máquina virtual recebe seu endereço IP do servidor DHCP disponível.
- Não é necessário definir um pool de endereços IP estáticos para a rede VM de destino. Se um pool de endereços IP estáticos for usado para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN por meio das configurações especificadas para o site da rede na rede lógica associada.
- A máquina virtual receberá seu endereço IP do pool definido para a rede VM. Se um pool de endereços IP estáticos não for definido na rede VM de destino, a alocação do endereço IP falhará. Crie o pool de endereços IP nos servidores VMM de origem e destino que você usará para proteção e recuperação.

### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com Virtualização de Rede do Windows

Se uma rede de VMs é configurada no VMM com Virtualização de Rede do Windows, observe o seguinte:

- Você deverá definir um pool estático para a rede VM de destino, independentemente de a rede VM de origem estar configurada para usar o DHCP ou um pool de endereços IP estáticos. 
- Se você definir o DHCP, o servidor VMM de destino atuará como um servidor DHCP e fornecerá um endereço IP do pool definido para a rede VM de destino.
- Se o uso de um pool de endereços IP estáticos for definido para o servidor de origem, o servidor VMM de destino alocará um endereço IP do pool. Em ambos os casos, a alocação de endereço IP falhará se um pool de endereços IP estáticos não for definido.



## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura

Se você simplesmente deseja verificar que uma máquina virtual pode fazer failover, você pode executar um failover de teste sem uma infraestrutura. Se você quiser fazer uma análise completa de recuperação de desastres para testar o failover de aplicativo, você precisará preparar a infraestrutura no site secundário:

- Se você executar um failover de teste usando uma rede existente, prepare o Active Directory, DHCP e DNS nessa rede.
- Se você executar um failover de teste com a opção de criar uma rede de VMs automaticamente, você precisará adicionar recursos de infraestrutura à rede criada automaticamente, antes de executar o failover de teste. Em um plano de recuperação, você pode facilitar isso adicionando uma etapa manual antes do Grupo-1 no plano de recuperação que você pretende usar para o failover de teste. Em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover de teste.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvidas no failover de teste usarem o DHCP, crie um servidor DHCP de teste na rede isolada que é criada para fins de failover de teste.


### <a name="prepare-active-directory"></a>Preparar o Active Directory
Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Para obter mais informações, examine as [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparar o DNS
Prepare um servidor DNS para o failover de teste da seguinte forma:

* **DHCP**: Se as máquinas virtuais usarem o DHCP, o endereço IP do DNS de teste deverá ser atualizado no servidor DHCP de teste. Quando você usa um tipo de rede da Virtualização de Rede do Windows, o servidor VMM atua como o servidor DHCP. Portanto, o endereço IP do DNS deve ser atualizado na rede de failover de teste. Nesse caso, as máquinas virtuais se registrarão no servidor DNS relevante.
* **Endereço estático**: Se as máquinas virtuais usarem um endereço IP estático, o endereço IP do servidor DNS de teste deverá ser atualizado na rede de failover de teste. Talvez você precise atualizar o DNS com o endereço IP das máquinas virtuais de teste. É possível usar o seguinte script de exemplo para este fim:

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

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Test Failover**.
2. Na folha **Failover de Teste**, especifique como as VMs de réplica devem ser conectadas às redes após o failover de teste.
3. Acompanhe o progresso do failover na guia **Trabalhos** .
4. Após o failover ser concluído, verifique se as VMs são iniciadas com êxito.
5. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. Essa etapa exclui quaisquer VMs e redes que foram criadas pelo Site Recovery durante o failover de teste. 

![Failover de Teste](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> O endereço IP fornecido a uma máquina virtual durante o failover de teste é o mesmo endereço IP que a máquina virtual receberia em um failover planejado ou não planejado (supondo que o endereço IP esteja disponível na rede de failover de teste). Se o mesmo endereço IP não estiver disponível na rede de failover de teste, a máquina virtual receberá outro endereço IP que está disponível na rede de failover de teste.



### <a name="run-a-test-failover-to-a-production-network"></a>Executar um failover de teste para uma rede de produção

É recomendável que você não execute um failover de teste para sua rede de sites de recuperação de produção que você especificou durante o mapeamento de rede. Mas se você realmente precisa validar a conectividade de rede ponta a ponta em uma VM com failover, observe os seguintes pontos:

* Verifique se a máquina virtual primária está desligada quando você executa o failover de teste. Se você não fizer isso, duas máquinas virtuais com a mesma identidade serão executadas na mesma rede ao mesmo tempo. Essa situação pode levar a consequências indesejadas.
* As alterações feitas nas VMs de failover de teste são perdidas quando elas são limpas. Essas alterações não são replicadas de volta nas VMs primárias.
* Essa forma de realizar o teste resulta em um tempo de inatividade para o aplicativo de produção. Solicite aos usuários do aplicativo que não usem o aplicativo quando a simulação de DR estiver em andamento.  


## <a name="next-steps"></a>Próximas etapas
Depois de executar uma simulação de recuperação de desastre com êxito, você pode [executar um failover completo](site-recovery-failover.md).



