---
title: Automatizar a DR de compartilhamento de arquivos do StorSimple com o Azure Site Recovery | Microsoft Docs
description: "Descreve as etapas e práticas recomendadas para criar uma solução de recuperação de desastre para compartilhamentos de arquivos hospedados no armazenamento do Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/09/2017
ms.author: vidarmsft
ms.openlocfilehash: b4d575587eec1bcf43c33c7faeb8360ec67b5214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução de recuperação de desastre automatizada usando o Azure Site Recovery para compartilhamentos de arquivos hospedados no StorSimple
## <a name="overview"></a>Visão geral
O Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrida que resolve as complexidades de dados não estruturados comumente associados aos compartilhamentos de arquivos. O StorSimple usa o armazenamento em nuvem como uma extensão da solução local e dispõe os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem. A proteção de dados integrada, com instantâneos locais e de nuvem, elimina a necessidade de uma infraestrutura de armazenamento ampla.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) é um serviço baseado no Azure que fornece recursos de DR (recuperação de desastre) por meio da orquestração de replicação, de failover e da recuperação de máquinas virtuais. O Azure Site Recovery dá suporte para várias tecnologias de replicação, com o intuito de replicar, proteger e fazer o failover de forma consistente das máquinas virtuais e dos aplicativos em nuvens privadas/públicas ou hospedadas.

Ao usar o Azure Site Recovery, a replicação de máquina virtual e os recursos de instantâneo de nuvem do StorSimple, você pode proteger o ambiente completo do servidor de arquivos. Caso haja uma interrupção, você poderá usar um clique simples para colocar seus compartilhamentos de arquivos online no Azure em apenas alguns minutos.

Este documento explica detalhadamente como você pode criar uma solução de recuperação de desastre para seus compartilhamentos de arquivos hospedados no armazenamento do StorSimple e executar failovers planejados, não planejados e de teste usando um plano de recuperação de um clique. Basicamente, ele mostra como você pode modificar o plano de recuperação em seu cofre do Azure Site Recovery para habilitar failovers do StorSimple em cenários de desastre. Além disso, ele descreve as configurações e pré-requisitos com suporte. Este documento presume que você esteja familiarizado com os fundamentos básicos do Azure Site Recovery e com as arquiteturas do StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implantação com suporte do Azure Site Recovery
Os clientes podem implantar servidores de arquivos como servidores físicos ou VMs (máquinas virtuais) em execução no Hyper-V ou no VMware e, então, criar compartilhamentos de arquivos de volumes configurados fora do armazenamento do StorSimple. O Azure Site Recovery pode proteger as implantações físicas e virtuais em um site secundário ou no Azure. Este documento aborda os detalhes de uma solução de DR com o Azure como o site de recuperação para uma VM de servidor de arquivos hospedada no Hyper-V e com compartilhamentos de arquivos no armazenamento do StorSimple. Outros cenários nos quais a VM do servidor de arquivos está em uma VM do VMware ou em um computador físico podem ser implementados de maneira semelhante.

## <a name="prerequisites"></a>Pré-requisitos
A implementação de uma solução de recuperação de desastre de um clique, que usa o Azure Site Recovery para compartilhamentos de arquivos hospedados no armazenamento do StorSimple, tem os seguintes pré-requisitos:

* VM de servidor de arquivo do Windows Server 2012 R2 local hospedada no Hyper-V, no VMware ou em um computador físico
* Dispositivo de armazenamento do StorSimple local registrado com o Azure StorSimple Manager
* Dispositivo de nuvem do StorSimple criado no StorSimple Manager do Azure (ele pode ser mantido no estado de desligamento)
* Compartilhamentos de arquivos hospedados em volumes configurados no dispositivo de armazenamento do StorSimple
* [Cofre de serviços do Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) criado em uma assinatura do Microsoft Azure

Além disso, se o Azure for seu site de recuperação, execute a [ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) nas VMs, a fim de garantir que elas sejam compatíveis com as VMs do Azure e com os serviços do Azure Site Recovery.

