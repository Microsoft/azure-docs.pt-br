---
title: Fazer backup do Azure Managed Disks
description: Saiba como fazer backup do Azure Managed Disks do portal do Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: e234495eb483d6d0cc6ca556ca418138c61a99f5
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110620"
---
# <a name="back-up-azure-managed-disks"></a>Fazer backup do Azure Managed Disks

Este artigo explica como fazer backup do [disco gerenciado do Azure](../virtual-machines/managed-disks-overview.md) no portal do Azure.

Neste artigo, você aprenderá a:

- Criar um cofre de backup

- Criar uma política de backup

- Configurar um backup de um disco do Azure

- Executar um trabalho de backup sob demanda

Para obter informações sobre a disponibilidade da região de backup em disco do Azure, cenários e limitações com suporte, consulte a [matriz de suporte](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Criar um cofre de backup

Um cofre de backup é uma entidade de armazenamento no Azure que mantém dados de backup para várias cargas de trabalho mais recentes com suporte no backup do Azure, como o banco de dados do Azure para servidores PostgreSQL e discos do Azure. Os cofres de backup facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento. Os cofres de backup são baseados no modelo de Azure Resource Manager do Azure, que fornece recursos avançados para ajudar a proteger os dados de backup.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
1. Digite **centro de backup** na caixa de pesquisa.
1. Em **Serviços**, selecione **centro de backup**.
1. Na página **centro de backup** , selecione **cofre**.

   ![Selecionar cofre no centro de backup](./media/backup-managed-disks/backup-center.png)

1. Na tela **Iniciar: criar cofre** , selecione **cofre de backup** e **continue**.

   ![Iniciar: criar cofre](./media/backup-managed-disks/initiate-create-vault.png)

1. Na guia **noções básicas** , forneça assinatura, grupo de recursos, nome do cofre de backup, região e redundância de armazenamento de backup. Continue selecionando **revisar + criar**. Saiba mais sobre como [criar um cofre de backup](./backup-vault-overview.md#create-a-backup-vault).

   ![Revisar e criar cofre](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Criar política de backup

1. No **cofre de backup** do *DemoVault* criado na etapa anterior, vá para **políticas de backup** e selecione **Adicionar**.

   ![Adicionar política de backup](./media/backup-managed-disks/backup-policies.png)

1. Na guia **noções básicas** , forneça o nome da política, selecione o **tipo DataSource** como **disco do Azure**. O cofre já foi preenchido previamente e as propriedades do cofre selecionado são apresentadas.

   >[!NOTE]
   > Embora o cofre selecionado possa ter a configuração de redundância global, atualmente o backup em disco do Azure dá suporte apenas ao armazenamento de instantâneos de snapshot. Todos os backups são armazenados em um grupo de recursos em sua assinatura e não são copiados para o armazenamento do cofre de backup.

   ![Selecionar tipo de fonte de origem](./media/backup-managed-disks/datasource-type.png)

1. Na guia **política de backup** , selecione a frequência de agendamento de backup.

   ![Selecionar frequência de agendamento de backup](./media/backup-managed-disks/backup-schedule-frequency.png)

   O backup em disco do Azure oferece vários backups por dia. Se você precisar de backups mais frequentes, escolha a frequência de backup por **hora** com a capacidade de fazer backups com intervalos de cada 4, 6, 8 ou 12 horas. Os backups são agendados com base no intervalo de **tempo** selecionado. Por exemplo, se você selecionar a **cada 4 horas**, os backups serão feitos em aproximadamente no intervalo de cada 4 horas para que os backups sejam distribuídos igualmente ao longo do dia. Se um backup de um dia for suficiente, escolha a frequência de backup **diária** . Na frequência de backup diário, você pode especificar a hora do dia em que os backups são feitos. É importante observar que a hora do dia indica a hora de início do backup e não a hora em que o backup foi concluído. O tempo necessário para concluir a operação de backup depende de vários fatores, incluindo o tamanho do disco e a taxa de rotatividade entre backups consecutivos. No entanto, o backup em disco do Azure é um backup sem agente que usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md), o que não afeta o desempenho do aplicativo de produção.

1. Na guia **política de backup** , selecione as configurações de retenção que atendem ao requisito de objetivo de ponto de recuperação (RPO).

   A regra de retenção padrão se aplicará se nenhuma outra regra de retenção for especificada. A regra de retenção padrão pode ser modificada para alterar a duração da retenção, mas não pode ser excluída. Você pode adicionar uma nova regra de retenção selecionando **Adicionar regra de retenção**.

   ![Adicionar uma regra de retenção](./media/backup-managed-disks/add-retention-rule.png)

   Você pode escolher o **primeiro backup bem-sucedido** feito diariamente ou semanalmente e fornecer a duração da retenção de que os backups específicos devem ser retidos antes de serem excluídos. Essa opção é útil para reter backups específicos do dia ou da semana para uma duração mais longa do tempo. Todos os outros backups frequentes podem ser retidos por uma duração menor.

   ![Configurações de retenção](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >O backup do Azure para Managed Disks usa instantâneos incrementais limitados a 200 instantâneos por disco. Para permitir que você faça backups sob demanda além dos backups agendados, a política de backup limita o total de backups a 180. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) do disco gerenciado.

1. Conclua a criação da política de backup selecionando **examinar + criar**.

## <a name="configure-backup"></a>Configurar backup

O cofre de backup usa identidade gerenciada para acessar outros recursos do Azure. Para configurar o backup de discos gerenciados, a identidade gerenciada do cofre de backup requer um conjunto de permissões nos discos de origem e nos grupos de recursos em que os instantâneos são criados e gerenciados.

Uma identidade gerenciada atribuída pelo sistema é restrita a uma por recurso e está vinculada ao ciclo de vida deste recurso. Você pode conceder permissões para a identidade gerenciada usando o controle de acesso baseado em função do Azure (RBAC do Azure). A identidade gerenciada é uma entidade de serviço de um tipo especial que pode ser usada apenas com recursos do Azure. Saiba mais sobre [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

Os seguintes pré-requisitos são necessários para configurar o backup de discos gerenciados:

1. Atribua a função **leitor de backup de disco** à identidade gerenciada do cofre de backup no disco de origem cujo backup deve ser feito.

   1. Vá para o disco que precisa ser submetido a backup.

   1. Vá para **controle de acesso (iam)** e selecione **Adicionar atribuições de função**

   1. No painel de contexto à direita, selecione **leitor de backup em disco** na lista suspensa **função** . Selecione a identidade gerenciada do cofre de backup e **salve**.

   >[!TIP]
   >Digite o nome do cofre de backup para selecionar a identidade gerenciada do cofre.

   ![Adicionar função de leitor de backup de disco](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Atribua a função de **colaborador de instantâneo de disco** à identidade gerenciada do cofre de backup no grupo de recursos em que os backups são criados e gerenciados pelo serviço de backup do Azure. Os instantâneos de disco são armazenados em um grupo de recursos em sua assinatura. Para permitir que o serviço de backup do Azure crie, armazene e gerencie instantâneos, você precisa fornecer permissões para o cofre de backup.

   **Escolhendo o grupo de recursos para armazenar e gerenciar instantâneos:**

   - Não selecione o mesmo grupo de recursos do disco de origem.

   - Como uma diretriz, é recomendável criar um grupo de recursos dedicado como um repositório de armazenamento de instantâneo a ser usado pelo serviço de backup do Azure. Ter um grupo de recursos dedicado permite restringir as permissões de acesso no grupo de recursos, fornecendo segurança e facilidade de gerenciamento dos dados de backup.

   - Você pode usar esse grupo de recursos para armazenar instantâneos em vários discos que estão sendo incluídos no backup (ou planejado).  

   - Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco. Portanto, escolha o grupo de recursos na mesma assinatura do disco do qual será feito o backup. Saiba mais sobre o [instantâneo incremental](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos gerenciados.

   Para atribuir a função, siga estas etapas:

   1. Vá para o grupo de recursos. Por exemplo, o grupo de recursos é *SnapshotRG*, que está na mesma assinatura do disco do qual será feito o backup.

   1. Vá para **controle de acesso (iam)** e selecione **Adicionar atribuições de função**.

   1. No painel de contexto à direita, selecione **colaborador de instantâneo de disco** na lista suspensa **função** . Selecione a identidade gerenciada do cofre de backup e **salve**.

   >[!TIP]
   >Digite o nome do cofre de backup para selecionar a identidade gerenciada do cofre.

   ![Adicionar função de colaborador de instantâneo de disco](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Verifique se a identidade gerenciada do cofre de backup tem o conjunto correto de atribuições de função no disco de origem e no grupo de recursos que serve como o repositório de armazenamento de instantâneo.

   1. Vá para **cofre de backup-> identidade** e selecione **atribuições de função do Azure**.

      ![Selecionar atribuições de função do Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Verifique se a função, o nome do recurso e o tipo de recurso estão refletidos corretamente.

      ![Verificar a função, o nome do recurso e o tipo de recurso](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Embora as atribuições de função sejam refletidas corretamente no portal, pode levar aproximadamente 15 minutos para que a permissão seja aplicada na identidade gerenciada do cofre de backup.

1. Depois que os pré-requisitos forem atendidos, vá para **cofre de backup-> visão geral** e selecione **backup** para começar a configurar o backup dos discos.

   ![Selecionar backup](./media/backup-managed-disks/select-backup.png)

1. Na guia **noções básicas** , selecione **Azure Disk** como o tipo DataSource.

   ![Selecionar disco do Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >O backup do Azure usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) de discos gerenciados, que armazenam apenas as alterações delta no disco desde o último instantâneo no armazenamento de HDD Standard, independentemente do tipo de armazenamento do disco pai. Para obter confiabilidade adicional, os instantâneos incrementais são armazenados no armazenamento com redundância de zona (ZRS) por padrão em regiões que dão suporte a ZRS. Atualmente, o backup em disco do Azure dá suporte ao backup operacional de discos gerenciados que não copia os backups para o armazenamento do cofre de backup. Portanto, a configuração de redundância de armazenamento de backup do cofre de backup não se aplica aos pontos de recuperação.

1. Na guia **política de backup** , escolha uma política de backup.

   ![Escolher política de backup](./media/backup-managed-disks/choose-backup-policy.png)

1. Na guia **recursos** , selecione **selecionar disco do Azure**. No painel de contexto à direita, selecione os discos cujo backup será feito.

   ![Selecionar os discos para backup](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Embora o portal permita que você selecione vários discos e configure o backup, cada disco é uma instância de backup individual. Atualmente, o backup em disco do Azure só dá suporte ao backup de discos individuais. Não há suporte para o backup pontual de vários discos anexados a um disco virtual.
   >
   >Ao usar o portal, você está limitado a selecionar os discos na mesma assinatura. Se você tiver vários discos para fazer backup ou se os discos estiverem espalhados por uma assinatura diferente, você poderá usar scripts para automatizar.
   >
   >Para obter mais informações sobre a disponibilidade da região de backup em disco do Azure, cenários e limitações com suporte, consulte a [matriz de suporte](disk-backup-support-matrix.md).

1. Selecione um **grupo de recursos de instantâneo** e selecione **validar**. Esse é o grupo de recursos em que o serviço de backup do Azure criou e gerencia os instantâneos incrementais para o cofre de backup. A identidade gerenciada é atribuída com as permissões de função necessárias como parte dos pré-requisitos.

   ![Selecionar grupo de recursos de instantâneo](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >A validação pode levar alguns minutos para ser concluída antes que você possa concluir a configuração do fluxo de trabalho de backup. A validação poderá falhar se:
   >
   > - Não há suporte para um disco. Consulte a [matriz de suporte](disk-backup-support-matrix.md) para cenários sem suporte.
   > - a identidade gerenciada do cofre de backup não tem atribuições de função válidas no **disco** para fazer backup ou no **grupo de recursos de instantâneo** em que os instantâneos incrementais são armazenados.

1. Após uma validação bem-sucedida, selecione **revisar e configurar** para configurar o backup dos discos selecionados.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

1. No **cofre de backup** do *DemoVault* criado na etapa anterior, vá para **instâncias de backup** e selecione uma instância de backup.

   ![Selecionar instância de backup](./media/backup-managed-disks/select-backup-instance.png)

1. Na tela **instâncias de backup** , você encontrará:

   - informações **essenciais** , incluindo o nome do disco de origem, o grupo de recursos de instantâneo em que os instantâneos incrementais são armazenados, cofre de backup e política de backup.
   - **Status do trabalho** mostrando o resumo das operações de backup e restauração e seu status nos últimos sete dias.
   - Uma lista de **pontos de restauração** para o período de tempo selecionado.

1. Selecione **backup** para iniciar um backup sob demanda.

   ![Selecione fazer backup agora](./media/backup-managed-disks/backup-now.png)

1. Selecione uma das regras de retenção associadas à política de backup. Essa regra de retenção determinará a duração da retenção deste backup sob demanda. Selecione **fazer backup agora** para iniciar o backup.

   ![Iniciar backup](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Rastrear uma operação de backup

O serviço de backup do Azure cria um trabalho para backups agendados ou se você disparar a operação de backup sob demanda para acompanhamento. Para exibir o status do trabalho de backup:

1. Vá para a tela de **instância de backup** . Ele mostra o painel trabalhos com a operação e o status dos últimos sete dias.

   ![Painel de trabalhos](./media/backup-managed-disks/jobs-dashboard.png)

1. Para exibir o status da operação de backup, selecione **Exibir tudo** para mostrar os trabalhos em andamento e passados desta instância de backup.

   ![Selecionar Exibir tudo](./media/backup-managed-disks/view-all.png)

1. Examine a lista de trabalhos de backup e restauração e seu status. Selecione um trabalho na lista de trabalhos para exibir detalhes do trabalho.

   ![Selecione o trabalho para ver os detalhes](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar Managed Disks do Azure](restore-managed-disks.md)