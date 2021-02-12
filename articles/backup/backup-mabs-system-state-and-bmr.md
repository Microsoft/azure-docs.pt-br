---
title: Proteção de recuperação bare-metal e estado do sistema
description: Use Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de BMR (recuperação bare-metal).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021615"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Fazer backup do estado do sistema e restaurar para bare-metal usando Servidor de Backup do Azure

Use o Servidor de Backup do Azure para fazer backup do estado do sistema e fornecer proteção de recuperação bare-metal (BMR).

* **Backup de estado do sistema**: faz backup dos arquivos do sistema operacional. Esse backup permite que você recupere quando um computador é iniciado, mas os arquivos do sistema e do registro são perdidos. Um backup de estado do sistema inclui os seguintes elementos:
  * Membro do domínio: arquivos de inicialização, banco de dados de registro da classe COM+, registro
  * Controlador de domínio: Windows Server Active Directory (NTDS), arquivos de inicialização, o banco de dados de registro da classe COM+, o registro, o volume do sistema (SYSVOL)
  * Computador que executa os serviços de cluster: metadados do servidor de Cluster
  * Computador que executa os serviços de certificados: dados de certificado
* **Backup bare-metal**: faz backup de arquivos do sistema operacional e de todos os dados em volumes críticos, exceto os dados do usuário. Por definição, um backup de BMR inclui um backup de estado do sistema. Ele oferece proteção quando um computador não será iniciado e você precisa recuperar tudo.

A tabela a seguir resume o que você pode fazer backup e recuperar. Para obter informações sobre as versões de aplicativo que o estado do sistema e a BMR podem proteger, consulte [o que servidor de backup do Azure backup?](backup-mabs-protection-matrix.md).

|Backup|Problema|Recuperar de backup do Servidor de Backup do Azure|Recuperar do backup de estado do sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dados de arquivos**<br /><br />Backup de dados regular<br /><br />BMR/backup de estado do sistema|Dados de arquivos perdidos|S|N|N|
|**Dados de arquivos**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Sistema operacional perdido ou danificado|N|S|S|
|**Dados de arquivos**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Servidor perdido (volumes de dados intactos)|N|N|S|
|**Dados de arquivos**<br /><br />Backup de arquivos de dados do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Servidor perdido (volumes de dados perdidos)|S|N|S<br /><br />BMR, seguida de recuperação regular de dados de arquivos de backup|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Site perdido, listas, itens de lista, documentos|S|N|N|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Sistema operacional perdido ou danificado|N|S|S|
|**Dados do SharePoint**<br /><br />Backup de dados do farm do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Recuperação de desastres|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />BMR/backup de estado do sistema do host|VM perdida|S|N|N|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />BMR/backup de estado do sistema do host|Sistema operacional perdido ou danificado|N|S|S|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />BMR/backup de estado do sistema do host|Host Hyper-V perdido (VMs intactas)|N|N|S|
|Hyper-V<br /><br />Backup do Servidor de Backup do Azure do host do Hyper-V ou convidado<br /><br />BMR/backup de estado do sistema do host|Host Hyper-V perdido (VMs perdidas)|N|N|S<br /><br />BMR, seguida de recuperação regular do Servidor de Backup do Azure|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Dados de aplicativos perdidos|S|N|N|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Sistema operacional perdido ou danificado|N|S|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Servidor perdido (banco de dados/logs de transação intactos)|N|N|S|
|SQL Server/Exchange<br /><br />Backup de aplicativo do Servidor de Backup do Azure<br /><br />BMR/backup de estado do sistema|Servidor perdido (banco de dados/logs de transação perdidos)|N|N|S<br /><br />Recuperação de BMR, seguida de recuperação regular do Servidor de Backup do Azure|

## <a name="how-system-state-backup-works"></a>Como funciona o backup de estado do sistema

Quando um backup de estado do sistema é executado, o Servidor de Backup se comunica com o Backup do Windows Server para solicitar um backup de estado do sistema do servidor. Por padrão, o Servidor de Backup e o Backup do Windows Server usam a unidade que tenha mais espaço livre. As informações sobre essa unidade são salvas no arquivo de *PSDataSourceConfig.xml* .

Você pode personalizar a unidade que o servidor de backup usa para o backup de estado do sistema:

