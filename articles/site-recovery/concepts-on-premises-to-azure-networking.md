---
title: Conectar-se ao failover local de VMs do Azure com o Azure Site Recovery
description: Descreve como se conectar a VMs do Azure após o failover do local para o Azure usando o Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 4b88ed44632aa255837d8fb499782e11c716d443
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048180"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Conectar-se a VMs do Azure após o failover do local 


Este artigo descreve como configurar a conectividade para que você possa se conectar com êxito às VMs do Azure após o failover.

Quando você configura a recuperação de desastre de VMs (máquinas virtuais) locais e servidores físicos no Azure, [Azure site Recovery](site-recovery-overview.md) inicia a replicação de computadores no Azure. Em seguida, quando ocorrerem interrupções, você poderá fazer failover para o Azure do site local. Quando ocorre o failover, o Site Recovery cria VMs do Azure usando dados replicados locais. Como parte do planejamento da recuperação de desastres, você precisa descobrir como se conectar a aplicativos em execução nessas VMs do Azure após o failover.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Prepare os computadores locais antes do failover.
> * Prepare as VMs do Azure após o failover. 
> * Reter endereços IP em VMs do Azure após o failover.
> * Atribua novos endereços IP às VMs do Azure após o failover.

## <a name="prepare-on-premises-machines"></a>Preparar computadores locais

Para garantir a conectividade com as VMs do Azure, prepare seus computadores locais antes do failover.

### <a name="prepare-windows-machines"></a>Preparar computadores Windows

Em computadores Windows locais, faça o seguinte:

1. Defina as configurações do Windows. Isso inclui remover todas as rotas persistentes estáticas ou o proxy WinHTTP e definir a política SAN de disco como **OnlineAll**. [Siga](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) estas instruções.

2. Verifique se [esses serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.

3. Habilite a RDP (Área de Trabalho Remota) para permitir conexões remotas com o computador local. [Saiba como](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) habilitar o RDP com o PowerShell.

4. Para acessar uma VM do Azure pela Internet após o failover, no firewall do Windows no computador local, permita TCP e UDP no perfil público e defina o RDP como um aplicativo permitido para todos os perfis.

5. Se você quiser acessar uma VM do Azure em uma VPN site a site após o failover, no firewall do Windows no computador local, permita o RDP para o domínio e os perfis particulares. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como permitir o tráfego RDP.
6. Certifique-se de que não haja nenhuma atualização do Windows pendente na VM local quando você disparar um failover. Se houver, as atualizações poderão iniciar a instalação na VM do Azure após o failover e você não poderá entrar na VM até que as atualizações sejam concluídas.

### <a name="prepare-linux-machines"></a>Preparar computadores Linux

Em computadores Linux locais, faça o seguinte:

1. Verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma conexão SSH.


## <a name="configure-azure-vms-after-failover"></a>Configurar VMs do Azure após o failover

Após o failover, faça o seguinte nas VMs do Azure que são criadas.

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. Você não pode usar o mesmo endereço IP público para a VM do Azure que você usou para seu computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md)
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada à porta RDP ou SSH.
3. Verifique o [diagnóstico de inicialização](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) para exibir a VM.


> [!NOTE]
> O serviço Azure Bastion oferece o acesso privado RDP e SSH a VMs do Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="set-a-public-ip-address"></a>Definir um endereço IP público

Como alternativa à atribuição de um endereço IP público manualmente a uma VM do Azure, você pode atribuir o endereço durante o failover usando um script ou um runbook de automação do Azure em um [plano de recuperação](site-recovery-create-recovery-plans.md)site Recovery, ou pode configurar o roteamento no nível do DNS usando o Gerenciador de tráfego do Azure. [Saiba mais](concepts-public-ip-address-with-site-recovery.md) sobre como configurar um endereço público.


## <a name="assign-an-internal-address"></a>Atribuir um endereço interno

Para definir o endereço IP interno de uma VM do Azure após o failover, você tem algumas opções:

- **Manter o mesmo endereço IP**: você pode usar o mesmo endereço IP na VM do Azure como aquele alocado para o computador local.
- **Usar endereço IP diferente**: você pode usar um endereço IP diferente para a VM do Azure.


## <a name="retain-ip-addresses"></a>Manter os endereços IP

Site Recovery permite que você mantenha os mesmos endereços IP ao fazer failover no Azure. Manter o mesmo endereço IP evita possíveis problemas de rede após o failover, mas introduz alguma complexidade.

- Se a VM do Azure de destino usar o mesmo endereço IP/sub-rede que o site local, você não poderá se conectar entre eles usando uma conexão VPN site a site ou ExpressRoute, devido à sobreposição de endereço. As sub-redes devem ser exclusivas.
- Você precisa de uma conexão do local para o Azure após o failover, para que os aplicativos estejam disponíveis em VMs do Azure. O Azure não dá suporte a VLANs ampliadas. portanto, se você quiser manter os endereços IP, precisará obter o espaço IP para o Azure ao fazer failover de toda a sub-rede, além da máquina local.
- O failover de sub-rede garante que uma sub-rede específica não esteja disponível simultaneamente no local e no Azure.

A retenção de endereços IP requer as seguintes etapas:

