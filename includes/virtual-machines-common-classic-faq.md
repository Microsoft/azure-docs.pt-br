---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bc3590e90cacfa4966f0d1f64aa1c8d49483cb1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485420"
---
Este artigo trata de algumas das perguntas mais comuns feitas pelos usuários sobre as máquinas virtuais do Azure criadas com o modelo de implantação clássico.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Posso migrar minha VM criada no modelo de implantação clássico para o novo modelo do Gerenciador de Recursos?
Sim. Para obter instruções de como migrar, consulte:

* [Migrar do modelo clássico para o Azure Resource Manager usando o Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migrar do modelo clássico para o Azure Resource Manager usando a CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>O que eu posso executar em uma VM do Azure?
Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Você pode executar versões recentes do Windows Server, bem como uma variedade de distribuições Linux. Para obter detalhes sobre o suporte, consulte:

• Para VMs do Windows – [Suporte de software para servidores da Microsoft para Máquinas Virtuais do Azure](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Para VMs do Linux – [Linux em distribuições endossadas pelo Azure](https://go.microsoft.com/fwlink/p/?LinkId=393551)

Para imagens do Windows Client, determinadas versões do Windows 7 e Windows 8.1 estão disponíveis para assinantes de benefícios do Azure na MSDN e assinantes do modelo "pré-pago" do Desenvolvimento e Teste da MSDN, para tarefas de teste e desenvolvimento. Para obter detalhes, incluindo instruções e limitações, veja [Imagens do Windows Client para assinantes do MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Por que os grupos de afinidades estão sendo preteridos?
Os grupos de afinidade são um conceito herdado para um agrupamento geográfico de implantações de serviço de nuvem do cliente e contas de armazenamento no Azure. Eles foram originalmente fornecidos para aprimorar o desempenho da rede VM para VM nos designs de rede iniciais do Azure. Elas também dão suporte à versão inicial das redes virtuais (VNets), que eram limitadas a um pequeno conjunto de hardware em uma região.

A rede atual do Azure em uma região foi projetada de modo que os grupos de afinidades não são mais necessários. As redes virtuais também estão em um escopo regional e, portanto, não é mais necessário ter um grupo de afinidades ao usar uma rede virtual. Em virtude dessas melhorias, não é mais recomendável que os clientes usem grupos de afinidades, pois eles podem ser restritivos em alguns cenários. O uso de grupos de afinidades associará desnecessariamente suas VMs a um hardware específico, limitando a escolha de tamanhos de VM disponíveis para você. Ele também pode levar a erros relacionados à capacidade quando você tentar adicionar novas VMs, caso o hardware específico associado ao grupo de afinidades esteja próximo à capacidade.

Os recursos de grupo de afinidades já foram preteridos no modelo de implantação do Azure Resource Manager e no portal do Azure. Para o portal clássico do Azure, o suporte para criação de grupos de afinidades e criação de recursos de armazenamento fixos em um grupo de afinidades está sendo preterido. Você não precisa modificar os serviços de nuvem existentes que estejam usando um grupo de afinidades. No entanto, você não deve usar os grupos de afinidades para os novos serviços de nuvem, a menos que isso seja recomendado por um profissional do suporte do Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento eu posso usar com uma máquina virtual?
Cada disco de dados pode ter até 1 TB. O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Uma conta de armazenamento do Azure fornece armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Quais tipos de disco rígido virtual eu posso usar?
O Azure dá suporte apenas a discos rígidos virtuais fixos no formato VHD. Se tiver um VHDX que deseja usar no Azure, você precisará primeiro convertê-lo usando o Gerenciador do Hyper-V ou o cmdlet [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656) . Depois de fazer isso, use o cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (no modo de Gerenciamento de Serviços) para carregar o VHD em uma conta de armazenamento do Azure para que você possa usá-lo com máquinas virtuais.

* Para obter instruções sobre o Linux, veja [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Essas máquinas virtuais são iguais às máquinas virtuais Hyper-V?
De muitas formas, elas são semelhantes às VMs Hyper-V da "Geração 1", mas não são exatamente iguais. Os dois tipos oferecem hardware virtualizado e os discos rígidos virtuais em formato VHD são compatíveis. Isso significa que você pode movê-los entre o Hyper-V e o Azure. Três diferenças principais que às vezes surpreendem os usuários do Hyper-V são:

* O Azure não fornece acesso ao console para uma máquina virtual. Não é possível acessar uma VM até a conclusão da inicialização.
* As VMs do Azure na maioria dos [tamanhos](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) têm apenas um adaptador de rede virtual, o que significa que elas também podem ter apenas um endereço IP externo. (Os tamanhos A8 e A9 usam um segundo adaptador de rede para comunicação de aplicativos entre instâncias em cenários limitados.)
* As VMs do Azure não são compatíveis com recursos de VM do Hyper-V de geração 2. Para obter detalhes sobre esses recursos, confira [Especificações de máquina virtual do Hyper-V](https://technet.microsoft.com/library/dn592184.aspx) e [Visão geral da máquina virtual geração 2](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Essas máquinas virtuais podem usar minha infraestrutura de rede local existente?
Para máquinas virtuais criadas modelo de implantação clássico, é possível usar a Rede Virtual do Azure para ampliar a infraestrutura existente. A abordagem é semelhante ao estabelecimento de uma filial. Você provisionar e gerenciar VPNs (redes virtuais privadas) no Azure e conectá-las com segurança à infraestrutura de TI local. Para obter detalhes, veja [Visão geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md).

Você precisará especificar a rede à qual deseja que a máquina virtual pertença quando criar a máquina virtual. Você não pode vincular uma máquina virtual existente a uma rede virtual. No entanto, você pode contornar esse problema desanexando o VHD (disco rígido virtual) da máquina virtual existente e usá-lo para criar uma nova máquina virtual com a configuração de rede desejada.

## <a name="how-can-i-access--my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Você precisa estabelecer uma conexão remota para fazer logon na máquina virtual, usando a Conexão de Área de Trabalho Remota para uma VM do Windows ou um SSH (Secure Shell) para uma VM Linux. Para obter instruções, consulte:

* [Como fazer logon em uma máquina virtual executando o Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Há suporte para o máximo de duas conexões simultâneas, a menos que o servidor esteja configurado como um host de sessão dos Serviços de Área de Trabalho Remota.  
* [Como fazer logon em uma máquina virtual executando o Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por padrão, o SSH permite um máximo de 10 conexões simultâneas. Você pode aumentar esse número editando o arquivo de configuração.

Se tiver problemas com a Área de Trabalho Remota ou o SSH, instale e use a extensão [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a corrigir o problema.

Para VMs Windows, as opções adicionais incluem:

* No portal do Azure, localize a VM e clique em **Redefinir Acesso Remoto** na barra de comandos.
* Examine [Solucionar problemas de conexões da Área de Trabalho Remota a uma Máquina Virtual do Azure baseada em Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Use a Comunicação Remota do Windows PowerShell para conectar-se à VM ou crie pontos de extremidade adicionais para outros recursos se conectarem à VM. Para obter detalhes, confira [Como instalar pontos de extremidade em uma máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Se estiver familiarizado com o Hyper-V, você pode estar em busca de uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque não há suporte para o acesso de console a uma máquina virtual.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Posso usar o disco temporário (a unidade D: para Windows ou /dev/sdb1 para Linux) para armazenar dados?
Você não deve usar o disco temporário (a unidade D: por padrão para Windows ou /dev/sdb1 para Linux) para armazenar dados. Elas são apenas um armazenamento temporário, você se arriscaria a perder dados que não podem ser recuperados. Isso pode ocorrer quando a máquina virtual é movida para um host diferente. O redimensionamento de uma máquina virtual, atualização do host ou uma falha de hardware no host são algumas das razões que podem levar à mudança de uma máquina virtual.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como eu posso alterar a letra da unidade do disco temporário?
Em uma máquina virtual do Windows, você pode alterar a letra da unidade movendo o arquivo de paginação e reatribuindo as letras de unidade, mas você precisará certificar-se de seguir as etapas em uma ordem específica. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Como eu posso atualizar o sistema operacional convidado?
O termo atualizar geralmente significa passar para uma versão mais recente do sistema operacional, mantendo o mesmo hardware. Para VMs do Azure, o processo de mudança para uma versão mais recente é diferente para o Linux e o Windows:

* Para VMs do Linux, use os procedimentos e ferramentas de gerenciamento do pacote adequados à distribuição.
* Para uma máquina virtual do Windows, você precisa para migrar o servidor usando algo como as Ferramentas de Migração do Windows Server. Não tente atualizar o SO convidado enquanto ele estiver no Azure. Isso não tem suporte devido ao risco de perder o acesso à máquina virtual. Se ocorrerem problemas durante a atualização, você pode perder a capacidade de iniciar uma sessão de Área de Trabalho Remota e não seria possível solucionar os problemas.

Para obter detalhes gerais sobre o processo e as ferramentas para migrar um Windows Server, veja [Migrar funções e recursos para o Windows Server](https://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Quais são o nome de usuário e a senha padrão na máquina virtual?
As imagens fornecidas pelo Azure não têm um nome de usuário e uma senha pré-configurados. Quando cria a máquina virtual usando uma dessas imagens, você precisa fornecer um nome de usuário e senha que você usará para fazer logon na máquina virtual.

Se você esqueceu o nome de usuário ou senha e instalou o agente de VM, você pode instalar e usar a extensão [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para corrigir o problema.

Detalhes adicionais:

* Para as imagens do Linux, se você usar o portal do Azure, "azureuser" é fornecido como nome de usuário padrão, mas você pode alterar isso usando "Da Galeria" em vez de "Criação rápida" como a maneira de criar a máquina virtual. Usar "Da Galeria" também permite optar por usar uma senha, uma chave SSH ou ambas para fazer o logon. A conta de usuário é um usuário sem privilégios que tem acesso "sudo" para executar comandos privilegiados. A conta "raiz" está desabilitada.
* Para imagens do Windows, você precisará fornecer um nome de usuário e uma senha ao criar a máquina virtual. A conta é adicionada ao grupo Administradores.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>O Azure pode executar antivírus nas minhas máquinas virtuais?
O Azure oferece diversas opções para soluções antivírus, mas cabe a você gerenciar isso. Por exemplo, talvez seja necessário ter uma assinatura separada para software antimalware, e você precisará decidir quando executar varreduras e instalar atualizações. Você pode adicionar suporte antivírus com uma extensão de VM para Antimalware da Microsoft, o Symantec Endpoint Protection ou o TrendMicro Deep Security Agent quando criar uma máquina virtual do Windows ou em um momento posterior. As extensões Symantec e TrendMicro permitem que você use uma assinatura de avaliação gratuita de tempo limitado ou uma assinatura empresarial existente. O Antimalware da Microsoft é gratuito. Para obter mais informações, consulte:

* [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Implantando soluções antimalware em máquinas virtuais do Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Quais são minhas opções de backup e recuperação?
O Backup do Azure está disponível como visualização em determinadas regiões. Para obter detalhes, veja [Fazer backup de máquinas virtuais do Azure](../articles/backup/backup-azure-arm-vms.md). Existem outras soluções de parceiros certificados. Para descobrir o que está disponível no momento, pesquise no Azure Marketplace.

Uma outra opção é usar os recursos de instantâneo do armazenamento de blob. Para fazer isso, você precisará fechar a VM antes de qualquer operação que se baseie em um instantâneo de blob. Isso salva gravações de dados pendentes e coloca o sistema de arquivos em um estado consistente.

## <a name="how-does-azure-charge-for-my-vm"></a>Como o Azure cobra pela minha VM?
O Azure cobra um preço por hora com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos de uso. Se você criar a VM com uma imagem de VM que contém um determinado software pré-instalado, encargos adicionais de software por hora podem ser aplicáveis. O Azure cobra separadamente pelo armazenamento para discos de dados e sistema operacional da VM. Armazenamento em disco temporário é gratuito.

Você será cobrado quando o status da VM for Em execução ou Parada, mas não será cobrado quando o status da VM for Parada (Desalocada). Para colocar uma VM no estado Parada (Desalocada), siga um destes procedimentos:

* Feche ou exclua a VM no portal do Azure.
* Use o cmdlet Stop-AzureVM, disponível no módulo do Azure PowerShell.
* Use a operação de Função de Desligamento na API REST do Gerenciamento de Serviços e especifique StoppedDeallocated para o elemento PostShutdownAction.

Para obter mais detalhes, veja [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>O Azure reinicializará minha VM para manutenção?
Às vezes, o Azure reinicia sua VM como parte de atualizações de manutenção regulares e planejadas nos datacenters do Azure.

Eventos de manutenção não planejados podem ocorrer quando o Azure detectar um problema sério de hardware que afete sua VM. Para eventos não planejados, o Azure automaticamente migra a VM para um host íntegro e reinicia a VM.

Para qualquer VM autônoma (ou seja, uma VM que não faz parte de um conjunto de disponibilidade), o Azure notifica o Administrador de Serviços de assinatura por email pelo menos uma semana antes da manutenção planejada, porque as máquinas virtuais podem ser reiniciadas durante a atualização. Aplicativos em execução nas VMs podem experimentar tempo de inatividade.

Você também pode usar o portal do Azure ou Azure PowerShell para exibir os logs de reinicialização quando a reinicialização ocorrer devido a uma manutenção planejada. Para obter detalhes, veja [Exibindo logs de reinicialização de VM](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Para fornecer redundância, coloque duas ou mais VMs configuradas de forma semelhante no mesmo conjunto de disponibilidade. Isso ajuda a garantir que pelo menos uma VM esteja disponível durante a manutenção planejada ou não planejada. O Azure garante determinados níveis de disponibilidade de VM para essa configuração. Para obter detalhes, confira [Gerenciar a disponibilidade de máquinas virtuais](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Recursos adicionais
[Sobre máquinas virtuais do Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Criar e Gerenciar as VMs do Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criar e gerenciar VMs do Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