1. No servidor protegido, vá para *c:\Arquivos de Programas\microsoft Data Protection Manager\MABS\Datasources*.
1. Abra o arquivo *PSDataSourceConfig.xml* para edição.
1. Altere o valor \<FilesToProtect\> para a letra da unidade.
1. Salve e feche o arquivo.

Se um grupo de proteção estiver definido para proteger o estado do sistema do computador, execute uma verificação de consistência. Se um alerta for gerado, selecione **Modificar Grupo de proteção** no alerta e, em seguida, conclua as páginas no assistente. Em seguida, execute outra verificação de consistência.

Se o servidor de proteção estiver em um cluster, uma unidade de cluster poderá ser selecionada como a unidade que tem mais espaço livre. Se essa propriedade da unidade for alternada para outro nó e um backup de estado do sistema for executado, a unidade não estará disponível e o backup falhará. Nesse cenário, modifique *PSDataSourceConfig.xml* para apontar para uma unidade local.

Em seguida, Backup do Windows Server cria uma pasta chamada *WindowsImageBackup* na raiz da pasta Restore. À medida que o Backup do Windows Server cria o backup, todos os dados são colocados nessa pasta. Quando o backup for concluído, o arquivo será transferido para o computador do servidor de backup. Observe as seguintes informações:

* Essa pasta e seu conteúdo não são limpos quando o backup ou a transferência termina. A melhor maneira de considerar isso é que o espaço é reservado para a próxima vez que um backup for concluído.
* A pasta é criada para cada backup. O carimbo de data/hora e reflete a hora de seu último backup de estado do sistema.

## <a name="how-bmr-backup-works"></a>Como funciona o backup BMR

Para a BMR (incluindo um backup de estado do sistema), o trabalho de backup é salvo diretamente para um compartilhamento no computador do Servidor de Backup. Ele não é salvo em uma pasta no servidor protegido.

Servidor de Backup chama o Backup do Windows Server e compartilha o volume de réplica para esse backup BMR. Nesse caso, ele não exige Backup do Windows Server para usar a unidade que tem mais espaço livre. Em vez disso, ele usa o compartilhamento que foi criado para o trabalho.

Quando o backup for concluído, o arquivo será transferido para o computador do servidor de backup. Os logs são armazenados em *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* BMR não tem suporte para computadores que executam o Windows Server 2003 ou para computadores que executam um sistema operacional cliente.

* Não é possível proteger o BMR e o estado do sistema no mesmo computador em grupos de proteção diferentes.

* Um computador do Servidor de Backup não pode se proteger de BMR.

* A proteção de curto prazo em fita (disco para fita ou D2T) não tem suporte para BMR. Há suporte para o armazenamento de longo prazo em fita (disco para disco para fita ou D2D2T).

* Para proteção de BMR, o Backup do Windows Server deve ser instalado no computador protegido.

* Para proteção de BMR, ao contrário da proteção de estado do sistema, o servidor de backup não tem requisitos de espaço no computador protegido. Backup do Windows Server transfere diretamente os backups para o computador do Servidor de Backup. O trabalho de transferência de backup não aparece na exibição de **trabalhos** do Servidor de Backup.

* O Servidor de Backup reserva 30 GB de espaço no volume de réplica para a BMR. Você pode alterar essa alocação de espaço na página **alocação de disco** no Assistente para modificar grupo de proteção. Ou você pode usar os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell. No volume de ponto de recuperação, a proteção BMR exige cerca de 6 GB para uma retenção de cinco dias.
  * Não é possível reduzir o tamanho do volume de réplica para menos de 15 GB.
  * Servidor de Backup não calcula o tamanho da fonte de dados BMR. Ele presume 30 GB para todos os servidores. Altere o valor com base no tamanho dos backups da BMR esperados no seu ambiente. É possível calcular aproximadamente o tamanho de um backup BMR como a soma do espaço usado em todos os volumes críticos. Volumes críticos = volume de inicialização + volume do sistema + volume que hospeda os dados de estado do sistema, como o Active Directory.