Para evitar problemas de latência (que podem resultar em custos mais altos), certifique-se de criar seu dispositivo de nuvem do StorSimple, a conta de automação e as contas de armazenamento na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Habilitar a DR para os compartilhamentos de arquivos do StorSimple
Cada componente do ambiente local precisa ser protegido para habilitar a replicação e a recuperação completas. Esta seção descreve como:

* Configurar o Active Directory e a replicação do DNS (opcional)
* Use o Azure Site Recovery para habilitar a proteção da VM do servidor de arquivos
* Habilitar a proteção de volumes do StorSimple
* Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar o Active Directory e a replicação do DNS (opcional)
Se você quiser proteger os computadores que executam o Active Directory e o DNS para que fiquem disponíveis no site de DR, você precisará protegê-los explicitamente (de modo que os servidores de arquivos sejam acessíveis após a execução do failover com autenticação). Há duas opções recomendadas com base na complexidade do ambiente local do cliente.

#### <a name="option-1"></a>Opção 1
Se o cliente tem um número pequeno de aplicativos, um único controlador de domínio para todo o site local e eles estão apresentando falhas em todo o site, é recomendável usar a replicação do Azure Site Recovery para replicar o computador do controlador de domínio para um site secundário (isso é aplicável tanto de site a site quando de site para o Azure).

#### <a name="option-2"></a>Opção 2
Se o cliente tem um grande número de aplicativos, está executando uma floresta do Active Directory e se alguns aplicativos apresentam falhas de uma vez, é recomendável configurar um controlador de domínio adicional no site de DR (em um site secundário ou no Azure).

