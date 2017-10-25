---
title: Proteja Novamente do Azure para um site local | Microsoft Docs
description: "Após o failover de máquinas virtuais no Azure, você pode iniciar um failback para trazer as máquinas virtuais de volta para o local. Saiba como proteger novamente antes de um failback."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 3644b41c3e3293a263bd9ff996d4e3d26417aeed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Proteja Novamente do Azure para um site local



## <a name="overview"></a>Visão geral
Este artigo descreve como proteger novamente as máquinas virtuais do Azure para um site local. Quando estiver pronto, siga as instruções neste artigo para fazer o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure (conforme descrito em [Replicar máquinas virtuais VMware e servidores físicos para o Azure com o Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Não é possível realizar o failback após [concluir a migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), mover a máquina virtual para outro grupo de recursos ou excluir a máquina virtual do Azure. Se você desabilitar a proteção da máquina virtual, não é possível realizar o failback.


Depois que o proteja novamente for concluído e as máquinas virtuais protegidas estiverem replicando, você pode iniciar um failback nas máquinas virtuais para trazê-las para o site local.

> [!NOTE]
> Você só pode proteger novamente e failback para um host ESXi. Não é possível realizar o failback da máquina virtual para hosts Hyper-v, ou estações de trabalho do VMware ou qualquer outra plataforma de virtualização.

Publique comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Para obter uma visão geral, assista a este vídeo sobre como fazer failover do Azure para um site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Durante o failover para o Azure, o site local pode não estar acessível e, portanto, o servidor de configuração pode não estar disponível ou estar desligado. Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e em um estado conectado OK.


Quando você se prepara para proteger novamente máquinas virtuais, realize ou considere as seguintes ações de pré-requisito:

* Se um servidor vCenter gerencia as máquinas virtuais para as quais você deseja realizar o failback, você precisará das [permissões necessárias](site-recovery-vmware-to-azure-classic.md) para a descoberta das máquinas virtuais nos servidores vCenter.

  > [!WARNING]
  > Se houver instantâneos na máquina virtual ou no destino mestre local, a nova proteção falhará. Você pode excluir os instantâneos no destino mestre antes de prosseguir para uma nova proteção. Os instantâneos na máquina virtual são mesclados automaticamente durante um trabalho de nova proteção.

* Antes de realizar o failback, crie dois componentes adicionais:

  * **Servidor de processo**: o [servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md) recebe dados da máquina virtual protegida no Azure e envia dados para o site local. Uma rede de baixa latência é necessária entre o servidor de processo e a máquina virtual protegida. Assim, você poderá ter um servidor de processo local se estiver usando uma conexão do Azure ExpressRoute ou um servidor de processo baseado no Azure e uma VPN.
  
  * **Servidor de destino mestre**: o servidor de destino mestre recebe dados do failback. O servidor de gerenciamento local criado tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
    * [Uma máquina virtual Linux precisa de um servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md).
    * Uma máquina virtual Windows precisa de um servidor de destino mestre do Windows. Você pode usar o servidor de processo local e os computadores de servidor de destino mestre novamente.

    O destino mestre tem outros pré-requisitos listados em [Tarefas comuns para verificar em um destino mestre antes de proteger novamente](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

* Um servidor de configuração é necessário localmente ao fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido. 

> [!IMPORTANT]
> Faça o backup agendado regular de seu servidor. de configuração. Em caso de desastre, restaure o servidor com o mesmo endereço IP para que o failback funcione.

* Defina a configuração `disk.EnableUUID=true` nos parâmetros de configuração da máquina virtual de destino mestre no VMware. Se essa linha não existir, adicione-a. Essa configuração é necessária para fornecer um UUID consistente para o VMDK (disco de máquina virtual) para que ele monte corretamente.

* *Você não pode usar Storage vMotion no servidor de destino mestre*. Isso pode causar a falha do failback. A máquina virtual não pode ser iniciada porque os discos não estão disponíveis para ela. Para evitar esse problema, exclua os servidores de destino mestre de sua lista do vMotion.

* Adicione uma nova unidade ao servidor de destino mestre: uma unidade de retenção. Adicione um novo disco e formate a unidade.


### <a name="frequently-asked-questions"></a>Perguntas frequentes

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Por que preciso de uma VPN S2S ou uma conexão ExpressRoute para replicar dados de volta para o site local?
Enquanto que a replicação do local para o Azure pode ser feita via Internet ou uma por meio de uma conexão do ExpressRoute que tenha emparelhamento público, o proteger novamente e o failback exigem uma VPN S2S (Site a Site) para replicar os dados. Forneça a rede de modo que as máquinas virtuais que passaram por failover no Azure possam alcançar (executar ping) o servidor de configuração local. Você também pode implantar um servidor de processo na rede do Azure da máquina virtual que passou por failover. Esse servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando instalar um servidor de processo no Azure?


As máquinas virtuais no Azure que você deseja proteger novamente enviam dados de replicação a um servidor de processo. Configure sua rede para que as máquinas virtuais no Azure possam alcançar o servidor de processo.

Você pode implantar um servidor de processo no Azure ou usar o servidor de processo existente que você usou durante o failover. O ponto importante a considerar é a latência para enviar os dados de máquinas virtuais no Azure para o servidor de processo.

Se você tem uma conexão do ExpressRoute configurada, você pode usar um servidor de processo local para enviar dados porque a latência entre a máquina virtual e o servidor de processo é baixa.

 ![Diagrama da arquitetura para ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



No entanto, se você tem apenas uma VPN S2S, é recomendável implantar o servidor de processo no Azure.

 ![Diagrama da arquitetura para VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Lembre-se de que a replicação do Azure para locais pode ocorrer somente pela VPN S2S ou pelo emparelhamento privado da sua rede ExpressRoute. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

Para obter informações sobre a instalação de um servidor de processo baseado no Azure, consulte [Gerenciar um servidor de processo em execução no Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> É recomendável usar um servidor de processo com base no Azure durante o failback. O desempenho da replicação será maior se o servidor de processo estiver mais próximo da máquina virtual de replicação (a máquina no Azure que passou pelo failover). No entanto, durante a POC (prova de conceito) ou a demonstração, você pode usar o servidor de processo local junto com o ExpressRoute com emparelhamento privado para concluir a POC mais rapidamente.

> [!NOTE]
> Replicação a partir do local para o Azure poder rodar apenas através da internet ou ExpressRoute com emparelhamento público. Replicação do Azure para locais paa poder rodar somente acima de S2S VPN ou ExpressRoute com o emparelhamento privado


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>Quais portas devo abrir nos diferentes componentes para que a nova proteção possa funcionar?

![Portas para failover e failback](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>Qual servidor de destino mestre devo usar para a nova proteção?
Um servidor de destino mestre local é necessário para receber dados do servidor de processo e, em seguida, gravar o VMDK da máquina virtual local. Se você estiver protegendo máquinas virtuais do Windows, precisará de um servidor de destino mestre do Windows. Você pode reutilizar o destino mestre e o servidor de processo local<!-- !todo component -->. Para máquinas virtuais Linux, você precisa configurar outro destino mestre Linux local.


Para obter informações sobre como instalar um servidor de destino mestre, consulte:

* [Como instalar o servidor de destino mestre do Windows](site-recovery-vmware-to-azure.md)
* [Como instalar o servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Quais tipos de armazenamento têm suporte no host ESXi local durante o failback?

Atualmente, o Azure Site Recovery dá suporte ao failback somente para um repositório de dados do VMFS (sistema de arquivos de máquina virtual) ou vSAN. Não há suporte para um armazenamento de dados NFS. Devido a essa limitação, a entrada de seleção do repositório de dados na tela de nova proteção fica vazia no caso de repositórios de dados NFS ou exibe o repositório de dados vSAN, mas falha durante o trabalho. Se você pretende realizar failback, é possível criar um repositório de dados VMFS local e realizar failback nele. Este failback causará o download completo do VMDK.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Coisas comuns a verificar após a conclusão da instalação do destino mestre

* Se a máquina virtual está presente localmente no servidor vCenter, o servidor de destino mestre precisa acessar o VMDK da máquina virtual local. O acesso é necessário para gravar os dados replicados nos discos da máquina virtual. Verifique se o armazenamento de dados da máquina virtual local está montado no host de destino mestre com acesso de leitura/gravação.

* Se a máquina virtual não está presente localmente no servidor vCenter, o serviço do Site Recovery precisa criar uma nova máquina virtual durante a nova proteção. Essa máquina virtual é criada no host ESX em que você criou o destino mestre. Escolha o host ESX com cuidado para que a máquina virtual de failback seja criada no host desejado.

* *Você não pode usar Storage vMotion para o servidor de destino mestre*. Isso pode causar a falha do failback. A máquina virtual não pode ser iniciada porque os discos não estão disponíveis para ela.

  > [!WARNING]
  > Se um destino mestre passar por uma tarefa Armazenamento de vMotion depois da nova proteção, os discos das máquinas virtuais protegidas que estiverem anexados ao destino mestre serão migrados para o destino da tarefa vMotion. Se você tentar fazer failback após isso, a ação de desanexar o disco resultará em falha porque os discos não foram encontrados. Assim, ficará difícil localizar os discos em suas contas de armazenamento. Você precisará localizá-los manualmente e anexá-los à máquina virtual. Depois disso, a máquina virtual local poderá ser reinicializada.

* Adicione uma unidade de retenção ao servidor de destino mestre existente do Windows. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a máquina virtual replica para o site local. A seguir estão alguns critérios de uma unidade de retenção. Sem esses critérios, a unidade não será listada para o servidor de destino mestre.

   * O volume não é usado para nenhuma outra finalidade, como um destino de replicação.

   * O volume não está no modo de bloqueio.

   * O volume não é um volume de cache. A instalação do destino mestre não deve existir nesse volume. O volume de instalação personalizada para o servidor de processo e o destino mestre não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.

   * O tipo de sistema de arquivos do volume não é FAT ou FAT32.

   * A capacidade do volume é diferente de zero.

   * O volume de retenção padrão para o Windows é o volume R.

   * O volume de retenção padrão para o Linux é /mnt/retention.

  > [!IMPORTANT]
  > Você precisará adicionar uma nova unidade se estiver usando um servidor de processo/computador de servidor de configuração existente ou uma escala ou um servidor de processo/computador de servidor de destino mestre. A nova unidade deve atender aos requisitos anteriores. Se a unidade de retenção não estiver presente, ela não aparecerá na lista suspensa de seleção no portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no portal. Você também poderá atualizar o servidor de configuração se a unidade não aparecer depois de 15 minutos.

* Uma máquina virtual do Linux que passou por failover exige um servidor de destino mestre do Linux. Uma máquina virtual do Windows após failover precisa de um servidor de destino mestre do Windows.

* Instalar ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, os armazenamentos de dados no host de ESXi do destino mestre não podem ser detectados.

* Habilite o parâmetro `disk.EnableUUID=true` na máquina virtual de destino mestre usando as propriedades do vCenter. <!-- !todo Needs link. -->

* O destino mestre deve ter pelo menos um repositório de dados VMFS anexado. Se não houver nenhum, a entrada do **Repositório de Dados** na página de nova proteção estará vazia e você não poderá continuar.

* O servidor de destino mestre não pode ter instantâneos nos discos. Se houver instantâneos, a nova proteção e o failback falharão.

* O destino mestre não pode ter um controlador Paravirtual SCSI. O controlador só pode ser um controlador LSI Logic. Sem um controlador de Lógica LSI, a nova proteção falha.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Etapas para proteger novamente

> [!NOTE]
> Depois que uma máquina virtual é reinicializada no Azure, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, a nova proteção falhará e uma mensagem de erro indicará que o agente não está instalado. Aguarde alguns minutos e tente a nova proteção outra vez.



1. Em **Vault** > **Itens replicados**, clique com o botão direito do mouse na máquina virtual em que foi executado o failover e, em seguida, selecione **Proteger Novamente**. Também é possível clicar no computador e selecionar **Proteger Novamente** nos botões de comando.
2. Na folha, observe que a direção da proteção **Azure para Local** já está marcada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.
4. Para **Repositório de Dados**, selecione o repositório de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Clique em **OK** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se você quiser recuperar para um local alternativo (quando a máquina virtual local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo repositório de dados que o servidor de destino mestre. Então, uma nova máquina virtual é criada no vCenter.

Se você quiser recuperar a máquina virtual do Azure para uma máquina virtual local existente, monte os repositórios de dados da máquina virtual local com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.
    ![Proteger novamente a caixa de diálogo](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também é possível proteger novamente no nível de um plano de recuperação. Um grupo de replicação pode ser protegido novamente somente por meio de um plano de recuperação. Ao proteger novamente usando um plano de recuperação, você precisará fornecer os valores para cada computador protegido.

> [!NOTE]
> Use o mesmo servidor de destino mestre para proteger novamente um grupo de replicação. Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não poderá fornecer um ponto comum no tempo.

> [!NOTE]
> A máquina virtual local é desativada durante a nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação. Não ative a máquina virtual após o fim da nova proteção.

Depois que nova proteção for bem-sucedida, a máquina virtual entrará em um estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual entrou em um estado protegido, você poderá [iniciar um failback](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

O failback desligará a máquina virtual no Azure e inicializará a máquina virtual local. Espere algum tempo de inatividade do aplicativo. Escolha um horário para realizar failback quando o aplicativo puder tolerar o tempo de inatividade.

## <a name="common-problems"></a>Problemas comuns

* Se você usou um modelo para criar suas máquinas virtuais, verifique se cada máquina virtual tem seus próprios UUIDs para os discos. Se o UUID da máquina virtual local estiver em conflito com o do destino mestre, porque ambos foram criados com base no mesmo modelo, a nova proteção falhará. Implante outro destino mestre que não tenha sido criado com base no mesmo modelo.

* Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. Durante a nova proteção, o failover falha porque não é possível descobrir os armazenamentos de dados. Um sintoma é que os repositórios de dados não estão listados durante a nova proteção. Para resolver esse problema, você pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repetir o trabalho. Para obter mais informações, consulte [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Ao executar failback de uma máquina virtual Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. A desinstalação ocorre porque o pacote do Gerenciador de Rede é removido quando a máquina virtual é recuperada no Azure.

* Quando uma máquina virtual Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido do DHCP. Quando você executa o failover localmente, a máquina virtual continua a usar o DHCP para obter o endereço IP. Conecte-se manualmente à máquina e defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual Windows pode adquirir novamente seu endereço IP estático.

* Se você usa a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover tem êxito, mas não acontece. Para permitir o failback, atualize para a licença de avaliação dos programas.

* Se você não conseguir acessar o servidor de configuração do servidor de processo, use Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. O servidor de processo também deve ter uma pulsação quando for conectado ao servidor de configuração.

* Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter e o servidor de destino mestre estão descobertos. Um sintoma típico é que os armazenamentos de dados não ficam acessíveis ou visíveis na caixa de diálogo **Proteger Novamente**.

* Um servidor Windows Server 2008 R2 SP1 que é protegido, já que um servidor local físico não pode fazer failback do Azure para um site local.

### <a name="common-error-codes"></a>Códigos de erro comuns

#### <a name="error-code-95226"></a>Código de erro 95226

*A nova proteção falhou porque a máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local.*

Isso ocorre quando 
1. A máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local e, portanto, não pôde ser descoberta e registrada no servidor de configuração. 
2. O serviço de aplicativo do InMage Scout na máquina virtual do Azure que precisa estar em execução para comunicar-se com o servidor de configuração local pode não estar executando após o failover.

Para resolver esse problema
1. Você precisa verificar se a rede de máquina virtual do Azure está configurada de modo que a máquina virtual possa se comunicar com o servidor de configuração local. Para fazer isso, defina uma VPN Site a Site para seu datacenter local ou configure uma conexão ExpressRoute com emparelhamento privado na rede virtual da máquina virtual do Azure. 
2. Se você já tiver uma rede configurada de forma que a máquina virtual do Azure possa se comunicar com o servidor de configuração local, faça logon na máquina virtual e marque o 'Serviço de Aplicativo InMage Scout'. Se você observar que o Serviço de Aplicativo InMage Scout não está em execução, inicie o serviço manualmente e verifique se o tipo de inicialização do serviço está definido como Automático.

### <a name="error-code-78052"></a>Código de erro 78052
A nova proteção falha com a mensagem de erro: *Não foi possível concluir a proteção para a máquina virtual.*

Isso pode acontecer devido a duas razões
1. A máquina virtual que você está protegendo novamente é um Windows Server 2016. Atualmente, esse sistema operacional não tem suporte para failback, mas terá suporte em breve.
2. Já existe uma máquina virtual com o mesmo nome no servidor de destino Mestre para o qual você está fazendo failback.

Para resolver esse problema, você pode selecionar um servidor de destino mestre diferente em um host diferente, de modo que a nova proteção crie a máquina em um host diferente no qual não ocorra conflito entre os nomes. Você também pode fazer vMotion do destino mestre para um host diferente no qual não ocorra conflito entre os nomes. Se a máquina virtual existente for uma máquina isolada, você pode apenas renomeá-la para que a nova máquina virtual possa ser criada no mesmo host ESXi.

### <a name="error-code-78093"></a>Código de erro 78093

*A VM não está em execução, está em um estado suspenso ou não está acessível.*

Para proteger novamente uma máquina virtual que sofreu failover de volta para o local, é necessário que a máquina virtual do Azure esteja em execução. Isso é para que o serviço de mobilidade registre-se no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador estiver em uma rede incorreta ou não estiver em execução (estado suspenso ou desligado), o servidor de configuração não poderá acessar o serviço de mobilidade na máquina virtual para iniciar a nova proteção. Você pode reiniciar a máquina virtual para que ela possa começar a comunicar-se de volta localmente. Reiniciar o trabalho de nova proteção depois de iniciar a máquina virtual do Azure

### <a name="error-code-8061"></a>Código de erro 8061

*A loja de dados não está acessível no host ESXi.*

Consulte os [pré-requisitos de destino](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e as [lojas de dados de suporte](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para failback

