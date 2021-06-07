---
title: Mover VMs do Azure entre regiões governamentais e públicas com o Azure Site Recovery
description: Use o Azure Site Recovery para mover VMs do Azure entre regiões governamentais e públicas do Azure.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425340"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Mover VMs do Azure entre regiões públicas e do Azure Governamental 

Você talvez queira mover suas VMs de IaaS entre regiões públicas e do Azure Governamental para aumentar a disponibilidade de suas VMs existentes, melhorar a capacidade de gerenciamento, ou por motivos de governança, conforme detalhado [aqui](azure-to-azure-move-overview.md).

Além de usar o serviço [Azure Site Recovery](site-recovery-overview.md) para gerenciar e coordenar a recuperação de desastres de máquinas locais e VMs do Azure para fins de continuidade de negócios e recuperação de desastres (BCDR), você também pode usar o Site Recovery para gerenciar a transferência das VMs do Azure para uma região secundária.       

Este tutorial mostra como mover VMs do Azure entre regiões públicas e do Azure Governamental usando o Azure Site Recovery. O mesmo pode ser estendido para mover VMs entre pares de regiões que não estão dentro do mesmo cluster geográfico. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar os dados para a região de destino
> * Testar a configuração
> * Realizar a movimentação
> * Descartar os recursos na região de origem

> [!IMPORTANT]
> Este tutorial mostra como mover VMs do Azure entre regiões públicas e do Azure Governamental, ou entre pares de regiões que não são compatíveis com a solução de recuperação de desastre comum para VMs do Azure. Se seus pares de regiões de origem e destino são [compatíveis](./azure-to-azure-support-matrix.md#region-support), consulte esse [documento](azure-to-azure-tutorial-migrate.md) para informações sobre a movimentação. Se o requisito for melhorar a disponibilidade movendo as VMs em um conjunto de disponibilidade para VMs fixas em zonas em outra região, veja o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Não é aconselhável usar esse método para configurar recuperação de desastre entre pares de região não compatíveis, pois os pares são definidos com latência de dados em mente, o que é essencial para um cenário de recuperação de desastre.

## <a name="verify-prerequisites"></a>Verificar pré-requisitos

> [!NOTE]
> Verifique se você entende os [componentes e a arquitetura](physical-azure-architecture.md) para este cenário. Essa arquitetura será usada para mover VMs do Azure, **tratando as VMs como servidores físicos**.

- Examine os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Verifique se os servidores que você deseja replicar estão em conformidade com os [requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare uma conta para a instalação automática do serviço de Mobilidade em cada servidor que você deseja replicar.

- Observe que, depois de fazer failover para a região de destino no Azure, você não poderá executar um failback diretamente de volta para a região de origem. Você precisará configurar a replicação novamente para o destino.

### <a name="verify-azure-account-permissions"></a>Verificar as permissões de conta do Azure

Verifique se sua conta do Azure tem permissões para a replicação de VMs para o Azure.

- Examine as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) você precisa para replicar máquinas para o Azure.
- Verifique e modifique permissões do [Azure RBAC (controle de acesso baseado em função do Azure)](../role-based-access-control/role-assignments-portal.md). 

### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Configure uma [rede do Azure](../virtual-network/quick-create-portal.md) de destino.

- As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação


### <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

Configure uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md).

- O Site Recovery replica máquinas locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após o failover.
- A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.


## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado em cada servidor que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para o servidor. Para instalar automaticamente, você precisa preparar uma conta que o Site Recovery usará para acessar o servidor.

- Você pode usar uma conta local ou de domínio
- Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuários Remotos no computador local. Para fazer isso, no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
- Para adicionar a entrada do Registro para desabilitar a configuração de uma CLI, digite:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, a conta deve ser a raiz no servidor Linux de origem.


## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Verifique se a sua assinatura do Azure permite criar VMs na região de destino usada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a VMs com tamanhos que correspondem às VMs de origem. Se você estiver usando o Site Recovery para copiar os dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

3. Crie um recurso de destino para cada componente identificado no layout da rede de origem. Isso é importante para garantir que, após a substituição para a região de destino, as VMs tenham todos os recursos e as funcionalidades que tinham na origem.

    > [!NOTE]
    > O Azure Site Recovery descobre automaticamente e cria uma rede virtual quando você habilita a replicação para a VM de origem. Se preferir, você também poderá pré-criar uma rede e atribuí-la à VM no fluxo de usuário para habilitar a replicação. Porém, para outros recursos, conforme mencionado abaixo, você precisará criá-los manualmente na região de destino.

     Consulte os documentos a seguir para criar os recursos de rede mais comumente usados que são relevantes para você, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
    - [Balanceadores de carga](../load-balancer/index.yml)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
    Para outros componentes de rede, veja a [documentação](../index.yml?pivot=products&panel=network) da rede.

4. [Crie uma rede de não produção](../virtual-network/quick-create-portal.md) manualmente na região de destino caso deseje testar a configuração antes de realizar a substituição final para a região de destino. Isso criará interferência mínima com a produção e é recomendado.