Confira [Automated DR solution for Active Directory and DNS using Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) (Solução de DR automatizada para o Active Directory e para o DNS usando o Azure Site Recovery) para obter instruções ao disponibilizar um controlador de domínio no site de DR. Para o restante deste documento, presumiremos que um controlador de domínio está disponível no site de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Use o Azure Site Recovery para habilitar a proteção da VM do servidor de arquivos
Esta etapa exige que você prepare o ambiente do servidor de arquivos local, crie e prepare um cofre do Azure Site Recovery e habilite a proteção de arquivo da VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente do servidor de arquivos local
1. Defina o **Controle de Conta de Usuário** para **Nunca Notificar**. Isso é necessário para que você possa usar os scripts de Automação do Azure para conectar os destinos iSCSI após fazer failover por meio do Azure Site Recovery.

   1. Pressione a tecla Windows +Q e pesquise por **UAC**.
   2. Selecione **Alterar configurações de Controle de Conta de Usuário**.
   3. Arraste a barra para a parte inferior até **Nunca Notificar**.
   4. Clique em **OK** e, então, selecione **Sim** quando solicitado.

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. Instale o agente de VM em cada uma das VMs do servidor de arquivos. Isso é necessário para que você possa executar os scripts de Automação do Azure nas máquinas virtuais que foram submetidas ao failover.

   1. [Baixar o agente](http://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.
   2. Abra o Windows PowerShell no modo de Administrador (Executar como Administrador) e, em seguida, digite o seguinte comando para navegar até o local de download:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > O nome do arquivo poderá mudar dependendo da versão.
      >
      >
3. Clique em **Avançar**.
4. Aceite os **Termos do Contrato** e, em seguida, clique em **Avançar**.
5. Clique em **Concluir**.
6. Crie os compartilhamentos de arquivos usando volumes criados fora do armazenamento do StorSimple. Para obter mais informações, confira [Usar o serviço StorSimple Manager para gerenciar volumes](storsimple-manage-volumes.md).

   1. Em suas VMs locais, pressione a tecla Windows +Q e pesquise por **iSCSI**.
   2. Selecione o **iniciador iSCSI**.
   3. Selecione a guia **Configuração** e copie o nome do iniciador.
   4. Faça logon no [Portal do Azure](https://portal.azure.com/).
   5. Selecione a guia **StorSimple** e, então, o serviço StorSimple Manager que contém o dispositivo físico.
   6. Crie contêineres de volume e, em seguida, crie volumes. (Esses volumes são para os compartilhamentos de arquivos nas VMs do servidor de arquivos). Copie o nome do iniciador e dê um nome apropriado para os Registros de Controle de Acesso ao criar os volumes.
   7. Selecione a guia **Configurar** e anote o endereço IP do dispositivo.
   8. Em suas VMs locais, acesse o **iniciador iSCSI** novamente e digite o IP na seção Conexão Rápida. Clique em **Conexão Rápida** (o dispositivo agora deve estar conectado).
   9. Abra o Portal do Azure e selecione a guia **Volumes e Dispositivos** . Clique em **Configuração Automática**. O volume que você acabou de criar deverá aparecer.
   10. No portal, selecione a guia **Dispositivos** e, em seguida, selecione **Criar um Novo Dispositivo Virtual.** (Este dispositivo virtual será usado se ocorrer um failover). Esse novo dispositivo virtual pode ser mantido em estado offline para evitar custos extras. Para colocar o dispositivo virtual offline, acesse a seção **Máquinas Virtuais** no Portal e desligue-o.
   11. Volte para as VMs locais e abra o Gerenciamento de Disco (pressione a tecla Windows + X e selecione **Gerenciamento de Disco**).
   12. Você notará alguns discos extras (dependendo do número de volumes que você criou). Clique com o botão direito do mouse no primeiro, selecione **Inicializar Disco** e selecione **OK**. Clique com o botão direito do mouse na seção **Não Alocado**, selecione **Novo Volume Simples**, atribua uma letra da unidade e conclua o assistente.
   13. Repita a etapa l para todos os discos. Agora você pode ver todos os discos **neste computador** no Windows Explorer.
   14. Use a função Serviços de Arquivo e Armazenamento para criar compartilhamentos de arquivos nesses volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar um cofre do Azure Site Recovery
Confira a [documentação do Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a usá-lo antes de proteger a VM do servidor de arquivos.

#### <a name="to-enable-protection"></a>Para habilitar a proteção
1. Desconecte os destinos iSCSI das VMs locais que você deseja proteger por meio do Azure Site Recovery:

   1. Pressione a tecla Windows + Q e pesquise por **iSCSI**.
   2. Selecione **Configurar o iniciador iSCSI**.
   3. Desconecte o dispositivo do StorSimple que você conectou anteriormente. Como alternativa, você pode desativar o servidor de arquivos por alguns minutos ao habilitar a proteção.

   > [!NOTE]
   > Isso fará com que os compartilhamentos de arquivos fiquem temporariamente indisponíveis.
   >
   >
2. [Habilitar a proteção da máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) da VM do servidor de arquivos do portal do Azure Site Recovery.
3. Quando a sincronização inicial começar, você poderá reconectar o destino novamente. Acesse o iniciador iSCSI, selecione o dispositivo StorSimple e clique em **Conectar**.
4. Quando a sincronização for concluída e o status da VM estiver como **Protegida**, selecione a VM, depois a guia **Configurar** e atualize a rede da VM adequadamente (essa é a rede da qual as VMs submetidas ao failover farão parte). Se a rede não aparecer, isso significará que a sincronização ainda está em execução.

### <a name="enable-protection-of-storsimple-volumes"></a>Habilitar a proteção de volumes do StorSimple
Se você não tiver selecionado a opção **Habilitar um backup padrão para este volume** para os volumes do StorSimple, acesse as **Políticas de Backup** no serviço StorSimple Manager e crie uma política de backup adequada para todos os volumes. É recomendável que você defina a frequência dos backups para o RPO (Objetivo de Ponto de Recuperação) que você gostaria de ver no aplicativo.

### <a name="configure-the-network"></a>Configurar a rede
Para o servidor de arquivos da VM, defina as configurações de rede no Azure Site Recovery para que as redes da VM sejam conectadas à rede correta de DR após o failover.

Você pode selecionar a VM na guia **Itens replicados** para definir as configurações de rede, conforme mostrado na ilustração a seguir.

![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Você pode criar um plano de recuperação no ASR para automatizar o processo de failover de compartilhamentos de arquivos. Se ocorrer uma interrupção, você poderá exibir os compartilhamentos de arquivos em poucos minutos com apenas um clique simples. Para habilitar essa automação, você precisará de uma conta de Automação do Azure.

#### <a name="to-create-an-automation-account"></a>Para criar uma conta de Automação
1. Vá para o Portal do Azure, &gt; seção **Automação**.
2. Clique no botão **+ Adicionar** e a folha abaixo é aberta.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Nome – insira uma nova conta de automação
   * Assinatura – escolha uma assinatura
   * Grupo de recursos – crie um novo grupo de recursos ou escolha um grupo de recursos existente
   * Localização – escolha uma localização e mantenha-a na mesma área geográfica/região em que as Contas de Armazenamento e o Dispositivo de Nuvem StorSimple foram criados.
   * Criar conta Executar Como do Azure – selecione a opção **Sim**.

3. Vá para a conta Automação, clique em **Runbooks** &gt; **Procurar na Galeria** para importar todos os runbooks necessários para a conta de automação.
4. Adicione os seguintes runbooks ao localizar a marcação **Recuperação de Desastre** na galeria:

   * Limpe os volumes do StorSimple após o TFO (failover de teste)
   * Faça failover de contêineres de volume do StorSimple
   * Monte volumes no dispositivo StorSimple após o failover
   * Desinstale a extensão de script personalizada na VM do Azure
   * Inicie a solução de virtualização StorSimple

     ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. Publique todos os scripts, selecionando o runbook na conta de automação e clique em **Editar** &gt; **Publicar** e, em seguida, em **Sim** para a mensagem de verificação. Após essa etapa, a guia **Runbooks** será exibida da seguinte maneira:

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. Na conta de automação, selecione a guia **Ativos** &gt;, clique em **Variáveis** &gt; **Adicionar Variável** e adicione as seguintes variáveis. Você pode optar por criptografar esses ativos. Essas variáveis são específicas do plano de recuperação. Se o nome do seu plano de recuperação (que você criará na próxima etapa) for TestPlan, as variáveis deverão ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName e assim por diante.

   * *RecoveryPlanName***-StorSimRegKey**: a chave de registro para o serviço StorSimple Manager.
   * *RecoveryPlanName***- AzureSubscriptionName**: o nome da assinatura do Azure.
   * *RecoveryPlanName***-ResourceName**: o nome do recurso do StorSimple que tem o dispositivo StorSimple.
   * *RecoveryPlanName***-DeviceName**: o dispositivo que precisa ser submetido ao failover.
   * *RecoveryPlanName***-VolumeContainers**: uma cadeia de caracteres separada por vírgulas dos contêineres de volume presentes no dispositivo que precisa ser submetido ao failover; por exemplo, volcon1, volcon2, volcon3.
   * *RecoveryPlanName***-TargetDeviceName**: o dispositivo de nuvem do StorSimple no qual os contêineres serão submetidos ao failover.
   * *RecoveryPlanName***-TargetDeviceDnsName**: o nome do dispositivo de destino (ele pode ser encontrado na seção **Máquina Virtual**: o nome do serviço é o mesmo que o nome DNS).
   * *RecoveryPlanName***-StorageAccountName**: o nome da conta de armazenamento na qual o script (que precisa ser executado na VM submetida ao failover) será armazenado. Isso pode ser qualquer conta de armazenamento que tenha espaço para armazenar o script temporariamente.
   * *RecoveryPlanName***-StorageAccountKey**: a tecla de acesso da conta de armazenamento acima.
   * *RecoveryPlanName***-ScriptContainer**: o nome do contêiner no qual o script será armazenado na nuvem. Se o contêiner não existir, ele será criado.
   * *RecoveryPlanName***-VMGUIDS**: ao proteger uma VM, o Azure Site Recovery atribui uma identificação exclusiva para todas as VMs, que fornece os detalhes sobre a VM submetida ao failover. Para obter o VMGUID, selecione a guia **Serviços de Recuperação** e clique em **Item protegido** &gt; **Grupos de Proteção** &gt; **Computadores** &gt; **Propriedades**. Se você tiver várias VMs, adicione os GUIDs como uma cadeia de caracteres separada por vírgulas.
   * *RecoveryPlanName***-AutomationAccountName** – O nome da conta de automação na qual você adicionou os runbooks e os ativos.

  Por exemplo, se o nome do plano de recuperação for fileServerpredayRP, então, as suas guias **Credenciais** & **Variáveis** deverão aparecer da seguinte forma após a adição de todos os recursos.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Acesse a seção **Serviços de Recuperação** e selecione o cofre do Azure Site Recovery que você criou anteriormente.
8. Selecione a opção **Planos de Recuperação (Recuperação de Site)** do grupo **Gerenciar** e crie um novo plano de recuperação, conforme a seguir:

   a.  Clique no botão **+ Plano de recuperação**, abra a folha abaixo.

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Insira um nome de plano de recuperação, escolha valores de modelo de Implantação, Destino e Origem.

   c.  Selecione as VMs do grupo de proteção que você deseja incluir no plano de recuperação e clique no botão **OK**.

   d.  Selecione o plano de recuperação que você criou anteriormente, clique no botão **Personalizar** para abrir a exibição de personalização do plano de recuperação.

   e.  Clique com o botão direito em **Desligamento de todos os grupos** e clique em **Adicionar ação prévia**.

   f.  Abra a folha de ação Inserir, insira um nome, selecione a opção **Lado primário** na opção Onde executar, selecione a conta de Automação (na qual os runbooks foram adicionados) e, em seguida, selecione o runbook **Failover-StorSimple-Volume-Containers**.

   g.  Clique com o botão direito em **Grupo 1: Iniciar** e clique na opção **Adicionar itens protegidos** e selecione as VMs que devem ser protegidas no plano de recuperação e clique no botão **OK**. Opcional, caso as VMs já estejam selecionadas.

   h.  Clique com o botão direito em **Grupo 1: Iniciar** e clique na opção **Ação posterior** e adicione os seguintes scripts:

   * Runbook Start-StorSimple-Virtual-Appliance
   * Runbook Fail over-StorSimple-volume-containers
   * Runbook mount-volumes-after-failover
   * Runbook uninstall-custom-script-extension

   i.  Adicione uma ação manual após os quatro scripts acima na mesma seção **Grupo 1: etapas posteriores** . Essa ação é o ponto em que você pode verificar se tudo está funcionando corretamente. Essa ação precisa ser adicionada somente como parte do Failover de teste (portanto, selecione apenas a caixa de seleção **Failover de Teste** ).

   j.  Após a ação manual, adicione o script **Limpeza**, usando o mesmo procedimento utilizado para os outros runbooks. **Salvar** o plano de recuperação.

    > [!NOTE]
    > Ao executar um failover de teste, você deverá verificar tudo na etapa de ação manual porque os volumes do StorSimple que tinham sido clonados no dispositivo de destino serão excluídos como parte da limpeza após a ação manual.
    >

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Executar um failover de teste
Confira o guia complementar [Solução de DR do Active Directory](../site-recovery/site-recovery-active-directory.md) para considerações específicas sobre o Active Directory durante o failover de teste. A configuração local não será afetada quando o failover de teste ocorrer. Os volumes do StorSimple que foram anexados à VM local são clonados para o dispositivo de nuvem do StorSimple no Azure. Uma VM para fins de teste é colocada no Azure e os volumes clonados são anexados à VM.

#### <a name="to-perform-the-test-failover"></a>Para executar o failover de teste
1. No Portal do Azure, selecione seu cofre de recuperação de site.
2. Clique no plano de recuperação criado para a VM do servidor de arquivos.
3. Clique em **Failover de Teste**.
4. Selecione a rede virtual do Azure à qual as VMs do Azure serão conectadas após o failover.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. Clique em **OK** para iniciar o failover. É possível acompanhar o progresso, clicando na VM para abrir suas propriedades ou no **Trabalho de failover de teste** no nome do cofre &gt; **Trabalhos** &gt; **Trabalhos do Site Recovery**.
6. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure &gt; **Máquinas Virtuais**. Você pode executar suas validações.
7. Depois que as validações forem feitas, clique em **Validações Completas**. Isso limpará os Volumes StorSimple e desligará o Dispositivo de Nuvem StorSimple.
8. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em Observações, registre e salve todas as observações associadas ao failover de teste. Isso excluirá a máquina virtual que foi criada durante o teste de failover.

## <a name="perform-a-planned-failover"></a>Faça um failover planejado
   Durante um failover planejado, a VM do servidor de arquivos local é desligada normalmente e os instantâneos de backup de nuvem dos volumes no dispositivo StorSimple são criados. Os volumes do StorSimple são submetidos ao failover para o dispositivo virtual, uma réplica da VM é colocada no Azure e os volumes são associados à VM.

#### <a name="to-perform-a-planned-failover"></a>Para executar um failover planejado
1. No portal do Azure, selecione o cofre **Serviços de Recuperação** &gt; **Planos de Recuperação (Recuperação de Site)** &gt; **nome_planoderecuperação** criado para a VM do servidor de arquivos.
2. Na folha do plano de Recuperação, Clique em **Mais** &gt;  **Failover Planejado**.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. Na folha **Confirmar Failover Planejado**, escolha os locais de origem e destino, selecione a rede de destino e clique no ícone de seleção ✓ para iniciar o processo de failover.
4. Depois que as máquinas virtuais de réplica são criadas, elas ficam em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
5. Depois que a replicação é concluída, as máquinas virtuais são iniciadas no local secundário.

## <a name="perform-a-failover"></a>Executar um failover
Durante um failover não planejado, os volumes do StorSimple serão submetidos ao failover para o dispositivo virtual, uma réplica da VM será colocada no Azure e os volumes serão associados à VM.

#### <a name="to-perform-a-failover"></a>Executar um failover
1. No portal do Azure, selecione o cofre **Serviços de Recuperação** &gt; **Planos de Recuperação (Recuperação de Site)** &gt; **nome_planoderecuperação** criado para a VM do servidor de arquivos.
2. Na folha do plano de Recuperação, Clique em **Mais** &gt;  **Failover**.
3. Na folha **Confirmar Failover** , escolha os locais de origem e de destino.
4. Selecione **Desligue as máquinas virtuais e sincronize os dados mais recentes** para especificar que a Recuperação de Site deve tentar desligar a máquina virtual protegida e sincronizar os dados para que ocorra o failover da versão mais recente dos dados.
5. Após o failover, as máquinas virtuais entram em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.


## <a name="perform-a-failback"></a>Executar um failback
Durante um failback, os contêineres de volume do StorSimple são submetidos ao failover para o dispositivo físico depois que o backup é feito.

#### <a name="to-perform-a-failback"></a>Para executar um failback
1. No portal do Azure, selecione o cofre **Serviços de Recuperação** &gt; **Planos de Recuperação (Recuperação de Site)** &gt; **nome_planoderecuperação** criado para a VM do servidor de arquivos.
2. Na folha do plano de Recuperação, Clique em **Mais** &gt;  **Failover Planejado**.
3. Escolha os locais de origem e de destino, selecione as opções apropriadas de sincronização de Dados e de criação de VM.
4. Clique no botão **OK** para iniciar o processo de failback.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Práticas Recomendadas
### <a name="capacity-planning-and-readiness-assessment"></a>Planejamento da capacidade e avaliação de prontidão
#### <a name="hyper-v-site"></a>Site do Hyper-V
Use a [ferramenta Planejador de Capacidade do usuário](http://www.microsoft.com/download/details.aspx?id=39057) para projetar o servidor, o armazenamento e a infraestrutura de rede para o seu ambiente de Réplica do Hyper-V.

#### <a name="azure"></a>As tabelas
Você pode executar a [ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) nas VMs, a fim de garantir que elas sejam compatíveis com as VMs do Azure e com os serviços do Azure Site Recovery. A Ferramenta de Avaliação de Prontidão verifica as configurações da VM e avisa quando elas são incompatíveis com o Azure. Por exemplo, ela emitirá um aviso se uma unidade C: tiver mais do que 127 GB.

O planejamento de capacidade é composto de, pelo menos, dois processos importantes:

* Mapeamento das VMs do Hyper-V locais para obter os tamanhos de VM do Azure ( como A6, A7, A8 e A9).
* Determinação da largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações
* Atualmente, somente um dispositivo StorSimple pode ser submetido ao failover (para um único dispositivo de nuvem StorSimple). Ainda não há suporte para o cenário de um servidor de arquivos que abrange vários dispositivos StorSimple.
* Se houver um erro ao habilitar a proteção para uma VM, certifique-se de ter desconectado os destinos iSCSI.
* Todos os contêineres de volume que foram agrupados devido a abrangência das políticas de backup em contêineres de volume serão submetidos ao failover juntos.
* Todos os volumes nos contêineres de volume que você escolheu serão submetidos ao failover.
* Os volumes que somam mais de 64 TB não podem ser submetidos ao failover porque a capacidade máxima de um único dispositivo de nuvem do StorSimple é de 64 TB.
* Não limpe as VMs se o failover planejado/não planejado falhar e as VMs forem criadas no Azure. Em vez disso, faça um failback. Se você excluir as VMs, as VMs locais não poderão ser ativadas novamente.
* Após um failover, se você não conseguir ver os volumes, acesse as VMs, abra o Gerenciamento de Disco, examine os discos novamente e coloque-os online.
* Em alguns casos, as letras de unidade no site de DR podem ser diferentes das letras locais. Se isso ocorrer, você precisará corrigir o problema manualmente depois que o failover for concluído.
* O Multi-Factor Authentication deve ser desabilitado para a credencial do Azure que foi inserida na conta de automação como um ativo. Se essa autenticação não for desabilitada, os scripts não poderão ser executados automaticamente e o plano de recuperação falhará.
* Tempo limite do trabalho de failover: o script do StorSimple atingirá o tempo limite se o failover dos contêineres de volume levar mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).
* Tempo limite do trabalho de backup: o script do StorSimple atingirá o tempo limite se o backup de volume levar mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).

  > [!IMPORTANT]
  > Execute o backup manualmente no Portal do Azure e, em seguida, execute o plano de recuperação novamente.

* Tempo limite do trabalho de clonagem: o script do StorSimple atingirá o tempo limite se a clonagem de volumes levar mais tempo do que o limite do Azure Site Recovery por script (atualmente 120 minutos).
* Erro de sincronização de tempo: os erros de script do StorSimple informando que os backups não foram bem-sucedidos, mesmo que o backup tenha sido bem-sucedido no portal. Uma causa possível para isso seria que a hora do dispositivo StorSimple não estaria sincronizada com a hora atual no fuso horário.

  > [!IMPORTANT]
  > Sincronize a hora do dispositivo com a hora atual no fuso horário.

* Erro de failover do dispositivo: o script do StorSimple poderá falhar se houver um failover de dispositivo quando o plano de recuperação estiver em execução.

  > [!IMPORTANT]
  > Execute o plano de recuperação novamente após concluir o failover do dispositivo.


## <a name="summary"></a>Resumo
Ao usar o Azure Site Recovery, você poderá criar um plano de recuperação de desastre automatizado completo para um servidor de arquivos de VM com compartilhamentos de arquivos hospedados no armazenamento do StorSimple. Você poderá iniciar o failover em segundos e de qualquer lugar, caso haja uma interrupção, e fazer o aplicativo funcionar em alguns minutos.