- Nas propriedades de rede & de computação do item replicado, defina a rede e o endereçamento IP para a VM do Azure de destino para espelhar a configuração local.
- As sub-redes devem ser gerenciadas como parte do processo de recuperação de desastre. Você precisa de uma VNet do Azure para corresponder à rede local e, depois que as rotas de rede de failover devem ser modificadas para refletir que a sub-rede foi movida para o Azure e novos locais de endereço IP.  

### <a name="failover-example"></a>Exemplo de failover

Vamos examinar um exemplo.

- O Woodgrove Bank da empresa fictícia hospeda seus aplicativos de negócios locais, eles hospedam seus aplicativos móveis no Azure.
- Eles se conectam do local para o Azure por VPN site a site. 
- O Woodgrove está usando Site Recovery para replicar computadores locais para o Azure.
- Seus aplicativos locais usam endereços IP embutidos em código, portanto, eles desejam manter os mesmos endereços IP no Azure.
- No local, os computadores que executam os aplicativos estão em execução em três sub-redes:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Seus aplicativos em execução no Azure estão localizados na **rede** de VNet do Azure do Azure em duas sub-redes:
    - 172.16.1.0/24
    - 172.16.2.0/24

Para manter os endereços, veja o que eles fazem.

1. Quando eles habilitam a replicação, eles especificam que os computadores devem replicar para a **rede do Azure**.
2. Eles criam a **rede de recuperação** no Azure. Essa VNet espelha a sub-rede 192.168.1.0/24 em sua rede local.
3. O Woodgrove configura uma [conexão vnet a vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre as duas redes. 

    > [!NOTE]
    > Dependendo dos requisitos do aplicativo, uma conexão VNet a VNet pode ser configurada antes do failover, como uma etapa manual/script do runbook de automação do Azure em um [plano de recuperação](site-recovery-create-recovery-plans.md)site Recovery ou após a conclusão do failover.

4. Antes do failover, nas propriedades da máquina em Site Recovery, elas definem o endereço IP de destino como o endereço do computador local, conforme descrito no próximo procedimento.
5. Após o failover, as VMs do Azure são criadas com o mesmo endereço IP. O Woodgrove conecta-se da **rede do Azure** à rede virtual de **recuperação** usando o emparelhamento VNet (com conectividade de trânsito habilitada).
6. No local, o Woodgrove precisa fazer alterações na rede, incluindo a modificação de rotas para refletir que 192.168.1.0/24 foi movido para o Azure.  

**Infraestrutura antes do failover**

![Antes do failover da sub-rede](./media/site-recovery-network-design/network-design7.png)


**Infraestrutura após o failover**

![Após o failover da sub-rede](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Definir configurações de rede de destino

Antes do failover, especifique as configurações de rede e o endereço IP para a VM do Azure de destino.

1.  No cofre dos serviços de recuperação-> **itens replicados**, selecione o computador local.
2. Na página **computação e rede** do computador, clique em **Editar** para definir as configurações de rede e adaptador para a VM do Azure de destino.
3. Em **Propriedades de rede**, selecione a rede de destino na qual a VM do Azure será localizada quando for criada após o failover.
4. Em **interfaces de rede**, configure os adaptadores de rede na rede de destino. Por padrão Site Recovery mostra todas as NICs detectadas no computador local.
    - Em **tipo de interface de rede de destino** , você pode definir cada NIC como **primária**, **secundária** ou **não criar** se não precisar dessa NIC específica na rede de destino. Um adaptador de rede deve ser definido como primário para o failover. Observe que a modificação da rede de destino afeta todas as NICs para a VM do Azure.
    - Clique no nome da NIC para especificar a sub-rede na qual a VM do Azure será implantada.
    - Substitua **Dynamic** pelo endereço IP privado que você deseja atribuir à VM do Azure de destino. Se um endereço IP não for especificado Site Recovery atribuirá o próximo endereço IP disponível na sub-rede à NIC no failover.
    - [Saiba mais](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sobre como gerenciar NICs para o failover local no Azure.


## <a name="get-new-ip-addresses"></a>Obter novos endereços IP

Nesse cenário, a VM do Azure Obtém um novo endereço IP após o failover. Para configurar um novo endereço IP para a máquina virtual criada após o failover, as etapas a seguir podem ser referenciadas-

1. Vá para **itens replicados**.
2. Selecione a máquina virtual do Azure desejada.
3. Selecione **computação e rede** e selecione **Editar**.

     ![Personalizar as configurações de rede de failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Para atualizar as configurações de rede de failover, selecione **Editar** para a NIC que você deseja configurar. Na próxima página que é aberta, forneça o endereço IP pré-criado correspondente no local de failover e failover de teste.

    ![Editar a configuração da NIC](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Selecione **OK**.

Site Recovery agora respeitará essas configurações e garantirá que a máquina virtual no failover esteja conectada ao recurso selecionado por meio do endereço IP correspondente, se estiver disponível no intervalo de IP de destino. Nesse cenário, não há necessidade de fazer failover de toda a sub-rede. Uma atualização de DNS será necessária para atualizar registros para o computador com failover para apontar para o novo endereço IP da máquina virtual.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre como](site-recovery-active-directory.md) replicar o Active Directory local e o DNS para o Azure.