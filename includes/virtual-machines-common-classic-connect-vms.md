

![Máquinas virtuais em um serviço de nuvem autônomo](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Ao colocar as máquinas virtuais em uma rede virtual, você pode decidir quantos serviços de nuvem deseja usar para os conjuntos de disponibilidade e o balanceamento de carga. Além disso, você pode organizar as máquinas virtuais em sub-redes da mesma forma como sua rede local e conectar a rede virtual à sua rede local. Aqui está um exemplo:

![Máquinas virtuais em uma rede virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

As redes virtuais são a maneira recomendada para conectar máquinas virtuais no Azure. A prática recomendada é configurar cada camada do aplicativo em um serviço de nuvem separado. No entanto, talvez seja necessário combinar algumas máquinas virtuais de diferentes níveis de aplicativos no mesmo serviço de nuvem para permanecer dentro do máximo de 200 serviços de nuvem por assinatura. Para verificar isso e outros limites, consulte [Assinatura do Azure e limites de serviços, cotas e restrições](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Conectar VMs em uma rede virtual
Para conectar máquinas virtuais em uma rede virtual:

1. Crie a rede virtual no [portal do Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) e especifique 'implantação clássica'.
2. Crie o conjunto de serviços de nuvem para sua implantação de modo a refletir seu design para conjuntos de disponibilidade e balanceamento de carga. No portal do Azure, clique em **Novo > Computação > Serviço de nuvem** para cada serviço de nuvem.

  Enquanto você preenche os detalhes do serviço de nuvem, escolha o mesmo _grupo de recursos_ usado com o serviço de nuvem.

3. Para criar cada nova máquina virtual, clique em **Novo > Computação** e então selecione a imagem de VM apropriada dos **Aplicativos em destaque**.

  Na folha **Noções básicas** da VM, escolha o mesmo _grupo de recursos_ usado com a rede virtual.

  ![Folha Noções básicas da VM ao usar uma rede virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Enquanto você preenche as **Configurações** da VM, escolha o _Serviço de nuvem_ ou a _rede virtual_ correta para a máquina virtual.

  O Azure selecionará o outro item com base em sua seleção.

  ![Folha Configurações da VM ao usar uma rede virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Conectar VMs em um serviço de nuvem autônomo
Para conectar máquinas virtuais em um serviço de nuvem autônomo:

1. Crie o serviço de nuvem no [portal do Azure](http://portal.azure.com). Clique em **Novo > Computação > Serviço de nuvem**. Ou você pode criar o serviço de nuvem para sua implantação ao criar sua primeira máquina virtual.
2. Quando você criar as máquinas virtuais, escolha o mesmo grupo de recursos usado com o serviço de nuvem.

  ![Adicionar uma máquina virtual a um serviço de nuvem existente](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Enquanto preenche os detalhes da VM, escolha o nome do serviço de nuvem criado na primeira etapa.

  ![Selecionar um serviço de nuvem para uma máquina virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
