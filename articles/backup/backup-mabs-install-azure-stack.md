---
title: Instalar Servidor de Backup do Azure no Azure Stack
description: Neste artigo, saiba como usar o Servidor de Backup do Azure para proteger ou fazer backup de cargas de trabalho no Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 12dfd15c2bd43816dd361fdf45995bcbcd6fba56
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986998"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalar Servidor de Backup do Azure no Azure Stack

Este artigo explica como instalar o Servidor de Backup do Azure no Azure Stack. Com o Servidor de Backup do Azure, você pode proteger cargas de trabalho de IaaS (infraestrutura como serviço), como máquinas virtuais em execução no Azure Stack. Um benefício de usar o Servidor de Backup do Azure para proteger suas cargas de trabalho é que você pode gerenciar a proteção de todas as cargas de trabalho em um único console.

> [!NOTE]
> Para saber mais sobre as funcionalidades de segurança, consulte a [documentação dos recursos de segurança do Backup do Azure](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Servidor de Backup do Azure

O Servidor de Backup do Azure protege as seguintes cargas de trabalho de máquina virtual do Azure Stack.

| Fonte de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Canal Semestral do Windows Server – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| SQL Server 2016 | Banco de dados |
| SQL Server 2014 | Banco de dados |
| SQL Server 2012 SP1 | Banco de dados |
| SharePoint 2016 | Farm, banco de dados, front-end, servidor Web |
| SharePoint 2013 | Farm, banco de dados, front-end, servidor Web |
| SharePoint 2010 | Farm, banco de dados, front-end, servidor Web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente do Servidor de Backup do Azure

Considere as recomendações nesta seção ao instalar o Servidor de Backup do Azure em seu ambiente do Azure Stack. O instalador do Servidor de Backup do Azure verifica que seu ambiente tem os pré-requisitos necessários, mas você economizará tempo se preparando antes de instalar.

### <a name="determining-size-of-virtual-machine"></a>Determinar o tamanho da máquina virtual

Para executar o Servidor de Backup do Azure em uma máquina virtual do Azure Stack, use o tamanho A2 ou maior. Para obter assistência na escolha do tamanho da máquina virtual, baixe a [calculadora de tamanho da VM do Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuais nas máquinas virtuais do Azure Stack

Todas as máquinas virtuais usadas em uma carga de trabalho do Azure Stack devem pertencer à mesma Rede Virtual e Assinatura do Azure.

### <a name="azure-backup-server-vm-performance"></a>Desempenho da VM do Servidor de Backup do Azure

Se compartilhado com outras máquinas virtuais, o tamanho da conta de armazenamento e os limites IOPS afetam o desempenho da VM do Servidor de Backup do Azure. Por esse motivo, use uma conta de armazenamento separada para a máquina virtual do Servidor de Backup do Azure. O agente de Backup do Azure em execução no Servidor de Backup do Azure precisa de armazenamento temporário para:

- seu próprio uso (um local de cache),
- dados restaurados da nuvem (área de preparação local)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar o armazenamento em disco temporário do Backup do Azure

Cada máquina virtual do Azure Stack vem com armazenamento em disco temporário, que está disponível para o usuário como volume `D:\`. A área de preparação local necessária para o Backup do Azure pode ser configurada para residir no `D:\`, e o local do cache pode ser colocado em `C:\`. Dessa forma, nenhum armazenamento precisará ser formado longe os discos de dados anexados à máquina virtual do Servidor de Backup do Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup em disco local e no Azure

O Servidor de Backup do Azure armazena dados de backup em discos do Azure anexados à máquina virtual, para recuperação operacional. Depois que os discos e o espaço de armazenamento são anexados à máquina virtual, o Servidor de Backup do Azure gerenciará o armazenamento para você. A quantidade de armazenamento de dados de backup depende do número e do tamanho dos discos anexados a cada [máquina de virtual do Azure Stack](/azure-stack/user/azure-stack-storage-overview). Cada tamanho de VM do Azure Stack tem um número máximo de discos que podem ser anexados à máquina virtual. Por exemplo, A2 é para quatro discos. A3 é para oito discos. A4 é para 16 discos. Novamente, o tamanho e o número de discos determina o pool de armazenamento de backup total.

> [!IMPORTANT]
> **Não** retenha os dados de recuperação operacional (backup) nos discos anexados ao Servidor de Backup do Azure por mais de cinco dias.
>

Armazenar os dados de backup no Azure reduz a infraestrutura de backup no Azure Stack. Se os dados tiverem mais de cinco dias, deverão ser armazenados no Azure.

Para armazenar dados de backup no Azure, crie ou use um cofre dos Serviços de Recuperação. Ao se preparar para fazer backup da carga de trabalho do Servidor de Backup do Azure, [configure o cofre dos Serviços de Recuperação](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de backup for executado, um ponto de recuperação será criado no cofre. Cada cofre dos Serviços de Recuperação contém até 9999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e de quanto tempo eles são retidos, você pode reter os dados de backup por muitos anos. Por exemplo, você pode criar pontos de recuperação mensalmente e mantê-los por cinco anos.

### <a name="scaling-deployment"></a>Dimensionar a implantação

Se você quiser dimensionar a implantação, terá as seguintes opções:

- Escalar verticalmente: aumentar o tamanho da máquina virtual do Servidor de Backup do Azure da série A para a série D. Além disso, aumentar o armazenamento local [de acordo com as instruções da máquina virtual do Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
- Descarregar dados: enviar dados mais antigos para o Microsoft Azure e reter somente os mais recentes no armazenamento anexado ao Servidor de Backup do Azure.
- Escalar horizontalmente: adicionar mais Servidores de Backup do Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 SP1 ou superior deve ser instalado na máquina virtual.

### <a name="joining-a-domain"></a>Ingressando em um domínio

A máquina virtual do Servidor de Backup do Azure deve ser ingressada em um domínio. Um usuário de domínio com privilégios de administrador deve instalar o Servidor de Backup do Azure na máquina virtual.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Usando uma VM de IaaS no Azure Stack

Ao escolher um servidor para o Servidor de Backup do Azure, comece com uma imagem da galeria do Windows Server 2012 R2 Datacenter ou Windows Server 2016 Datacenter. O artigo [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), fornece um tutorial para começar a trabalhar com a máquina virtual recomendada no Azure. Os requisitos mínimos recomendados para a máquina virtual (VM) do servidor devem ser: Standard A2 com 2 núcleos e 3.5 GB de RAM.

Proteger as cargas de trabalho com o Servidor de Backup do Azure tem muitas nuanças. A [matriz de proteção para MABS](./backup-mabs-protection-matrix.md) ajuda a explicar essas nuances. Antes de implantar o computador, leia este artigo na íntegra.

> [!NOTE]
> O Servidor de Backup do Azure foi projetado para ser executado em uma máquina virtual dedicada de finalidade única. Não é possível instalar o Servidor de Backup do Azure em:
>
> - Um computador que esteja sendo executado como um controlador de domínio
> - Um computador no qual a função de Servidor de Aplicativos está instalada
> - Um computador que o Exchange Server está executando
> - Um computador que é um nó de um cluster

Sempre ingresse o Servidor de Backup do Azure em um domínio. Se você precisa mover o Servidor de Backup do Azure para um domínio diferente, primeiro instale o Servidor de Backup do Azure e, em seguida, ingresse-o em um novo domínio. Depois de implantar o Servidor de Backup do Azure, você não pode movê-lo para um novo domínio.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Definir replicação de armazenamento

A opção de replicação de armazenamento de cofre dos Serviços de Recuperação permite que você escolha entre o armazenamento com redundância geográfica e o armazenamento com redundância local. Por padrão, os cofres dos Serviços de Recuperação usam armazenamento com redundância geográfica. Se este cofre for o primário, deixe a opção de armazenamento definida como armazenamento com redundância geográfica. Escolha armazenamento com redundância local se quiser uma opção mais barata que seja menos durável. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage), [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage)e [com redundância de zona](../storage/common/storage-redundancy.md#zone-redundant-storage) na [visão geral da replicação de armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a configuração de replicação de armazenamento:

1. Selecione o cofre para abrir o painel do cofre e o menu Configurações. Se o menu **configurações** não abrir, selecione **todas as configurações** no painel do cofre.
2. No menu **configurações** , selecione **infraestrutura de backup**  >  **configuração de backup** para abrir o menu **configuração de backup** . No menu **Configuração de Backup**, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Baixar o instalador do Servidor de Backup do Azure

Há duas maneiras de baixar o instalador do Servidor de Backup do Azure. Você pode baixar o instalador do Servidor de Backup do Azure do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55269). Você também pode baixar Servidor de Backup do Azure Installer enquanto estiver configurando um cofre dos serviços de recuperação. As etapas a seguir orientam você durante o download do instalador do portal do Azure ao configurar um cofre de Serviços de Recuperação.

1. Na sua máquina virtual do Azure Stack, [entre na sua assinatura do Azure no portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, selecione **Todos os Serviços**.

    ![Escolha a opção Todos os Serviços no menu principal](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Na caixa de diálogo **Todos os Serviços**, digite *Serviços de Recuperação*. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Ao ver a opção, selecione **Cofres dos Serviços de Recuperação**.

    ![Digite Serviços de Recuperação na caixa de diálogo Todos os serviços](./media/backup-mabs-install-azure-stack/all-services.png)

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

4. Na lista de cofres de Serviços de Recuperação, selecione um cofre para abrir seu painel.

    ![Selecione seu cofre para abrir o painel](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. No menu Introdução do cofre, selecione **backup** para abrir o assistente de introdução.

    ![Guia de introdução ao backup](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    O menu Backup é aberto.

    ![Backup-metas-padrão-aberto](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. No menu Backup, no item de menu **Onde sua carga de trabalho é executada**, selecione **Local**. No menu suspenso **Do que fazer você deseja fazer backup?** , selecione as cargas de trabalho que você deseja proteger com o Servidor de Backup do Azure. Se você não tiver certeza de quais cargas de trabalho selecionar, escolha **máquinas virtuais Hyper-V** e, em seguida, selecione **preparar infraestrutura**.

    ![local e cargas de trabalho como metas](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    O menu **Preparar infraestrutura** é aberto.

7. No menu **preparar infraestrutura** , selecione **baixar** para abrir uma página da Web para baixar servidor de backup do Azure arquivos de instalação.

    ![Guia de Introdução do assistente para alterar](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    A página da Web da Microsoft que hospeda os arquivos para download do Servidor de Backup do Azure é aberta.

8. Na página de download do Backup do Microsoft Azure Server, escolha um idioma e selecione **baixar**.

    ![O Centro de Download é aberto](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. O instalador do Servidor de Backup do Azure é composto por oito arquivos, um instalador e sete arquivos .bin. Verifique o **nome do arquivo** para selecionar todos os arquivos necessários e selecione **Avançar**. Baixe todos os arquivos na mesma pasta.

    ![Centro de download, arquivos selecionados](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    O tamanho do download de todos os arquivos de instalação é maior que 3 GB. Em um link de download de 10 Mbps, baixar todos os arquivos de instalação pode levar até 60 minutos. Os arquivos são baixados para o local de download especificado.

## <a name="extract-azure-backup-server-install-files"></a>Extrair os arquivos de instalação do Servidor de Backup do Azure

Depois de baixar todos os arquivos para a máquina virtual do Azure Stack, acesse o local do download. A primeira fase da instalação do Servidor de Backup do Azure é a extração dos arquivos.

![Baixar o instalador do MABS](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Para iniciar a instalação, na lista de arquivos baixados, selecione **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Pelo menos 4 GB de espaço livre são necessários para a extração dos arquivos de instalação.
    >

2. No assistente de Servidor de Backup do Azure, selecione **Avançar** para continuar.

    ![Assistente de Instalação do Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Escolha o caminho para os arquivos de Servidor de Backup do Azure e selecione **Avançar**.

   ![Selecionar destino para arquivos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Verifique o local de extração e selecione **extrair**.

   ![Verificar local de extração](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. O assistente extrai os arquivos e prepara o processo de instalação.

   ![O assistente extrai arquivos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Quando o processo de extração for concluído, selecione **concluir**. Por padrão, **Executar setup.exe** está selecionado. Quando você seleciona **concluir**, o Setup.exe instala backup do Microsoft Azure Server no local especificado.

   ![A instalação extrai arquivos do Backup do Microsoft Azure Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalar o pacote de software

Na etapa anterior, selecione **concluir** para sair da fase de extração e inicie o assistente de instalação do servidor de backup do Azure.

![O assistente de instalação do Backup do Microsoft Azure é iniciado](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

O Servidor de Backup do Azure compartilha código com o Data Protection Manager. Você verá referências a Data Protection Manager e ao DPM no instalador do Servidor de Backup do Azure. Embora o Servidor de Backup do Azure e o Data Protection Manager sejam produtos separados, esses produtos estão intimamente relacionados.

1. Para iniciar o assistente de instalação, selecione **backup do Microsoft Azure Server**.

   ![Selecionar servidor Backup do Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na tela de **boas-vindas** , selecione **Avançar**.

    ![Servidor de Backup do Azure-bem-vindo](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na tela **verificações de pré-requisitos** , selecione **verificar** para determinar se os pré-requisitos de hardware e software para servidor de backup do Azure foram atendidos.

    ![Servidor de Backup do Azure-verificação de pré-requisitos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Se o seu ambiente tiver os pré-requisitos necessários, você verá uma mensagem indicando que o computador atende aos requisitos. Selecione **Avançar**.  

    ![Servidor de Backup do Azure: verificação de pré-requisitos aprovada](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Se o ambiente não atender aos pré-requisitos necessários, os problemas serão especificados. Os pré-requisitos que não foram atendidos também estão listados no DpmSetup. log. Resolva os erros de pré-requisito e execute **Verificar Novamente**. A instalação não pode continuar até que todos os pré-requisitos sejam atendidos.

    ![Servidor de Backup do Azure: pré-requisitos de instalação não atendidos](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. O Servidor de Backup do Microsoft Azure exige o SQL Server. O pacote de instalação do Servidor de Backup do Azure é fornecido em pacote com os binários do SQL Server apropriados. Se quiser usar sua própria instalação do SQL, você pode. No entanto, a opção recomendada é permitir que o instalador adicione uma nova instância do SQL Server. Para garantir que sua escolha funcione com seu ambiente, selecione **verificar e instalar**.

   > [!NOTE]
   > Servidor de Backup do Azure não funcionará com uma instância de SQL Server remota. A instância usada pelo Servidor de Backup do Azure precisa ser local.
   >

    ![Configurações de Servidor de Backup do Azure-SQL](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Após a verificação, se a máquina virtual tiver os pré-requisitos necessários para instalar Servidor de Backup do Azure, selecione **Avançar**.

    ![Servidor de Backup do Azure-requisitos atendidos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Se ocorrer uma falha com uma recomendação de reiniciar o computador, faça isso. Depois de reiniciar o computador, reinicie o instalador e, quando chegar à tela de **configurações do SQL** , selecione **verificar novamente**.

5. Nas **configurações de instalação**, forneça um local para a instalação dos arquivos do backup do Microsoft Azure Server e selecione **Avançar**.

    ![Fornecer local para a instalação de arquivos](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    O local temporário é um requisito para o backup no Azure. Verifique se o tamanho do local temporário é equivalente a pelo menos 5% dos dados planejados para fazer backup no Azure. Para proteção de disco, será necessário configurar discos separados após a conclusão da instalação. Para obter mais informações sobre pools de armazenamento, consulte [preparar o armazenamento de dados](/system-center/dpm/plan-long-and-short-term-data-storage).

6. Na tela **configurações de segurança** , forneça uma senha forte para contas de usuário locais restritas e selecione **Avançar**.

    ![Tela de Configurações de segurança](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na tela **Microsoft Update aceitar** , selecione se deseja usar *Microsoft Update* para verificar se há atualizações e selecione **Avançar**.

   > [!NOTE]
   > É recomendável fazer o Windows Update ser redirecionado para o Microsoft Update, que oferece segurança e atualizações importantes para o Windows e outros produtos, como o Servidor de Backup do Microsoft Azure.
   >

    ![Tela de Opt-In de Microsoft Update](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Examine o *Resumo das configurações* e selecione **instalar**.

    ![Resumo das configurações](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Quando o Servidor de Backup do Azure conclui a instalação, o instalador inicia imediatamente o instalador do Agente dos Serviços de Recuperação do Microsoft Azure.

9. O instalador do Agente dos Serviços de Recuperação do Microsoft Azure abre e verifica a conectividade com a Internet. Se a conectividade com a Internet estiver disponível, continue com a instalação. Se não houver conectividade, forneça detalhes de proxy para se conectar à Internet. Depois de especificar as configurações de proxy, selecione **Avançar**.

    ![Configuração de proxy](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Para instalar o agente de Serviços de Recuperação do Microsoft Azure, selecione **instalar**.

    ![Instalação do agente](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    O Agente dos Serviços de Recuperação do Microsoft Azure, também chamado de Agente de Backup do Azure, configura o Servidor de Backup do Azure para o cofre de Serviços de Recuperação. Uma vez configurado, Servidor de Backup do Azure sempre fará backup dos dados no mesmo cofre dos serviços de recuperação.

11. Depois que o agente de Serviços de Recuperação do Microsoft Azure concluir a instalação, selecione **Avançar** para iniciar a próxima fase: registrar servidor de backup do Azure com o cofre dos serviços de recuperação.

    ![Instalação do agente concluída com êxito](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    O instalador inicia o **Assistente de Registro do Servidor**.

12. Alterne para a sua assinatura do Azure e seu cofre de Serviços de Recuperação. No menu **preparar infraestrutura** , selecione **baixar** para baixar as credenciais do cofre. Se o botão **baixar** na etapa 2 não estiver ativo, selecione **já baixado ou usando a instalação mais recente do servidor de backup do Azure** para ativar o botão. As credenciais do cofre são baixadas no local em que você armazena os downloads. Esteja ciente deste local porque precisará dele para a próxima etapa.

    ![Baixar as credenciais do cofre](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. No menu de **identificação do cofre** , selecione **procurar** para localizar as credenciais do cofre dos serviços de recuperação.

    ![Menu de identificação do cofre](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Na caixa de diálogo **selecionar credenciais do cofre** , vá para o local de download, selecione as credenciais do cofre e selecione **abrir**.

    O caminho para as credenciais é exibido no menu Identificação do Cofre. Selecione **Avançar** para ir até as **configurações de criptografia**.

14. Na caixa de diálogo **configuração de criptografia** , forneça uma senha para a criptografia de backup e um local para armazenar a senha e selecione **Avançar**.

    ![Configurações de Criptografia](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Você pode fornecer sua própria frase secreta ou usar o gerador de frase secreta para criar uma para você. A frase secreta é sua e a Microsoft não salva nem gerencia essa frase secreta. Para se preparar para um desastre, salve sua frase secreta em um local acessível.

    Depois de selecionar **Avançar**, o servidor de backup do Azure é registrado com o cofre dos serviços de recuperação. O instalador continuará a instalação do SQL Server e do Servidor de Backup do Azure.

    ![A instalação instala o SQL e o Servidor de Backup do Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Quando o instalador for concluído, o **status** mostrará que todo o software foi instalado com êxito.

    ![O software foi instalado com êxito](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Quando a instalação for concluída, o console do Servidor de Backup do Azure e os ícones do PowerShell do Servidor de Backup do Azure são criados na área de trabalho do servidor.

## <a name="add-backup-storage"></a>Adicionar armazenamento de backup

A primeira cópia de backup é mantida no armazenamento anexado ao computador do Servidor de Backup do Azure. Para obter mais informações sobre adicionar discos, consulte [Adicionar armazenamento de Backup Moderno](/system-center/dpm/add-storage).

> [!NOTE]
> Você precisará adicionar armazenamento de backup mesmo se planejar enviar dados para o Azure. Na arquitetura do Servidor de Backup do Azure, o cofre dos Serviços de Recuperação mantém a *segunda* cópia dos dados, enquanto o armazenamento local mantém a primeira (e obrigatória) cópia de backup.
>
>

## <a name="network-connectivity"></a>Conectividade de rede

O Servidor de Backup do Azure requer conectividade com o serviço de Backup Azure para que o produto funcione com êxito. Para validar se o computador tem conectividade com o Azure, use o cmdlet ```Get-DPMCloudConnection``` no console do PowerShell do Servidor de Backup do Azure. Se a saída do cmdlet for TRUE, a conectividade existirá, caso contrário, não haverá conectividade.

Ao mesmo tempo, a assinatura do Azure deve estar em um estado íntegro. Para descobrir o estado de sua assinatura e gerenciá-la, entre no [portal da assinatura](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Quando você souber o estado da conectividade do Azure e da assinatura do Azure, poderá usar a tabela abaixo para descobrir o impacto sobre a funcionalidade de backup/restauração oferecida.

| Estado de conectividade | Assinatura do Azure | Fazer backup no Azure | Fazer backup em disco | Restaurar do Azure | Restaurar do disco |
| --- | --- | --- | --- | --- | --- |
| Conectado |Ativo |Permitido |Permitido |Permitido |Permitido |
| Conectado |Expirado |Parado |Parado |Permitido |Permitido |
| Conectado |Provisionamento Cancelado |Parado |Parado |Parado e pontos de recuperação do Azure excluídos |Parado |
| Perda de conectividade > 15 dias |Ativo |Parado |Parado |Permitido |Permitido |
| Perda de conectividade > 15 dias |Expirado |Parado |Parado |Permitido |Permitido |
| Perda de conectividade > 15 dias |Provisionamento Cancelado |Parado |Parado |Parado e pontos de recuperação do Azure excluídos |Parado |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperação de perda de conectividade

Se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem as seguintes URLs e endereços IP:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18


Depois que a conectividade com o Azure for restaurada para o Servidor de Backup do Azure, o estado da assinatura do Azure determinará as operações que podem ser realizadas. Depois que o servidor estiver **Conectado**, use a tabela em [Conectividade de rede](backup-mabs-install-azure-stack.md#network-connectivity) para ver as operações disponíveis.

### <a name="handling-subscription-states"></a>Tratando os estados de assinatura

É possível alterar uma assinatura do Azure do estado *Expirado* ou *Desprovisionado* para o estado *Ativo*. Enquanto o estado da assinatura não estiver *ativo*:

- Enquanto uma assinatura está com o estado *Desprovisionado*, ela perde a funcionalidade. Restaurar o estado da assinatura para *Ativo* reativa a funcionalidade de backup/restauração. Se os dados de backup no disco local forem mantidos com um período de retenção suficientemente grande, eles poderão ser recuperados. No entanto, os dados de backup no Azure serão irremediavelmente perdidos depois da assinatura entrar no estado *Desprovisionado*.
- Enquanto uma assinatura está com o estado *Expirado*, ela perde a funcionalidade. Os backups agendados não são executados enquanto uma assinatura *expira*.

## <a name="troubleshooting"></a>Solução de problemas

Se o servidor de Backup do Microsoft Azure falhar com erros durante a fase de instalação (ou no backup ou na restauração), consulte o [documento de códigos de erro](https://support.microsoft.com/kb/3041338).
Você também pode consultar as [Perguntas frequentes relacionadas ao Backup do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Próximas etapas

O artigo [Preparando seu ambiente para o DPM](/system-center/dpm/prepare-environment-for-dpm), contém informações sobre as configurações compatíveis do Servidor de Backup do Azure.

Você pode usar os seguintes artigos para obter um entendimento mais profundo sobre a proteção da carga de trabalho usando o Servidor de Backup do Microsoft Azure.

- [Backup do SQL Server](./backup-mabs-sql-azure-stack.md)
- [Backup do servidor do SharePoint](./backup-mabs-sharepoint-azure-stack.md)
- [Backup do servidor alternativo](backup-azure-alternate-dpm-server.md)
