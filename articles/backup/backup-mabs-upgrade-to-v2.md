---
title: Instalar o Servidor de Backup do Azure v2 | Microsoft Docs
description: "O Servidor de Backup do Azure v2 oferece recursos aprimorados de backup para proteção de VMs, arquivos e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o Servidor de Backup do Azure v2."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-backup-server-v2"></a>Instalar o Servidor de Backup do Azure v2

Servidor de Backup do Azure ajuda a proteger suas máquinas virtuais (VMs), cargas de trabalho, arquivos e pastas e muito mais. O Servidor de Backup do Azure v2 se baseia no Servidor de Backup do Azure v1 e oferece novos recursos que não estão disponíveis em v1. Para obter uma comparação de recursos entre v1 e v2, consulte [Matriz de proteção do Servidor de Backup do Azure](backup-mabs-protection-matrix.md). 

Os recursos adicionais no Servidor de Backup v2 são uma atualização do Servidor de Backup v1. No entanto, o Servidor de Backup v1 não é um pré-requisito para instalar o Servidor de Backup v2. Se você quiser atualizar do Servidor de Backup v1 para o Servidor de Backup v2, instale o Servidor de Backup v2 no servidor de proteção do Servidor de Backup. As suas configurações do Servidor de Backup existentes permanecem intactas.

