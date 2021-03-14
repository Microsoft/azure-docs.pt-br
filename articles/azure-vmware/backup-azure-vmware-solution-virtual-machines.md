---
title: Fazer backup de VMs de solução do Azure VMware com Servidor de Backup do Azure
description: Configure seu ambiente de solução do Azure VMware para fazer backup de máquinas virtuais usando Servidor de Backup do Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 163065556b7dbc979d68613744ea827c209d9fda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618909"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Fazer backup de VMs de solução do Azure VMware com Servidor de Backup do Azure

Neste artigo, vamos fazer backup de VMs (máquinas virtuais) VMware em execução na solução VMware do Azure com Servidor de Backup do Azure. Primeiro, percorra minuciosamente a [configuração do servidor backup do Microsoft Azure para a solução VMware do Azure](set-up-backup-server-for-azure-vmware-solution.md).

Em seguida, examinaremos todos os procedimentos necessários para:

> [!div class="checklist"] 
> * Configurar um canal seguro para que o Servidor de Backup do Azure possa se comunicar com servidores VMware via HTTPS. 
> * Adicione as credenciais da conta a Servidor de Backup do Azure. 
> * Adicione o vCenter ao Servidor de Backup do Azure. 
> * Configure um grupo de proteção que contenha as VMs do VMware de que você deseja fazer backup, especifique as configurações de backup e agende o backup.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Criar uma conexão segura com o vCenter Server

Por padrão, o Servidor de Backup do Azure se comunica com servidores VMware via HTTPS. Para configurar a conexão HTTPS, baixe o certificado da AC (autoridade de certificação) do VMware e importe-o no Servidor de Backup do Azure.

### <a name="set-up-the-certificate"></a>Configurar o certificado

1. No navegador, na máquina Servidor de Backup do Azure, insira a URL do cliente Web vSphere.

   > [!NOTE] 
   > Se a página **introdução** do VMware não for exibida, verifique as configurações de conexão e proxy do navegador e tente novamente.

1. Na página **introdução** de VMware, selecione **baixar certificados de AC raiz confiável**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Cliente Web vSphere":::

1. Salve o arquivo de **download.zip** no computador servidor de backup do Azure e, em seguida, Extraia seu conteúdo para a pasta **certs** , que contém:

   - Arquivo de certificado raiz com uma extensão que começa com uma sequência numerada como. 0 e. 1.
   - Arquivo de CRL com uma extensão que começa com uma sequência como. R0 ou. R1.

1. Na pasta **certificados** , clique com o botão direito do mouse no arquivo de certificado raiz e selecione **renomear** para alterar a extensão para **. CRT**.

   O ícone de arquivo muda para um que representa um certificado raiz.

1. Clique com o botão direito do mouse no certificado raiz e selecione **Instalar certificado**.