* Se você alterar a proteção de estado do sistema para a proteção BMR, a proteção BMR exigirá menos espaço no *volume de pontos de recuperação*. No entanto, o espaço extra no volume não é recuperado. Você pode reduzir manualmente o tamanho do volume na página **modificar alocação do disco** do assistente para modificar grupo de proteção. Ou você pode usar os cmdlets Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation PowerShell.

    Se você alterar a proteção de estado do sistema para a proteção BMR, a proteção BMR exigirá mais espaço no *volume da réplica*. O volume é estendido automaticamente. Se você quiser alterar as alocações de espaço padrão, use o cmdlet Modify-DiskAllocation PowerShell.

* Se você alterar da proteção BMR para a proteção de estado do sistema, precisará de mais espaço no volume de pontos de recuperação. O Servidor de Backup pode tentar aumentar automaticamente o volume. Se o pool de armazenamento não tiver espaço suficiente, ocorrerá um erro.

    Se você alterar da proteção BMR para a proteção de estado do sistema, precisará de espaço no computador protegido. Você precisa do espaço porque a proteção de estado do sistema primeiro grava a réplica no computador local e, em seguida, transfere a réplica para o computador do servidor de backup.

## <a name="before-you-begin"></a>Antes de começar

1. **Implantar o Servidor de Backup do Azure**. Verifique se o Servidor de Backup é implantado corretamente. Para obter mais informações, consulte:
    * [Requisitos de sistema para o Servidor de Backup do Azure](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de proteção do Servidor de Backup](backup-mabs-protection-matrix.md)

