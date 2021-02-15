---
title: Perguntas frequentes-fazendo backup de bancos de dados SQL Server em VMs do Azure
description: Encontre respostas para perguntas comuns sobre como fazer backup de bancos de dados SQL Server em VMs do Azure com o backup do Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: ca785e217da4355a44ffbb26b813d55d942c5c14
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787613"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Perguntas frequentes sobre SQL Server bancos de dados que estão em execução em um backup de VM do Azure

Este artigo responde a perguntas comuns sobre como fazer backup de bancos de dados SQL Server executados em VMs (máquinas virtuais) do Azure e usar o serviço de [backup do Azure](backup-overview.md) .

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Posso usar o backup do Azure para VM IaaS, bem como SQL Server no mesmo computador?

Sim, você pode ter o backup de VM e o backup do SQL na mesma VM. Nesse caso, internamente disparamos o backup completo somente cópia na VM para não truncar os logs.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução tenta novamente ou repara automaticamente os backups?

Em algumas circunstâncias, o serviço de backup do Azure dispara backups remediais. A detecção automática pode ocorrer para qualquer uma das seis condições mencionadas abaixo:

- Se o log ou backup diferencial falhar devido ao erro de validação LSN, o próximo log ou backup diferencial será convertido em um backup completo.
- Se nenhum backup completo tiver ocorrido antes de um log ou backup diferencial, esse log ou backup diferencial será convertido em um backup completo.
- Se o ponto no tempo do backup completo mais recente for anterior a 15 dias, o próximo log ou backup diferencial será convertido em um backup completo.
- Todos os trabalhos de backup que são cancelados devido a uma atualização de extensão são disparados novamente depois que a atualização é concluída e a extensão é iniciada.
- Se você optar por substituir o banco de dados durante a restauração, o próximo backup de log/diferencial falhará e um backup completo será disparado em vez disso.
- Nos casos em que um backup completo é necessário para redefinir as cadeias de logs devido à alteração no modelo de recuperação de banco de dados, um completo é disparado automaticamente na próxima agenda.

Por padrão, a reparo automático como um recurso está habilitado para todos os usuários. No entanto, se você optar por recusar, execute as seguintes etapas:

- Na instância de SQL Server, na pasta *C:\Program Programas\azure Workload Backup\bin* , crie ou edite o **ExtensionSettingsOverrides.jsno** arquivo.
- No **ExtensionSettingsOverrides.jsem**, defina *{"EnableAutoHealer": false}*.
- Salve suas alterações e feche o arquivo.
- Na instância do SQL Server, abra **tarefa gerenciar** e reinicie o serviço **AzureWLBackupCoordinatorSvc** .

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>É possível controlar quantos backups simultâneos são executados no SQL Server?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server. Para alterar a configuração:

1. Na instância de SQL Server, na pasta *C:\Program Programas\azure Workload Backup\bin* , crie o *ExtensionSettingsOverrides.jsno* arquivo.
2. Na *ExtensionSettingsOverrides.jsno* arquivo, altere a configuração **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
O valor padrão de DefaultBackupTasksThreshold é **20**.

3. Salve suas alterações e feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Embora esse método ajude se o aplicativo de backup está consumindo uma grande quantidade de recursos, SQL Server [resource governor](/sql/relational-databases/resource-governor/resource-governor) é uma maneira mais genérica de especificar limites na quantidade de CPU, e/s física e memória que as solicitações de aplicativos de entrada podem usar.

> [!NOTE]
> No UX, você ainda pode prosseguir e agendar quantos backups em um determinado momento. No entanto, eles serão processados em uma janela deslizante de digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?

De acordo com as limitações do SQL, você pode executar cópia somente backup completo na réplica secundária. No entanto, o backup completo não é permitido.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger os grupos de disponibilidade no local?

Não. O backup do Azure protege SQL Server bancos de dados em execução no Azure. Se um AG (grupo de disponibilidade) for distribuído entre o Azure e os computadores locais, o AG poderá ser protegido somente se a réplica primária estiver em execução no Azure. Além disso, o backup do Azure protege somente os nós que são executados na mesma região do Azure que o cofre dos serviços de recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger grupos de disponibilidade entre regiões?