Você pode instalar o Servidor de Backup v2 no Windows Server 2012 R2 ou Windows Server 2016. Para tirar proveito dos novos recursos como o moderno armazenamento de backup do System Center 2016 Data Protection Manager, você deve instalar o Servidor de Backup v2 no Windows Server 2016. Antes de atualizar ou instalar o Servidor de Backup v2, leia sobre [os pré-requisitos de instalação](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Servidor de Backup do Azure tem o mesmo código de base do System Center Data Protection Manager. O Servidor de Backup v1 é equivalente ao Data Protection Manager 2012 R2 e o Servidor de Backup v2 é equivalente ao Data Protection Manager 2016. Este artigo ocasionalmente faz referência à documentação do Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Atualizar Servidor de Backup para v2
Para atualizar do Servidor de Backup v1 para o Servidor de Backup v2, verifique se a instalação tem as atualizações necessárias:

- [Atualização dos agentes de proteção](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent) nos servidores protegidos.
- Atualização do Windows Server 2012 R2 para o Windows Server 2016.
- Atualização do administrador remoto do Servidor de Backup do Azure em todos os servidores de produção.
- Certifique-se de que os backups estão definidos para continuar sem reiniciar o servidor de produção.


### <a name="upgrade-steps-for-backup-server-v2"></a>Etapas de atualização para o Servidor de Backup v2

1. No Centro de Download, [baixar o instalador de atualização](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Depois de extrair o assistente de instalação, certifique-se de que **Executar setup.exe** está selecionado e, em seguida, selecione **Concluir**.

  ![Instalador de instalação - Execute a instalação](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. No assistente do Servidor de Backup do Microsoft Azure, em **Instalar**, selecione **Microsoft Azure Backup Server**.

  ![Instalador de instalação - Selecione instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na página **Inicial**, examine os avisos, e em seguida selecione **Avançar**.

  ![Instalador de instalação - Página Inicial](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. O assistente de instalação executa as verificações de pré-requisitos para certificar de que seu ambiente pode ser atualizado. Na página **Verificações de pré-requisitos**, selecione **Verificar**.

  ![Instalador de instalação - página Verificações de Pré-requisitos](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Seu ambiente deve passar pelas verificações de pré-requisito. Se seu ambiente não atender as verificações, observe os problemas e corrija-os. Em seguida, selecione **Verificar Novamente**. Depois de passar as verificações de pré-requisitos, selecione **Avançar**.

  ![Instalador de instalação - Botão Verificar Novamente](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na página **Configurações do SQL**, selecione a opção relevante para sua instalação do SQL e, em seguida, selecione **Verificar e Instalar**.

  ![Instalador de instalação - Página Configurações do SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  As verificações podem levar alguns minutos. Quando as verificações tiverem terminado, selecione **Avançar**.

  ![Instalador de instalação - botão de Instalação e Verificação de Configurações do SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. Na página **Configurações de instalação**, faça as alterações para o local onde o Servidor de Backup está instalado, ou para o Local de Rascunho. Selecione **Avançar**.

  ![Instalador de instalação - página de Configurações de Instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Para concluir o assistente de instalação, selecione **Concluir**.

  ![Instalador de instalação - Concluir](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Adicionar armazenamento para Armazenamento de Backup moderno

Para melhorar a eficiência do armazenamento de backup, o Servidor de Backup v2 adiciona suporte para volumes. Como o Servidor de Backup v1, o Servidor de Backup v2 oferece suporte a discos.

### <a name="add-volumes-and-disks"></a>Adicionar discos e volumes
Se você executar o Servidor de Backup v2 no Windows Server 2016, você pode usar volumes para armazenar dados de backup. Os volumes oferecem economia de armazenamento e backups mais rápidos. Como os volumes são novos para o Servidor de Backup, você deve adicioná-los. 

Quando você adicionar um volume para o Servidor de Backup, você pode dar um nome amigável ao volume. Clique na coluna **Nome amigável** do volume que você deseja nomear. Você pode alterar o nome posteriormente, se necessário. Você também pode usar o PowerShell para adicionar ou alterar os nomes amigáveis dos volumes.

Para adicionar um volume no Console do Administrador:

1. No Console do Administrador do Servidor de Backup do Azure, selecione **Gerenciamento** > **Armazenamento em Disco** > **Adicionar**.

    ![Abra o assistente para Adicionar Armazenamento em Disco](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Isso abre o assistente para Adicionar Armazenamento em Disco.

2. Na página **Adicionar Armazenamento em Disco**, na caixa **Volumes disponíveis**, selecione um volume e, em seguida, selecione **Adicionar**.
3. Na caixa **Volumes selecionados**, digite um nome amigável para o volume e, em seguida, selecione **OK**.

      ![Assistente para Adicionar Armazenamento em Disco - Adicionar volume](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Se você quiser adicionar um disco, o disco deve pertencer a um grupo de proteção que tem armazenamento herdado. Esses discos podem ser usados apenas para esses grupos de proteção. Se o Servidor de Backup não tiver fontes que têm proteção herdada, o disco não está listado.

  Para obter mais informações sobre como adicionar discos, consulte [Adicionando discos para aumentar o armazenamento herdado](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Você não pode dar um nome amigável para um disco.


### <a name="assign-workloads-to-volumes"></a>Atribuir as cargas de trabalho para volumes

No Servidor de Backup, você especificar as cargas de trabalho que estão atribuídas aos volumes. Por exemplo, você pode definir volumes caros que dão suporte a um grande número de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que exigem backups frequentes de alto volume. Um exemplo é o SQL Server com logs de transação.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Para atualizar as propriedades de um volume no pool de armazenamento no Servidor de Backup, use o cmdlet Update-DPMDiskStorage do PowerShell .

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Todas as alterações feitas por meio do PowerShell são refletidas na interface do usuário.


## <a name="protect-data-sources"></a>Proteger fontes de dados
Para começar a proteger as fontes de dados, crie um grupo de proteção. As etapas a seguir destacam as alterações ou adições ao Assistente de Novo Grupo de Proteção.

Para criar um grupo de proteção:

1. No Console do Administrador do Servidor de Backup, selecione **Proteção**.

2. Na faixa de opções da ferramenta, selecione **Novo**.

    Isso abre o Assistente para Criar Novo Grupo de Proteção.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Sobre o **boas-vindas** página, selecione **próxima**.
4. Na página **Selecionar Tipo de Grupo de Proteção**, selecione o tipo de grupo de proteção que você deseja criar e, em seguida, selecione **Avançar**.

  ![Selecionar a página do Tipo de Grupo de Proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na página **Selecionar membros do grupo**, no painel **Membros disponíveis**, os membros com agentes de proteção estão listados. Neste exemplo, selecione o volume D:\ e E:\ e adicione-os para o painel **Membros selecionados**. Selecione **Avançar**.

  ![Selecionar a página Membros do Grupo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na página **Selecionar Método de Proteção de Dados**, insira um **nome do Grupo de Proteção** e selecione o método de proteção e, em seguida, selecione **Avançar**. Se desejar a proteção de curto prazo, você deve selecionar o método de backup do **Disco**.

  ![Selecionar a página Método de Proteção de Dados](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na página **Especificar Objetivos de Curto Prazo**, selecione os detalhes de **Período de retenção** e **Frequência de sincronização**. Em seguida, selecione **Avançar**. Opcionalmente, para alterar o agendamento para quando os pontos de recuperação são obtidos, selecione **Modificar**.

  ![Especificar a página Objetivos de Curto Prazo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na página **Examinar Alocação de Armazenamento do Disco**, examinar detalhes sobre as fontes de dados selecionadas, seu tamanho e seus valores para o espaço a ser provisionado e o volume de armazenamento de destino.

  ![Página Examinar Alocação de Armazenamento de Disco](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Volumes de armazenamento são baseados na alocação de volume de carga de trabalho (definido usando o PowerShell) e o armazenamento disponível. Você pode alterar os volumes de armazenamento selecionando outros volumes no menu suspenso. Se você alterar o valor de **Armazenamento de Destino**, o valor de **Armazenamento em Disco Disponível** dinamicamente altera para refletir os valores em **Espaço Livre** e **Espaço Aprovisionado**.

  Se as fontes de dados crescem conforme planejado, o valor para a coluna **Espaço Aprovisionado** no **Armazenamento em Disco Disponível** reflete a quantidade de armazenamento adicional é necessário. Use esse valor para ajudar a planejar suas necessidades de armazenamento de backups estáveis. Se o valor for zero, não há nenhum problema potencial com o armazenamento no futuro próximo. Se o valor for um número diferente de zero, você não tem armazenamento suficiente alocado (com base em sua política de proteção e o tamanho dos dados de seus membros protegidos).

  ![Armazenamento em disco subalocado](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Para terminar de criar seu grupo de proteção, conclua o assistente.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar o armazenamento herdado para o Armazenamento de Backup Moderno
Depois de atualizar ou instalar o Servidor de Backup v2 e atualizar o sistema operacional para o Windows Server 2016, atualize seus grupos de proteção para usar o Armazenamento de Backup Moderno. Por padrão, os grupos de proteção não são alterados. Eles continuam a funcionar como foram configurados inicialmente. 

Atualizar grupos de proteção para usar o Armazenamento de Backup Moderno é opcional. Para atualizar o grupo de proteção, interrompa a proteção de todas as fontes de dados usando a opção manter dados. Em seguida, adicione as fontes de dados para um novo grupo de proteção.

1. No Console do Administrador, selecione a funcionalidade **Proteção**. Na lista **Membro do Grupo de Proteção**, clique com o botão direito no membro e, em seguida, selecione **Interromper proteção do membro**.

  ![Interromper a proteção de membro](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa de diálogo **Remover do grupo**, examine o espaço em disco usado e o espaço livre disponível no pool de armazenamento. O padrão é deixar os pontos de recuperação no disco e permitir a expiração por sua política de retenção associada. Clique em **OK**.

  Se você quiser retornar imediatamente o espaço em disco usado para o pool de armazenamento livre, selecione a caixa de seleção **Excluir réplica no disco** para excluir os dados de backup (e pontos de recuperação) associado a esse membro.

  ![Remover da caixa de diálogo Grupo](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que usa o Armazenamento de Backup Moderno. Inclua as fontes de dados desprotegidos.


## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento herdado

Se você quiser usar armazenamento herdado com o Servidor de Backup, talvez seja preciso adicionar discos para aumentar o armazenamento legado. 

Para adicionar armazenamento em disco:

1. No Console do Administrador, selecione **Gerenciamento** > **Armazenamento em Disco** > **Adicionar**.

    ![Adicionar caixa de diálogo de Armazenamento em Disco](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Na caixa de diálogo **Adicionar Armazenamento em Disco**, selecione **Adicionar discos**.

5. Na lista de discos disponíveis, selecione os discos que você deseja adicionar, selecione **Adicionar**e, em seguida, selecione **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Atualizar o agente de proteção do Data Protection Manager

O Servidor de Backup usa o agente de proteção do System Center Data Protection Manager para atualizações. Se você estiver atualizando um agente de proteção que não está conectado à rede, você não pode usar o Console do Administrador do Data Protection Manager para concluir uma atualização de agente conectado. Você deve atualizar o agente de proteção em um ambiente de domínio não ativo. Até que o computador cliente esteja conectado à rede, o Console do Administrador do Data Protection Manager mostrará que a atualização do agente de proteção está pendente.

As seções a seguir descrevem como atualizar os agentes de proteção para computadores cliente que estão conectados e os computadores cliente que não estão conectados.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Atualizar um agente de proteção de um computador cliente conectado

1. No Console do Administrador do Servidor de Backup, selecione **Gerenciamento** > **Agentes**.

2. No painel de exibição, selecione os computadores cliente para o qual você deseja atualizar o agente de proteção.

  > [!NOTE]
  > A coluna de **Atualizações de Agente** indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **Ações**, a ação de **Atualização** está disponível somente quando um computador protegido está selecionado e as atualizações estão disponíveis.
  >
  >

3. Para instalar agentes de proteção atualizados nos computadores selecionados, no painel **Ações**, selecione **Atualização**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Atualizar um agente de proteção em um computador cliente que não está conectado

1. No Console do Administrador do Servidor de Backup, selecione **Gerenciamento** > **Agentes**.

2. No painel de exibição, selecione os computadores cliente para o qual você deseja atualizar o agente de proteção.

  > [!NOTE]
   > A coluna de **Atualizações de Agente** indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **Ações**, a ação de **Atualização** não está disponível quando um computador protegido estiver selecionado salvo se as atualizações estiverem disponíveis.
  >
  >

3. Para instalar agentes de proteção atualizados nos computadores selecionados, selecione **Atualização**.

4. Para um computador cliente que não está conectado à rede, até que o computador esteja conectado à rede, a coluna do **Status do Agente** mostra um status de **Atualização Pendente**.

  Depois que um computador cliente está conectado à rede, a coluna **Atualizações de Agente** para o computador cliente mostra um status de **Atualizando**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Mover grupos de proteção herdados de uma versão antiga e sincronizar a nova versão com o Azure

Depois que o Servidor de Backup do Azure e o sistema operacional são atualizados, você está pronto para proteger novas fontes de dados usando armazenamento de backup moderno. Independentemente de já estarem protegidas anteriormente, as fontes de dados continuarão a estar protegidas do modo herdado assim como estavam no Servidor de Backup do Azure, mas todas as novas proteções usarão o armazenamento de backup moderno.

As etapas abaixo são para migrar fontes de dados de modo herdado de proteção para o armazenamento de backup moderno.

• Adicione os novos volumes ao pool de armazenamento do DPM e atribua marcas de fonte de dados e nomes amigáveis, se desejado.
• Para cada fonte de dados que está no modo herdado, interrompa a proteção das fontes de dados e a opção "Reter Dados Protegidos".  Isso permitirá a recuperação de pontos de recuperação antigos após a migração.

• Crie um novo grupo de proteção e selecione as fontes de dados que serão armazenadas usando o novo formato.
• O DPM fará uma cópia de réplica do armazenamento de backup herdado para o volume de armazenamento de backup moderno localmente.
Observação: Isso será visto como um trabalho de operação pós-recuperação • Todos os novos pontos de sincronização e de recuperação serão então armazenados no armazenamento de backup moderno.
• Pontos de recuperação antigos serão removidos conforme expirarem e, eventualmente, liberarão o espaço em disco.
• Depois que todos os volumes herdados são excluídos do armazenamento antigo, o disco pode ser removido do backup do Azure e do sistema.
• Faça um backup do DPMDB do Azure.

Parte 2: -Itens importantes> O novo servidor precisará receber o mesmo nome que o servidor de Backup do Azure original. Você não poderá alterar o nome do novo servidor de backup do Azure se você quiser usar o pool de armazenamento antigo e o DPMDB para manter os pontos de recuperação – será necessário ter o backup do DPMDB, já que ele precisará ser restaurado

1) Desligue o servidor de Backup do Azure original ou retire-o da rede.
2) Redefina a conta do computador no Active Directory.
3) Instale o Server 2016 no novo computador e dê a ele o mesmo nome de computador do servidor de Backup do Azure original.
4) Ingressar no domínio
5) Instalar o servidor de Backup do Azure V2 (mover discos do pool de armazenamento do DPM do servidor antigo e importar)
6) Restaurar o DPMDB retirado do final da parte 2
7) Anexe o armazenamento do servidor de backup original ao novo servidor.
8) Do SQL, restaurar o DPMDB
9) Na linha de comando de administrador no novo servidor, vá para o diretório do local de instalação do Backup do Microsoft Azure e a pasta bin

Exemplo de caminho: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
para o backup do Azure. Execute DPMSYNC-SYNC

10) Execute DPMSYNC -SYNC Observação Se você adicionou NOVOS discos ao pool de armazenamento do DPM em vez de mover os antigos, execute DPMSYNC -Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Novo cmdlets do PowerShell em v2

Quando você instala o Servidor de Backup do Azure v2, dois novos cmdlets estão disponíveis: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Próximas etapas

Saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho:
- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Usar o Armazenamento de Backup Moderno com o Servidor de Backup](backup-mabs-add-storage.md)