1. **Configure o armazenamento**. Você pode armazenar dados de backup em disco, fita e na nuvem com o Azure. Para obter mais informações, consulte [Preparar dados de armazenamento](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Configurar o agente de proteção**. Instale o agente de proteção no computador que você deseja fazer backup. Para obter mais informações, consulte [Implantar o agente de proteção do DPM](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Fazer backup de estado do sistema e bare-metal

Para fazer backup do estado do sistema e bare-metal:

1. Para abrir o assistente para criar novo grupo de proteção criar, na console do administrador servidor de backup, selecione ações de **proteção**  >  **Actions**  >  **Criar grupo de proteção**.

1. Na página **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores** e, depois, selecione **Avançar**.

1. Na página **Selecionar membros do grupo**, expanda o computador e, em seguida, selecione **BMR** ou **o estado do sistema**.

    Observe que você não pode proteger o estado do sistema e BMR para o mesmo computador em grupos diferentes. Além disso, quando você seleciona a BMR, o estado do sistema é habilitado automaticamente. Para obter mais informações, consulte [Implantar Grupos de proteção](/system-center/dpm/create-dpm-protection-groups).

1. Na página **selecionar método de proteção de dados** , escolha como lidar com backup de curto prazo e backup de longo prazo.

    O backup de curto prazo é sempre para o disco em primeiro lugar, com a opção de fazer backup do disco para o Azure usando o backup do Azure (curto ou longo prazo). Uma alternativa para backup de longo prazo para a nuvem é configurar o backup de longo prazo para um dispositivo de fita autônoma ou biblioteca de fitas que está conectada ao Servidor de Backup.

1. Na página **selecionar metas de Short-Term** , escolha como fazer backup em armazenamento de curto prazo em disco:
    * Para **período de retenção**, escolha por quanto tempo manter os dados em disco.
    * Para **frequência de sincronização**, escolha a frequência de execução de um backup incremental em disco. Se você não quiser definir um intervalo de backup, poderá selecionar **logo antes de um ponto de recuperação**. O servidor de backup executará um backup completo expresso logo antes de cada ponto de recuperação ser agendado.

1. Se você quiser armazenar dados em fita para armazenamento de longo prazo, na página **especificar metas de Long-Term** , escolha por quanto tempo manter os dados da fita (de 1 a 99 anos).
    1. Para **frequência de backup**, escolha a frequência de execução do backup em fita. A frequência é baseada no período de retenção selecionado:
        * Quando o período de retenção for de 1 a 99 anos, você poderá fazer backup diário, semanal, quinzenal, mensal, trimestral, semestral ou anual.
        * Quando o período de retenção é de 1 a 11 meses, você pode fazer backup diário, semanal, quinzenal ou mensal.
        * Quando o período de retenção for de 1 a 4 semanas, você poderá fazer backup diário ou semanal.

    1. Na página **selecionar detalhes de fita e biblioteca** , selecione a fita e a biblioteca a serem usadas. Escolha também se os dados devem ser compactados e criptografados.

1. Na página **rever alocação do disco** , examine o espaço em disco do pool de armazenamento que está disponível para o grupo de proteção.

    * **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup.
    * **Espaço em disco para ser provisionado no Servidor de Backup do Azure** é o espaço que o Servidor de Backup recomenda para o grupo de proteção. O servidor de backup usa essas configurações para escolher o volume de backup ideal. Você pode editar as opções de volume de backup em **detalhes de alocação de disco**.
    * Para cargas de trabalho, no menu suspenso, selecione o armazenamento preferido. As edições alteram os valores para **Armazenamento Total** e **Armazenamento Disponível** no painel **Armazenamento Disponível em Disco**. Espaço subprovisionado é a quantidade de armazenamento que o servidor de backup sugere que você adiciona ao volume para garantir backups suaves.

1. Na página **escolher método de criação de réplica** , selecione como lidar com a replicação de dados completa inicial.

    Se você optar por replicar pela rede, recomendamos que você escolha um horário de menos movimento. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados offline usando uma mídia removível.

1. Na página **escolher opções de verificação de consistência** , selecione como automatizar as verificações de consistência.

    Você pode escolher executar uma verificação somente quando dados de réplica se tornam inconsistentes ou em um agendamento. Se você não quiser configurar a verificação de consistência automática, poderá executar uma verificação manual a qualquer momento. Para executar uma verificação manual, na área de **Proteção** do Console do Administrador do Servidor de Backup do Azure, clique com o botão direito no grupo de proteção e, depois, selecione **Executar Verificação de Consistência**.

1. Se você optar por fazer backup na nuvem usando o backup do Azure, na página **especificar dados de proteção online** , selecione as cargas de trabalho que você deseja fazer backup no Azure.

1. Na página **especificar agendamento de backup online** , selecione a frequência de backup incremental no Azure.

    Você pode agendar backups para serem executados todos os dias, semana, mês e ano. Você também pode selecionar a data e a hora em que os backups devem ser executados. Os backups poderão ocorrer até duas vezes por dia. Cada vez que um backup é executado, um ponto de recuperação de dados é criado no Azure por meio da cópia dos dados de backup armazenados no disco do servidor de backup.

1. Na página **especificar política de retenção online** , selecione como os pontos de recuperação criados a partir dos backups diários, semanais, mensais e anuais são mantidos no Azure.

1. Na página **Escolher Replicação Online**, selecione como ocorre a replicação inicial completa de dados.

    Você pode replicar pela rede ou fazer backup offline (propagação offline). Um backup offline usa o recurso de importação do Azure. Para obter mais informações, consulte [Fluxo de trabalho de backup offline no Backup do Azure](offline-backup-azure-data-box.md).

1. Na página **Resumo**, examine as configurações. Depois de selecionar **Criar grupo**, ocorre a replicação inicial dos dados. Quando a replicação de dados for concluída, na página **status** , o status do grupo de proteção será **OK**. Os backups acontecem de acordo com as configurações do grupo de proteção.

## <a name="recover-system-state-or-bmr"></a>Recuperar o estado do sistema ou BMR

É possível recuperar o BMR ou o estado do sistema em um local de rede. Se você tiver feito backup de BMR, use o ambiente de recuperação do Windows (WinRE) para iniciar o sistema e conectá-lo à rede. Use o Backup do Windows Server para recuperar de um local de rede. Se você tiver feito backup do estado do sistema, basta usar Backup do Windows Server para recuperar a partir do local de rede.

### <a name="restore-bmr"></a>Restaurar BMR

Para executar a recuperação no computador do servidor de backup:

1. No painel **recuperação** , localize o computador que você deseja recuperar. Em seguida, selecione **recuperação bare-metal**.

1. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

1. Na página  **Selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede**.

1. Na página **especificar destino** , selecione o destino dos dados copiados.

    Lembre-se de que o destino precisa ter espaço suficiente para os dados. Recomendamos que você crie uma nova pasta para o destino.

1. Na página **especificar opções de recuperação** , selecione as configurações de segurança. Em seguida, selecione se deseja usar instantâneos de hardware baseados em SAN (rede de área de armazenamento) para uma recuperação mais rápida. Essa opção só estará disponível se:
    * Você tem uma SAN que fornece essa funcionalidade.
    * Você pode criar e dividir um clone para torná-lo gravável.
    * O computador protegido e o computador do servidor de backup estão conectados à mesma rede.

1. Configure opções de notificação.
1. Na página **Confirmação**, selecione **Recuperar**.

Para configurar o local de compartilhamento:

1. No local de recuperação, vá para a pasta que contém o backup.

1. Compartilhe a pasta que é um nível acima de *WindowsImageBackup* para que a raiz da pasta compartilhada seja a pasta *WindowsImageBackup* .

    Se você não compartilhar essa pasta, Restore não encontrará o backup. Para se conectar usando o WinRE, você precisa de um compartilhamento que pode ser acessado no WinRE com o endereço IP e as credenciais corretos.

Para restaurar o sistema:

1. Inicie o computador no qual você deseja restaurar a imagem usando o DVD do Windows para o sistema que você está restaurando.

1. Na primeira página, verifique as configurações de idioma e localidade. Na página **Instalar**, selecione **Reparar seu computador**.

1. Na página **Opções de recuperação do sistema** , selecione **restaurar o computador usando uma imagem do sistema que você criou anteriormente**.

1. Na página **selecionar um backup de imagem do sistema** , selecione **selecionar uma imagem de sistema**  >  **avançado**  >  **Pesquisar por uma imagem do sistema na rede**. Se um aviso for exibido, selecione **Sim**. Vá para o caminho de compartilhamento, insira as credenciais e, em seguida, selecione o ponto de recuperação. O sistema verifica se há backups específicos que estão disponíveis nesse ponto de recuperação. Selecione o ponto de recuperação que você deseja usar.

1. Na página **escolher como restaurar o backup** , selecione **Formatar e reparticionar discos**. Na página seguinte, verifique as configurações.

1. Para iniciar a restauração, selecione **Concluir**. Uma reinicialização é necessária.

### <a name="restore-system-state"></a>Restaurar o estado do sistema

Para executar a recuperação no servidor de backup:

1. No painel de **Recuperação**, localize o computador que deseja recuperar e selecione **Recuperação Bare-Metal**.

1. Os pontos de recuperação disponíveis são indicados em negrito no calendário. Selecione a data e hora para o ponto de recuperação que você deseja usar.

1. Na página **Selecionar tipo de recuperação** , selecione **copiar para uma pasta de rede**.

1. Na página **especificar destino** , selecione onde os dados são copiados.

    Lembre-se de que o destino selecionado precisa ter espaço suficiente para os dados. Recomendamos que você crie uma nova pasta para o destino.

1. Na página **especificar opções de recuperação** , selecione as configurações de segurança. Em seguida, selecione se deseja usar instantâneos de hardware baseados em SAN para uma recuperação mais rápida. Essa opção só estará disponível se:
    * Você tem uma SAN que fornece essa funcionalidade.
    * Você pode criar e dividir um clone para torná-lo gravável.
    * O computador protegido e o servidor de backup estão conectados à mesma rede.

1. Configure opções de notificação.
1. Na página **Confirmação**, selecione **Recuperar**.

Para executar Backup do Windows Server:

1. Selecione **ações**  >  **recuperar**  >  **este servidor**  >  **Avançar**.

1. Selecione **Outro Servidor**, selecione a página **Especificar Tipo de Local** e, em seguida, selecione **Pasta compartilhada remota**. Digite o caminho para a pasta que contém o ponto de recuperação.

1. Na página **Selecionar Tipo de Recuperação**, selecione **Estado do Sistema**.

1. Na página **Selecionar Local para Recuperação do Estado do Sistema**, selecione **Local Original**.

1. Na página **Confirmação**, selecione **Recuperar**.

1. Após a restauração, reinicie o servidor.

Você também pode executar a restauração do estado do sistema em um prompt de comando:

1. Inicie o Backup do Windows Server no computador que você deseja recuperar.

1. Para obter o identificador de versão, no prompt de comando, digite: 

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Use o identificador de versão para iniciar a restauração de estado do sistema. No prompt de comando, insira:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Confirme que você deseja iniciar a recuperação. Você pode ver o processo na janela do Prompt de Comando. Um log de restauração é criado.

1. Após a restauração, reinicie o servidor.