O cofre dos serviços de recuperação de backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o cofre. Se seu SQL Server Always On grupo de disponibilidade abrange várias regiões do Azure, configure o backup da região que tem o nó primário. O Backup do Microsoft Azure poderá detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a sua preferência de backup. Quando a preferência de backup não for atendida, os backups falharão e você receberá o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas do portal é documentado [aqui](backup-azure-monitoring-built-in-monitor.md). No entanto, se você estiver interessado em ter alertas mesmo para trabalhos bem-sucedidos, poderá usar o [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de backup agendados no menu Trabalhos de Backup?

O menu **trabalho de backup** mostra todas as operações agendadas e sob demanda, exceto os backups de log agendados, pois eles podem ser muito frequentes. Para trabalhos de log agendados, use [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

Sim, você pode obter esse recurso com [proteção automática](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância protegida automaticamente, o que acontecerá com os backups?

Se um banco de dados for descartado de uma instância autoprotegida, os backups de banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a ser exibido como não íntegro em **Itens de Backup** e ainda está protegido.

A maneira correta de interromper a proteção deste banco de dados é **interromper o backup** com **excluir os**  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se eu parar a operação de backup de um banco de dados autoprotegido, qual será seu comportamento?

Se você **parar o backup com reter dados**, não ocorrerá nenhum backup futuro e os pontos de recuperação existentes permanecerão intactos. O banco de dados ainda será considerado protegido e será mostrado nos **itens de backup**.

Se você **parar o backup com excluir dados**, nenhum backup futuro será realizado e os pontos de recuperação existentes também serão excluídos. O banco de dados será considerado não protegido e será mostrado na instância no backup de configuração. No entanto, ao contrário de outros bancos de dados protegidos que podem ser selecionados manualmente ou que podem ser protegidos automaticamente, esse banco de dados aparece esmaecido e não pode ser selecionado. A única maneira de proteger novamente esse banco de dados é desabilitar a proteção automática na instância do. Agora você pode selecionar esse banco de dados e configurar a proteção nele ou reabilitar a proteção automática novamente na instância.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu alterar o nome do banco de dados após ele ter sido protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo. Portanto, o serviço tratará essa situação como se o banco de dados não fosse encontrado e com a falha dos backups.

Você pode selecionar o banco de dados, que agora é renomeado e configurar a proteção nele. Se a proteção automática estiver habilitada na instância, o banco de dados renomeado será automaticamente detectado e protegido.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não consigo ver um banco de dados adicionado para uma instância autoprotegida?

Um banco de dados que você [adiciona a uma instância autoprotegida](backup-sql-server-database-azure-vms.md#enable-auto-protection) pode não aparecer imediatamente em itens protegidos. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, você pode descobrir e proteger novos bancos de dados imediatamente se executar uma descoberta manualmente, selecionando **redescobrir bancos**, conforme mostrado na imagem a seguir:

  ![Descobrir manualmente um banco de dados recém-adicionado](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="can-i-protect-databases-on-virtual-machines-that-have-azure-disk-encryption-ade-enabled"></a>Posso proteger bancos de dados em máquinas virtuais que têm o Azure Disk Encryption (ADE) habilitado?
Sim, você pode proteger bancos de dados em máquinas virtuais que tenham o Azure Disk Encryption (ADE) habilitado.

## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Posso proteger os bancos de dados que têm o TDE (Transparent Data Encryption) ativado e que ele continuará criptografado por todo o processo de backup?

Sim, o backup do Azure dá suporte ao backup de bancos de dados SQL Server ou servidor com TDE habilitado. O backup dá suporte a [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) com chaves gerenciadas pelo Azure ou com chaves gerenciadas pelo cliente (BYOK).  O backup não executa nenhuma criptografia SQL como parte do processo de backup, de modo que o banco de dados permanecerá criptografado quando o backup for feito.

## <a name="does-azure-backup-perform-a-checksum-operation-on-the-data-stream"></a>O backup do Azure executa uma operação de soma de verificação no fluxo de dados?

Realizamos uma operação de soma de verificação no fluxo de dados. No entanto, isso não deve ser confundido com a [soma de verificação SQL](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
O backup de carga de trabalho do Azure computa a soma de verificação no fluxo de dados e a armazena explicitamente durante a operação de backup. Esse fluxo de soma de verificação é então obtido como uma referência e verificada de forma cruzada com a soma de verificação do fluxo de dados durante a operação de restauração para garantir que os dados sejam consistentes.

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de um banco de dados SQL Server](backup-azure-sql-database.md) em execução em uma VM do Azure.