---
title: Restaurar bancos de dados SAP HANA em VMs do Azure
description: Neste artigo, descubra como restaurar SAP HANA bancos de dados que estão em execução em máquinas virtuais do Azure. Você também pode usar a restauração entre regiões para restaurar seus bancos de dados para uma região secundária.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021666"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurar bancos de dados SAP HANA em VMs do Azure

Este artigo descreve como restaurar SAP HANA bancos de dados em execução em uma VM (máquina virtual) do Azure, na qual o serviço de backup do Azure fez backup em um cofre dos serviços de recuperação. As restaurações podem ser usadas para criar cópias dos dados para cenários de desenvolvimento/teste ou para retornar a um estado anterior.

Para obter mais informações, sobre como fazer backup de bancos de dados do SAP HANA, consulte fazer backup de [bancos de dados do SAP Hana em VMs do Azure](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurar para um ponto no tempo ou para um ponto de recuperação

O Backup do Azure pode restaurar bancos de dados SAP HANA que estão em execução em VMs do Azure da seguinte maneira:

* Restauração em uma data ou uma hora específica (com precisão de segundos) usando backups de log. O Backup do Azure determina automaticamente os backups diferenciais completos apropriados e a cadeia de backups de log necessários para a restauração com base na hora selecionada.

* Restauração em um backup completo ou diferencial específico para restauração em um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

* Você pode restaurar o banco de dados somente para uma instância SAP HANA que esteja na mesma região.

* A instância de destino deve ser registrada com o mesmo cofre que a origem.

* O Backup do Azure não pode identificar duas instâncias diferentes do SAP HANA na mesma VM. Portanto, a restauração de dados de uma instância para outra na mesma VM não é possível.

* Para garantir que a instância de SAP HANA de destino esteja pronta para restauração, verifique seu status de **preparação de backup** :

  1. Abra o cofre no qual a instância de SAP HANA de destino está registrada.

  1. No painel do cofre, em **introdução**, escolha **backup**.

      ![Backup no painel do cofre](media/sap-hana-db-restore/getting-started-backup.png)

  1. Em **backup**, em **o que você deseja fazer backup?** escolha **SAP Hana na VM do Azure**.

      ![Escolha SAP HANA na VM do Azure](media/sap-hana-db-restore/sap-hana-backup.png)

  1. Em **descobrir bancos de os em VMs**, selecione **Exibir detalhes**.

      ![Exibir detalhes](media/sap-hana-db-restore/view-details.png)

  1. Examine a **prontidão de backup** da VM de destino.

      ![Servidores protegidos](media/sap-hana-db-restore/protected-servers.png)

* Para saber mais sobre os tipos de restauração aos quais SAP HANA dá suporte, consulte a SAP HANA observação [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar, você precisa das seguintes permissões:

* Permissões de **operador de backup** no cofre em que você está fazendo a restauração.
* Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
* Acesso de **colaborador (gravação**) à VM de destino:
  * Se você estiver restaurando para a mesma VM, esta é a VM de origem.
  * Se você estiver restaurando para um local alternativo, essa será a nova VM de destino.

1. Abra o cofre no qual o banco de dados do SAP HANA a ser restaurado está registrado

1. No painel do cofre, em **itens protegidos**, escolha **itens de backup**

    ![Itens de Backup](media/sap-hana-db-restore/backup-items.png)

1. Em **itens de backup**, em **tipo de gerenciamento de backup** , selecione **SAP Hana na VM do Azure**

    ![Tipo de gerenciamento de backup](media/sap-hana-db-restore/backup-management-type.png)

1. Selecione o banco de dados a ser restaurado

    ![Banco de dados a ser restaurado](media/sap-hana-db-restore/database-to-restore.png)

1. Examine o menu do banco de dados. Ele fornece informações sobre o backup de banco de dados, incluindo:

    * Os pontos de restauração mais antigos e mais recentes

    * O status do backup de log das últimas 24 e 72 horas para o banco de dados

    ![Menu banco de dados](media/sap-hana-db-restore/database-menu.png)

1. Selecione **restaurar BD**

1. Em **Restaurar configuração**, especifique onde (ou como) restaurar os dados:

    * **Local alternativo**: restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.

    * **Substituir BD**: restaure os dados para a mesma instância de SAP Hana como a fonte original. Essa opção substitui o banco de dados original.

      ![Restaurar configuração](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restauração em uma localização alternativa

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **local alternativo**.

    ![Restauração em uma localização alternativa](media/sap-hana-db-restore/restore-alternate-location.png)

1. Selecione o nome do host SAP HANA e o nome da instância para o qual você deseja restaurar o banco de dados.
1. Verifique se a instância de SAP HANA de destino está pronta para restauração garantindo sua **prontidão de backup.** Consulte a [seção pré-requisitos](#prerequisites) para obter mais detalhes.
1. Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.

    > [!NOTE]
    > As restaurações de contêiner de Banco de Dados Individual (SDC) devem seguir estas [verificações](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Se aplicável, selecione **substituir se o banco de BD com o mesmo nome já existir na instância do Hana selecionada**.
1. Selecione **OK**.

    ![Restaurar configuração – tela final](media/sap-hana-db-restore/restore-configuration-last.png)

1. Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **substituir DB**  >  **OK**.

    ![Substituir BD](media/sap-hana-db-restore/overwrite-db.png)

1. Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos em vez de um banco de dados, escolha **restaurar como arquivos**. Depois que os arquivos são despejados em um caminho especificado, você pode pegar esses arquivos em qualquer computador SAP HANA em que queira restaurá-los como um banco de dados. Como você pode mover esses arquivos para qualquer computador, agora é possível restaurar os dados entre assinaturas e regiões.

1. No menu **configuração de restauração** , em **onde e como restaurar**, selecione **restaurar como arquivos**.
1. Selecione o nome do servidor do **host** /Hana para o qual você deseja restaurar os arquivos de backup.
1. No **caminho de destino no servidor**, insira o caminho da pasta no servidor selecionado na etapa 2. Esse é o local onde o serviço irá despejar todos os arquivos de backup necessários.

    Os arquivos que são despejados são:

    * Arquivos de backup de banco de dados
    * Arquivos de catálogo
    * Arquivos de metadados JSON (para cada arquivo de backup envolvido)

    Normalmente, um caminho de compartilhamento de rede (ou caminho de um compartilhamento de arquivo do Azure montado quando especificado como o caminho de destino) permite o acesso mais fácil a esses arquivos por outros computadores na mesma rede ou com o mesmo compartilhamento de arquivo do Azure montado neles.

    >[!NOTE]
    >Para restaurar os arquivos de backup de banco de dados em um compartilhamento de arquivo do Azure montado na VM registrada de destino, verifique se a conta raiz tem permissões de leitura/gravação no compartilhamento de arquivo do Azure.

    ![Escolher caminho de destino](media/sap-hana-db-restore/restore-as-files.png)

1. Selecione o **ponto de restauração** correspondente ao qual todos os arquivos e pastas de backup serão restaurados.

    ![Folha Selecionar ponto de restauração](media/sap-hana-db-restore/select-restore-point.png)

1. Todos os arquivos de backup associados ao ponto de restauração selecionado são despejados no caminho de destino.
1. Com base no tipo de ponto de restauração escolhido (**Ponto no tempo** ou **Completo e Diferencial**), você verá uma ou mais pastas criadas no caminho de destino. Uma das pastas chamada `Data_<date and time of restore>` contém os backups completo e diferencial e a outra pasta chamada `Log` contém os backups de log.
1. Mova esses arquivos restaurados para o servidor de SAP HANA no qual você deseja restaurá-los como um banco de dados.
1. Depois, execute estas etapas:
    1. Defina permissões na pasta/diretório em que os arquivos de backup são armazenados usando o seguinte comando:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Execute o próximo conjunto de comandos como `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Gere o arquivo de catálogo para restauração. Extraia o **BackupId** do arquivo de metadados JSON para o backup completo, que será usado posteriormente na operação de restauração. Verifique se os backups completo e de log estão em pastas diferentes e exclua os arquivos de catálogo e os arquivos de metadados JSON nessas pastas.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        No comando acima:

        * `<DataFileDir>` – a pasta que contém os backups completos
        * `<LogFilesDir>` – a pasta que contém os backups de log
        * `<PathToPlaceCatalogFile>` – a pasta na qual o arquivo de catálogo gerado deve ser colocado

    1. Restaure usando o arquivo de catálogo recém-gerado por meio do HANA Studio ou execute a consulta de restauração do HDBSQL com esse catálogo que acaba de ser gerado. As consultas HDBSQL estão listadas abaixo:

    * Para restaurar para um momento determinado:

        Se você estiver criando um banco de dados restaurado, execute o comando HDBSQL para criar um banco de dados `<DatabaseName>` e interrompa o banco de dados para restauração. No entanto, se você estiver restaurando apenas um banco de dados existente, execute o comando HDBSQL para interromper o banco de dados.

        Em seguida, execute o seguinte comando para restaurar o banco de dados:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – nome do novo banco de dados ou banco de dados existente que você deseja restaurar
        * `<Timestamp>` – carimbo de data/hora exato da restauração pontual
        * `<DatabaseName@HostName>` – nome do banco de dados cujo backup é usado para restauração e o nome do servidor de **host**/SAP HANA em que esse banco de dados reside. A opção `USING SOURCE <DatabaseName@HostName>` especifica que o backup de dados (usado para restauração) é de um banco de dados com um SID ou nome diferente do computador de SAP HANA de destino. Portanto, ele não precisa ser especificado para restaurações feitas no mesmo servidor HANA de onde o backup é feito.
        * `<PathToGeneratedCatalogInStep3>` -Caminho para o arquivo de catálogo gerado na **etapa C**
        * `<DataFileDir>` – a pasta que contém os backups completos
        * `<LogFilesDir>` – a pasta que contém os backups de log
        * `<BackupIdFromJsonFile>` - **BackupId** extraído na **etapa C**

    * Para restaurar para um backup completo ou diferencial específico:

        Se você estiver criando um banco de dados restaurado, execute o comando HDBSQL para criar um banco de dados `<DatabaseName>` e interrompa o banco de dados para restauração. No entanto, se você estiver restaurando apenas um banco de dados existente, execute o comando HDBSQL para interromper o banco de dados:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – o nome do novo banco de dados ou banco de dados existente que você deseja restaurar
        * `<Timestamp>` – o carimbo de data/hora exato da restauração pontual
        * `<DatabaseName@HostName>` – o nome do banco de dados cujo backup é usado para restauração e o nome do servidor de **host**/SAP HANA em que esse banco de dados reside. A opção `USING SOURCE <DatabaseName@HostName>` especifica que o backup de dados (usado para restauração) é de um banco de dados com um SID ou nome diferente do computador de SAP HANA de destino. Portanto, ele não precisa ser especificado para restaurações feitas no mesmo servidor HANA do qual o backup é feito.
        * `<PathToGeneratedCatalogInStep3>` -o caminho para o arquivo de catálogo gerado na **etapa C**
        * `<DataFileDir>` – a pasta que contém os backups completos
        * `<LogFilesDir>` – a pasta que contém os backups de log
        * `<BackupIdFromJsonFile>` - **BackupId** extraído na **etapa C**

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação no grafo de log e selecione **OK** para escolher o ponto de restauração.

    ![Ponto de restauração](media/sap-hana-db-restore/restore-point.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.

    ![Selecione restaurar](media/sap-hana-db-restore/restore-restore.png)

1. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

    ![Restauração disparada com êxito](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurar para um ponto de recuperação específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação na lista e selecione **OK** para escolher o ponto de restauração.

    ![Restaurar ponto de recuperação específico](media/sap-hana-db-restore/specific-recovery-point.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.

    ![Iniciar trabalho de restauração](media/sap-hana-db-restore/restore-specific.png)

1. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

    ![Progresso da restauração](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Em uma nova restauração de contêiner de banco de dados (MDC) depois que o BD do sistema é restaurado para uma instância de destino, é necessário executar o script de pré-registro novamente. Somente então as restaurações subsequentes do banco de bancos de locatário serão realizadas com sucesso. Para saber mais, consulte [solução de problemas – a restauração MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Restauração Entre Regiões

Como uma das opções de restauração, a CRR (restauração entre regiões) permite que você restaure SAP HANA bancos de dados hospedados em VMs do Azure em uma região secundária, que é uma região emparelhada do Azure.

Para carregar o recurso durante a versão prévia, leia a [seção antes de começar](./backup-create-rs-vault.md#set-cross-region-restore).

Para ver se a CRR está habilitada, siga as instruções em [Configurar a restauração entre regiões](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Exibir itens de backup na região secundária

Se a CRR estiver habilitada, você poderá exibir os itens de backup na região secundária.

1. No portal, vá para **cofre dos serviços de recuperação**  >  **itens de backup**.
1. Selecione **região secundária** para exibir os itens na região secundária.

>[!NOTE]
>Somente os tipos de gerenciamento de backup que dão suporte ao recurso de CRR serão mostrados na lista. Atualmente, é permitido apenas o suporte para a restauração de dados de região secundária em uma região secundária.

![Itens de backup na região secundária](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Bancos de dados na região secundária](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Restaurar na região secundária

A experiência do usuário de restauração da região secundária será semelhante à experiência do usuário de restauração da região primária. Ao configurar detalhes no painel de configuração de restauração para configurar a restauração, você será solicitado a fornecer somente os parâmetros de região secundária.

![Onde e como restaurar](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>A rede virtual na região secundária precisa ser atribuída exclusivamente e não pode ser usada para outras VMs nesse grupo de recursos.

![Notificação de disparo de restauração em andamento](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* Depois que a restauração é disparada e na fase de transferência de dados, o trabalho de restauração não pode ser cancelado.
>* As funções do Azure necessárias para restaurar na região secundária são as mesmas da região primária.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorando trabalhos de restauração de região secundária

1. No portal, acesse **cofre dos serviços de recuperação**  >  **trabalhos de backup**
1. Selecione **região secundária** para exibir os itens na região secundária.

    ![Trabalhos de backup filtrados](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como gerenciar o](sap-hana-db-manage.md) backup de bancos de dados SAP Hana usando o backup do Azure
