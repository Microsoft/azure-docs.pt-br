---
title: Perguntas frequentes sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Backup do Microsoft Azure
description: Encontre respostas para perguntas comuns sobre como fazer backup de bancos de dados SQL Server em VMs do Azure com o backup do Azure.
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vijayts
ms.openlocfilehash: 082817899adc6ab9b4d57f7e0d4bc4e7c2f0a2ab
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464814"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Perguntas frequentes sobre SQL Server bancos de dados que estão em execução em um backup de VM do Azure

Este artigo responde a perguntas comuns sobre o backup de SQL Server bancos de dados que são executados em VMs (máquinas virtuais) do Azure e que usam o serviço de [backup do Azure](backup-overview.md) .

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

A reparo automática como um recurso é habilitado para todos os usuários por padrão; No entanto, caso você opte por recusá-lo, execute o seguinte:

  * Na instância de SQL Server, na pasta *C:\Program Programas\azure Workload Backup\bin* , crie ou edite o arquivo **ExtensionSettingsOverrides. JSON** .
  * No **ExtensionSettingsOverrides. JSON**, defina *{"EnableAutoHealer": false}* .
  * Salve suas alterações e feche o arquivo.
  * Na instância do SQL Server, abra **tarefa gerenciar** e reinicie o serviço **AzureWLBackupCoordinatorSvc** .  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Posso controlar como quantos backups simultâneos são executados no SQL Server?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server. Para alterar a configuração:
1. Na instância de SQL Server, na pasta *C:\Program Programas\azure Workload Backup\bin* , crie o arquivo *ExtensionSettingsOverrides. JSON* .
2. No arquivo *ExtensionSettingsOverrides. JSON* , altere a configuração **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Salve suas alterações e feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Embora esse método ajude se o aplicativo de backup está consumindo muitos recursos, SQL Server [resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) é uma maneira mais genérica de especificar limites na quantidade de CPU, e/s física e memória que as solicitações de aplicativos de entrada podem usar.

> [!NOTE]
> No UX, você ainda pode prosseguir e agendar quantos backups a qualquer momento, no entanto, eles serão processados em uma janela deslizante de digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?
De acordo com as limitações do SQL, você pode executar copiar somente backup completo na réplica secundária; no entanto, o backup completo não é permitido.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger os grupos de disponibilidade no local?
Nº O backup do Azure protege SQL Server bancos de dados em execução no Azure. Se um AG (grupo de disponibilidade) for distribuído entre o Azure e os computadores locais, o AG poderá ser protegido somente se a réplica primária estiver em execução no Azure. Além disso, o backup do Azure protege somente os nós que são executados na mesma região do Azure que o cofre dos serviços de recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger grupos de disponibilidade entre regiões?
O cofre dos serviços de recuperação de backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o cofre. Se seu SQL Server Always On grupo de disponibilidade abrange várias regiões do Azure, configure o backup da região que tem o nó primário. O Backup do Microsoft Azure poderá detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a sua preferência de backup. Quando a preferência de backup não for atendida, os backups falharão e você receberá o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?
Nº Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas do portal está documentado [aqui](backup-azure-monitoring-built-in-monitor.md). No entanto, caso você esteja interessado em ter alertas mesmo para trabalhos bem-sucedidos, você pode usar o [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver os trabalhos de backup agendados no menu trabalhos de backup?
O menu **trabalho de backup** mostrará apenas os trabalhos de backup ad hoc. Para o trabalho agendado, use [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?
Sim, você pode obter esse recurso com [proteção automática](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância autoprotegida, o que acontecerá com os backups?
Se um banco de dados for descartado de uma instância autoprotegida, os backups de banco de dados ainda serão tentados. Isso significa que o banco de dados excluído começa a aparecer como não íntegro em **itens de backup** e ainda está protegido.

A maneira correta de interromper a proteção deste banco de dados é **interromper o backup** com **excluir os**  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se eu parar a operação de backup de um banco de dados autoprotegido, qual será seu comportamento?
Se você **parar o backup com reter dados**, não ocorrerá nenhum backup futuro e os pontos de recuperação existentes permanecerão intactos. O banco de dados ainda será considerado protegido e será mostrado nos **itens de backup**.

Se você **parar o backup com excluir dados**, nenhum backup futuro será realizado e os pontos de recuperação existentes também serão excluídos. O banco de dados será considerado não protegido e será mostrado na instância no backup de configuração. No entanto, ao contrário de outros bancos de dados protegidos que podem ser selecionados manualmente ou que podem ser protegidos automaticamente, esse banco de dados aparece esmaecido e não pode ser selecionado. A única maneira de proteger novamente esse banco de dados é desabilitar a proteção automática na instância do. Agora você pode selecionar esse banco de dados e configurar a proteção nele ou reabilitar a proteção automática novamente na instância.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu alterar o nome do banco de dados após ele ter sido protegido, qual será o comportamento?
Um banco de dados renomeado é tratado como um novo banco de dados. Portanto, o serviço tratará essa situação como se o banco de dados não fosse encontrado e com a falha dos backups.

Você pode selecionar o banco de dados, que agora é renomeado e configurar a proteção nele. Caso a proteção automática esteja habilitada na instância, o banco de dados renomeado será automaticamente detectado e protegido.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não consigo ver um banco de dados adicionado para uma instância autoprotegida?
Um banco de dados que você [adiciona a uma instância autoprotegida](backup-sql-server-database-azure-vms.md#enable-auto-protection) pode não aparecer imediatamente em itens protegidos. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, você pode descobrir e proteger novos bancos de dados imediatamente se executar uma descoberta manualmente, selecionando **recuperar bancos**, conforme mostrado na imagem a seguir.

  ![Descobrir manualmente um banco de dados recém-adicionado](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de um banco de dados SQL Server](backup-azure-sql-database.md) em execução em uma VM do Azure.