## <a name="copy-data-to-the-target-region"></a>Copiar os dados para a região de destino
As etapas abaixo mostrarão como usar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Caso você tenha mais de uma a. assinatura, selecione a assinatura apropriada.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres dos Serviços de Recuperação, clique em **Visão Geral** > **ConsotoVMVault** >  **+Replicar**
7. Selecione **Para o Azure** > **Não virtualizado/Outro**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configure o servidor de configuração para descobrir VMs.


Defina o servidor de configuração, registre-o no cofre e descubra VMs.

1. Clique em **Site Recovery** > **Preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Antes de começar, faça o seguinte: 

#### <a name="verify-time-accuracy"></a>Verificar a precisão de tempo
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema esteja sincronizado com um [Servidor de Horário](/windows-server/networking/windows-time-service/windows-time-service-top). Ele deve ser correspondente. Se ele estiver 15 minutos adiantado ou atrasado, a instalação poderá falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Verifique se o computador pode acessar estas URLs com base no ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereço IP devem permitir a comunicação com todas as URLs do Azure listadas acima pela porta HTTPS (443). Para simplificar e limitar os Intervalos de IP, é recomendável fazer a filtragem de URL.

- **IPs comerciais** – permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443). Permita que os intervalos de endereços IP para a região do Azure de sua assinatura deem suporte ao AAD, ao Backup, à Replicação e às URLs de Armazenamento.  
- **IPs Governamentais** – permita os [Intervalos de IP do Datacenter do Azure Governamental](https://www.microsoft.com/en-us/download/details.aspx?id=57063) e a porta HTTPS (443) em todas as regiões US Gov (Virgínia, Texas, Arizona e Iowa) para dar suporte ao AAD, ao Backup, à Replicação e às URLs de Armazenamento.  

#### <a name="run-setup"></a>Executar a instalação
Execute a Instalação Unificada como Administrador Local, para instalar o servidor de configuração. O servidor de processo e o servidor de destino mestre também são instalados, por padrão, no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registro, o servidor de configuração será exibido na página **Configurações** > **Servidores** no cofre.

### <a name="configure-target-settings-for-replication"></a>Definir as configurações de destino para replicação

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO (objetivo de pontos de recuperação). Esse valor especifica com que frequência os pontos de recuperação de dados são criados. Um alerta será gerado se a replicação contínua exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique qual será a duração (em horas) da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência do instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, uma política de failback **rep-policy-failback** será criada. Essa política não é usada até você iniciar um failback do Azure.

### <a name="enable-replication"></a>Habilitar a replicação

- O Site Recovery instalará o serviço de Mobilidade quando a replicação for habilitada.
- Quando você habilitar a replicação para um servidor, poderá levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

1. Clique em **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas físicas**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. No **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recurso).
6. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.
8. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. 
9. Em **Computadores físicos**, clique em **+ Computador físico**. Especifique o nome e o endereço IP. Selecione o sistema operacional do computador que você deseja replicar. Levará alguns minutos para que os servidores sejam descobertos e listados. 

   > [!WARNING]
   > Você precisa inserir o endereço IP da VM do Azure que você pretende mover

10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. 
12. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.


Para monitorar os servidores adicionados, você pode verificar o horário da última descoberta em **Servidores de Configuração** > **Último Contato Em**. Para adicionar computadores sem esperar por um período de descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue para o cofre, em **Configurações** > **Itens replicados**, clique na Máquina Virtual que pretende mover para a região de destino e clique no ícone **+Failover de Teste**.
2. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

   - **Processado mais recente**: falha na VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, ela fornece um RTO (Objetivo do Tempo de Recuperação) baixo
   - **Consistente com o aplicativo mais recente**: essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizar**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino do Azure para a qual deseja mover as VMs do Azure para testar a configuração. 

   > [!IMPORTANT]
   > Recomendamos que você use uma rede separada de VMs do Azure para o failover de teste, não a rede de produção para a qual deseja mover as VMs mais tarde que foi configurada quando você habilitou a replicação.

4. Para começar a testar a movimentação, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Caso deseje excluir a VM criada como parte do teste da movimentação, clique em **Limpar failover de teste** no item replicado. Em **Observações**, registre e salve as observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Realize a movimentação para a região de destino e confirme-a.

1. Navegue para o cofre, em **Configurações** > **Itens replicados**, clique na máquina virtual e, em seguida, clique em **Failover**.
2. Em **Failover**, selecione **Mais recente**. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** . 
4. Depois que o trabalho for concluído, verifique se a VM é exibida na região de destino do Azure conforme esperado.
5. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Confirmar**. Isso concluirá o processo de movimentação para a região de destino. Aguarde até o trabalho de confirmação ser concluído.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região de origem 

- Navegue para a VM.  Clique em **Desabilitar Replicação**.  Isso interromperá o processo de cópia dos dados para a VM.  

   > [!IMPORTANT]
   > É importante executar esta etapa para evitar a cobrança pela replicação do ASR.

Caso não tenha planos de reutilizar os recursos de origem, continue com o próximo conjunto de etapas.

1. Continue e exclua todos os recursos de rede relevantes da região de origem listados como parte da Etapa 4 em [Preparar as VMs de origem](#prepare-the-source-vms) 
2. Exclua a conta de armazenamento correspondente da região de origem.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma VM do Azure para uma região diferente dele. Agora é possível configurar a recuperação de desastres para VMs do Azure movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)