1. No **Assistente para importação de certificados**, selecione **computador local** como o destino do certificado e selecione **Avançar**.

   ![Página de boas-vindas do assistente](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Se for solicitado, confirme que você deseja permitir alterações no computador.

1. Selecione **Coloque todos os certificados no repositório a seguir** e selecione **procurar** para escolher o repositório de certificados.

   ![Armazenamento de certificado](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selecione **autoridades de certificação raiz confiáveis** como a pasta de destino e selecione **OK**.

1. Examine as configurações e selecione **concluir** para começar a importar o certificado.

   ![Verificar se o certificado está na pasta correta](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Depois que a importação do certificado for confirmada, entre no vCenter Server para confirmar se a conexão está segura.

### <a name="enable-tls-12-on-azure-backup-server"></a>Habilitar TLS 1,2 em Servidor de Backup do Azure

O VMware 6,7 em diante tinha o TLS habilitado como o protocolo de comunicação. 

1. Copie as seguintes configurações do registro e cole-as no bloco de notas. Em seguida, salve o arquivo como TLS. REG sem a extensão. txt.

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Clique com o botão direito do mouse no TLS. REG e selecione **mesclar** ou **abrir** para adicionar as configurações ao registro.


## <a name="add-the-account-on-azure-backup-server"></a>Adicione a conta no Servidor de Backup do Azure

1. Abra servidor de backup do Azure e, no console do servidor de backup do Azure, selecione **Gerenciamento**  >  **servidores de produção**  >  **gerenciar VMware**.

   ![Console do Servidor de Backup do Azure](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Na caixa de diálogo **gerenciar credenciais** , selecione **Adicionar**.

   ![Na caixa de diálogo gerenciar credenciais, selecione Adicionar.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Na caixa de diálogo **Adicionar Credencial**, insira um nome e uma descrição para a nova credencial. Especifique o nome de usuário e a senha que você definiu no servidor VMware.

   > [!NOTE] 
   > Se o servidor VMware e Servidor de Backup do Azure não estiverem no mesmo domínio, especifique o domínio na caixa **nome de usuário** .

   ![Caixa de diálogo Adicionar Credencial do Servidor de Backup do Azure](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Selecione **Adicionar** para adicionar a nova credencial.

   ![Captura de tela mostra a caixa de diálogo Servidor de Backup do Azure gerenciar credenciais com novas credenciais exibidas.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adicionar o vCenter Server ao Servidor de Backup do Azure

1. No console do servidor de backup do Azure, selecione **Gerenciamento**  >  **servidores de produção**  >  **Adicionar**.

   ![Abra o Assistente de Adição de Servidor de Produção](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selecione **servidores VMware** e selecione **Avançar**.

   ![Assistente de Adição de Servidor de Produção](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Especifique o endereço IP do vCenter.

   ![Especifique o servidor VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Na caixa **porta SSL** , insira a porta usada para se comunicar com o vCenter.

   > [!TIP] 
   > A porta 443 é a porta padrão, mas você pode alterá-la se o seu vCenter escutar em uma porta diferente.

1. Na caixa **especificar credencial** , selecione a credencial que você criou na seção anterior.

1. Selecione **Adicionar** para adicionar o vCenter à lista de servidores e selecione **Avançar**.

   ![Adicionar servidor VMware e credencial](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na página **Resumo** , selecione **Adicionar** para adicionar o vCenter ao servidor de backup do Azure.

   O novo servidor é adicionado imediatamente. o vCenter não precisa de um agente.

   ![Adicionar servidor VMware para o Servidor de Backup do Azure](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na página **concluir** , examine as configurações e selecione **fechar**.

   ![Página Concluir](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Você verá o servidor do vCenter listado em **servidor de produção** com:
   - Tipo como **servidor VMware** 
   - Status do agente como **OK** 
   
      Se você vir o **status do agente** como **desconhecido**, selecione **Atualizar**.

## <a name="configure-a-protection-group"></a>Configurar grupo de proteção

Grupos de proteção reúnem várias VMs e aplicam as mesmas configurações de retenção de dados e backup a todas as VMs no grupo.

1. No console do servidor de backup do Azure, selecione **proteção**  >  **novo**.

   ![Abra o assistente para Criar Novo Grupo de Proteção](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na página de boas-vindas do assistente para **criar novo grupo de proteção** , selecione **Avançar**.

   ![Caixa de diálogo Assistente para Criar Novo Grupo de Proteção](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na página **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores** e, depois, selecione **Avançar**. A página **selecionar membros do grupo** é exibida.

1. Na página **selecionar membros do grupo** , selecione as VMs (ou as pastas de VM) que você deseja fazer backup e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Quando você seleciona uma pasta ou VMs, as pastas dentro dessa pasta também são selecionadas para backup. Você pode desmarcar as pastas ou VMs das quais não deseja fazer backup. Se uma VM ou pasta já estiver sendo submetida a backup, você não poderá selecioná-la, o que garante que os pontos de recuperação duplicados não sejam criados para uma VM.

   ![Selecionar membros do grupo](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na página **selecionar método de proteção de dados** , insira um nome para o grupo de proteção e as configurações de proteção. 

1. Defina a proteção de curto prazo em **disco**, habilite a proteção online e, em seguida, selecione **Avançar**.

   ![Selecionar método de proteção de dados](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Especifique por quanto tempo deseja manter os dados de backup em disco.

   - **Período de retenção**: o número de dias que os pontos de recuperação de disco são mantidos.
   - **Backup completo expresso**: com que frequência os pontos de recuperação de disco são obtidos. Para alterar os horários ou as datas em que os backups de curto prazo ocorrem, selecione **Modificar**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Especifique seus objetivos de curto prazo para a proteção baseada em disco":::

1. Na página **examinar armazenamento em disco alocação** , examine o espaço em disco fornecido para os backups de VM.

   - As alocações de disco recomendadas baseiam-se no período de retenção especificado, no tipo de carga de trabalho e no tamanho dos dados protegidos. Faça as alterações necessárias e, em seguida, selecione **Avançar**.
   - **Tamanho dos dados:** tamanho dos dados no grupo de proteção.
   - **Espaço em disco:** Quantidade recomendada de espaço em disco para o grupo de proteção. Se você quiser modificar essa configuração, selecione o espaço levemente maior do que a quantidade estimada de cada fonte de dados.
   - **Detalhes do pool de armazenamento:** Mostra o status do pool de armazenamento, que inclui o tamanho do disco total e restante.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Examinar o espaço em disco fornecido no pool de armazenamento":::

   > [!NOTE]
   > Em alguns cenários, o tamanho dos dados relatados é maior que o tamanho real da VM. Estamos cientes do problema e atualmente investigamos.

1. Na página **escolher método de criação de réplica** , indique como você deseja fazer o backup inicial e selecione **Avançar**.

   - O padrão é **Automaticamente pela rede** e **Agora**. Se você usar o padrão, especifique um horário de pico. Se você escolher **mais tarde**, especifique um dia e uma hora.
   - Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

   ![Escolher método de criação de réplica](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Para **Opções de verificação de consistência**, selecione como e quando automatizar as verificações de consistência e selecione **Avançar**.

   - Você pode executar verificações de consistência quando dados de réplica se tornam inconsistentes ou de acordo com um agendamento definido.
   - Se você não quiser configurar verificações de consistência automáticas, poderá executar uma verificação manual clicando com o botão direito do mouse no grupo de proteção **executar verificação de consistência**.

1. Na página **especificar dados de proteção online** , selecione as VMs ou as pastas de VM que você deseja fazer backup e, em seguida, selecione **Avançar**. 

   > [!TIP]
   > Você pode selecionar os membros individualmente ou escolher **selecionar todos** para escolher todos os membros.

   ![Especificar dados de proteção online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na página **especificar agendamento de backup online** , indique com que frequência você deseja fazer backup de dados do armazenamento local para o Azure. 

   - Pontos de recuperação de nuvem para os dados a serem gerados de acordo com o agendamento. 
   - Depois que o ponto de recuperação é gerado, ele é transferido para o cofre dos serviços de recuperação no Azure.

   ![Especifique o cronograma do backup online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na página **especificar política de retenção online** , indique por quanto tempo deseja manter os pontos de recuperação criados dos backups para o Azure.

   - Não há um tempo limite para manter dados no Azure.
   - O único limite é que você não pode ter mais de 9.999 pontos de recuperação por instância protegida. Neste exemplo, a instância protegida é o servidor VMware.

   ![Especificar política de retenção online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na página **Resumo** , examine as configurações e, em seguida, selecione **Criar grupo**.

   ![Resumo da configuração e do membro do grupo de proteção](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorar com o console do Servidor de Backup do Azure

Depois de configurar o grupo de proteção para fazer backup de VMs de solução do Azure VMware, você pode monitorar o status do trabalho de backup e alertar usando o console do Servidor de Backup do Azure. Veja o que você pode monitorar.

- Na área de tarefa **monitoramento** :
   - Em **alertas**, você pode monitorar erros, avisos e informações gerais.  Você pode exibir alertas ativos e inativos e configurar notificações por email.
   - Em **trabalhos**, você pode exibir os trabalhos iniciados por servidor de backup do Azure para uma fonte de dados protegida ou um grupo de proteção específico. Você pode seguir o progresso do trabalho ou verificar os recursos consumidos pelos trabalhos.
- Na área de tarefa **proteção** , você pode verificar o status de volumes e compartilhamentos no grupo de proteção. Você também pode verificar as definições de configuração, como configurações de recuperação, alocação de disco e agendamento de backup.
- Na área de tarefa **Gerenciamento** , você pode exibir as guias **discos, online** e **agentes** para verificar o status dos discos no pool de armazenamento, o registro no Azure e o status do agente do DPM implantado.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Monitorar o status dos trabalhos de backup no Servidor de Backup do Azure":::

## <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

No Servidor de Backup do Azure Console do Administrador, há duas maneiras de localizar dados recuperáveis. Você pode pesquisar ou procurar. Ao recuperar dados, você pode ou não querer restaurar dados ou uma VM para o mesmo local. Por esse motivo, Servidor de Backup do Azure dá suporte a três opções de recuperação para backups de VM VMware:

- **OLR (recuperação de local original)**: Use OLR para restaurar uma VM protegida para seu local original. Você poderá restaurar uma VM para seu local original somente se nenhum disco tiver sido adicionado ou excluído desde que o backup ocorreu. Se discos foram adicionados ou excluídos, você deve usar a recuperação de local alternativo.
- **Recuperação de local alternativo (ALR)**: Use quando a VM original estiver ausente ou você não quiser perturbar a VM original. Forneça o local de um host ESXi, um pool de recursos, uma pasta e o armazenamento e o caminho do armazenamento de repositório. Para ajudar a diferenciar a VM restaurada da VM original, Servidor de Backup do Azure acrescenta *"-Recovered"* ao nome da VM.
- **ILR (recuperação de local de arquivo individual)**: se a VM protegida for uma VM do Windows Server, os arquivos individuais ou pastas dentro da VM poderão ser recuperados usando o recurso ILR de servidor de backup do Azure. Para recuperar arquivos individuais, confira o procedimento neste artigo. A restauração de um arquivo individual de uma VM está disponível somente para pontos de recuperação de disco e VM do Windows.

### <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1. Na Console do Administrador Servidor de Backup do Azure, selecione o modo de exibição **recuperação** . 

1. Usando o painel **Procurar**, procure ou filtre para localizar a VM que você deseja recuperar. Depois de selecionar uma VM ou pasta, o painel * * pontos de recuperação para exibir os pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. No painel **pontos de recuperação para** , selecione uma data em que um ponto de recuperação foi tirado. Datas do calendário em negrito têm pontos de recuperação disponíveis. Como alternativa, você pode clicar com o botão direito do mouse na VM e selecionar **Mostrar todos os pontos de recuperação** e, em seguida, selecionar o ponto de recuperação na lista.

   > [!NOTE] 
   > Para proteção de curto prazo, selecione um ponto de recuperação baseado em disco para recuperação mais rápida. Depois que os pontos de recuperação de curto prazo expirarem, você verá apenas os pontos de recuperação **online** a serem recuperados.

1. Antes de recuperar de um ponto de recuperação online, verifique se o local de preparo contém espaço livre suficiente para abrigar o tamanho completo descompactado da VM que você deseja recuperar. O local de preparo pode ser exibido ou alterado executando o **Assistente para definir configurações de assinatura**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Servidor de Backup do Azure configurações da pasta de recuperação":::

1. Selecione **recuperar** para abrir o **Assistente de recuperação**.

   ![Assistente de recuperação, página examinar seleção de recuperação](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selecione **Avançar** para ir para a tela **especificar opções de recuperação** . Selecione **Avançar** novamente para ir para a tela **Selecionar tipo de recuperação** . 

   > [!NOTE]
   > As cargas de trabalho do VMware não dão suporte à habilitação da limitação da largura de banda

1. Na página **Selecionar tipo de recuperação** , recupere para a instância original ou para um novo local.

   - Se você escolher **Recuperar na instância original**, não precisará escolher outras opções no assistente. Os dados da instância original são usados.
   - Se você escolher **Recuperar como máquina virtual em algum host**, na tela **Especificar Destino**, forneça as informações de **Host ESXi**, **Pool de Recursos**, **Pasta** e **Caminho**.

   ![página Selecionar Tipo de Recuperação](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na página **Resumo** , examine as configurações e selecione **recuperar** para iniciar o processo de recuperação. 

   A tela **Status de recuperação** mostra o progresso da operação de recuperação.

### <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um arquivo individual de uma VM

Você pode restaurar arquivos individuais de um ponto de recuperação de VM protegida. Esse recurso só está disponível para VMs do Windows Server. A restauração de arquivos individuais é semelhante à restauração de toda a VM, exceto que você navega para o VMDK e localiza os arquivos desejados antes de iniciar o processo de recuperação. 

> [!NOTE]
> A restauração de um arquivo individual de uma VM está disponível somente para pontos de recuperação de disco e VM do Windows.

1. Na Console do Administrador Servidor de Backup do Azure, selecione o modo de exibição **recuperação** .

1. Usando o painel **Procurar**, procure ou filtre para localizar a VM que você deseja recuperar. Depois de selecionar uma VM ou pasta, o painel * * pontos de recuperação para exibir os pontos de recuperação disponíveis.

   ![Pontos de recuperação disponíveis](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. No painel **pontos de recuperação para** , use o calendário para selecionar a data que contém os pontos de recuperação desejados. Dependendo de como a política de backup foi configurada, as datas podem ter mais de um ponto de recuperação. 

1. Depois de selecionar o dia em que o ponto de recuperação foi obtido, certifique-se de escolher o **tempo de recuperação** correto. 

   > [!NOTE]
   > Se a data selecionada tiver vários pontos de recuperação, escolha o ponto de recuperação selecionando-o no menu suspenso **Tempo de recuperação**. 

   Depois de escolher o ponto de recuperação, a lista de itens recuperáveis aparece no painel **caminho** .

1. Para localizar os arquivos que você deseja recuperar, no painel **caminho** , clique duas vezes no item na coluna **Item recuperável** para abri-lo. Em seguida, selecione o arquivo ou as pastas que você deseja recuperar. Para selecionar vários itens, selecione a tecla **Ctrl** enquanto seleciona cada item. Use o painel **caminho** para pesquisar a lista de arquivos ou pastas que aparecem na coluna **Item recuperável** .
    
   > [!NOTE]
   > A **lista de pesquisa abaixo** não pesquisa em subpastas. Para pesquisar em subpastas, clique duas vezes na pasta. Use o botão **Para cima** a fim de sair de uma pasta filho para a pasta pai. Você pode selecionar vários itens (arquivos e pastas), mas eles devem estar na mesma pasta pai. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

   ![Revisar seleção de recuperação](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Depois de selecionar os itens para recuperação, na faixa de opções da ferramenta de Console do Administrador, selecione **recuperar** para abrir o **Assistente de recuperação**. No **Assistente de recuperação**, a tela **revisar seleção de recuperação** mostra os itens selecionados a serem recuperados.

1. Na tela **especificar opções de recuperação** , execute uma das seguintes etapas:

   - Selecione **Modificar** para habilitar a limitação de largura de banda de rede. Na caixa de diálogo **limitação** , selecione **habilitar limitação de uso de largura de banda de rede** para ativá-la. Uma vez habilitada, defina as **Configurações** e a **Agenda de Trabalho**.
   - Selecione **Avançar** para deixar a limitação de rede desabilitada.

1. Na tela **Selecionar tipo de recuperação** , selecione **Avançar**. Você só pode recuperar seus arquivos ou pastas em uma pasta de rede.

1. Na tela **especificar destino** , selecione **procurar** para localizar um local de rede para seus arquivos ou pastas. Servidor de Backup do Azure cria uma pasta na qual todos os itens recuperados são copiados. O nome da pasta tem o prefixo MABS_day-mês-ano. Quando você seleciona um local para os arquivos ou a pasta recuperados, os detalhes desse local são fornecidos.

   ![Especificar o local para recuperar arquivos](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na tela **Especificar Opções de Recuperação**, escolha quais configurações de segurança aplicar. Você pode optar por modificar a limitação do uso de largura de banda de rede, mas a limitação é desabilitada por padrão. Além disso, a recuperação e a **notificação** de **San** não estão habilitadas.

1. Na tela **Resumo** , examine as configurações e selecione **recuperar** para iniciar o processo de recuperação. A tela **Status de recuperação** mostra o progresso da operação de recuperação.

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou o backup de suas VMs de solução do Azure VMware com Servidor de Backup do Azure, convém aprender sobre: 

- [Solução de problemas ao configurar backups no servidor de backup do Azure](../backup/backup-azure-mabs-troubleshoot.md).
- [Gerenciamento do ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
