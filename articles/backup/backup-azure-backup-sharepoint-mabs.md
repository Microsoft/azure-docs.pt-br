---
title: Usar o servidor de Backup do Azure para fazer backup de um farm do SharePoint no Azure
description: Use o Servidor de Backup do Azure para fazer backup e restaurar seus dados do SharePoint. Este artigo fornece informações para configurar seu farm do SharePoint para que os dados desejados possam ser armazenados no Azure. Você pode restaurar dados protegidos do SharePoint do disco ou do Azure.
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: kasinh
ms.openlocfilehash: cc7a5f6703d8d6fcec800071e75b7ca42c8f1cef
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466695"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Fazer backup do farm do SharePoint para o Azure
Faça backup de um farm do SharePoint para o Microsoft Azure usando o MABS (Servidor de Backup do Microsoft Azure) da mesma maneira que você faz backup de outras fontes de dados. O Backup do Azure fornece flexibilidade no agendamento de backup para criar pontos de backup diariamente, semanalmente, mensalmente ou anualmente e fornece opções de política de retenção para diversos pontos de backup. Ele também fornece a capacidade de armazenar cópias de disco locais para obter RTOs (Objetivos de Tempo de Recuperação) rápidos e armazenar cópias no Azure para uma retenção econômica e de longo prazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões do SharePoint com suporte e cenários de proteção relacionados
O Backup do Azure para DPM dá suporte aos seguintes cenários:

| Carga de Trabalho | Versão | Implantação do SharePoint | Proteção e recuperação |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint implantado como um servidor físico ou em uma máquina virtual Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn | Proteger opções de recuperação do SharePoint Farm: Farm de recuperação, banco de dados e arquivo ou item de lista de pontos de recuperação de disco.  Recuperação do farm e do banco de dados dos pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de iniciar
Há alguns elementos que você precisa confirmar antes de fazer o backup de um farm do SharePoint para o Azure.

### <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de ter [instalado e preparado o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) para proteger as cargas de trabalho.

### <a name="protection-agent"></a>Agente de proteção
O agente de Backup do Azure deve ser instalado no servidor que executa o SharePoint, os servidores que executam o SQL Server e todos os outros servidores que fazem parte do farm do SharePoint. Para saber m ais sobre como configurar o agente de proteção, confira [Configurar o Agente de Proteção](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  A única exceção é que você instala o agente apenas em um único servidor WFE (Front-end da Web). O Servidor de Backup do Azure precisa do agente em um servidor WFE apenas para atuar como o ponto de entrada para proteção.

### <a name="sharepoint-farm"></a>Farm do SharePoint
Para cada 10 milhões de itens no farm, deve haver pelo menos 2 GB de espaço no volume em que a pasta do MABS está localizada. Esse espaço é necessário para a geração de catálogo. Para o MABS recuperar itens específicos (coleções de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogo cria uma lista das URLs contidas em cada banco de dados de conteúdo. Você pode exibir a lista de URLs no painel de item recuperável na área de tarefa **Recuperação** do Console do Administrador do MABS.

### <a name="sql-server"></a>SQL Server
O Servidor de Backup do Azure é executado como uma conta LocalSystem. Para fazer backup de bancos de dados do SQL Server, o MABS precisa de privilégios sysadmin nessa conta para o servidor que executa o SQL Server. Defina NT AUTHORITY\SYSTEM como *sysadmin* no servidor que executa o SQL Server antes de fazer backup dele.

Se o farm do SharePoint tiver bancos de dados do SQL Server configurados com aliases do SQL Server, instale os componentes de cliente do SQL Server no servidor Web front-end que o MABS protegerá.

### <a name="sharepoint-server"></a>SharePoint Server
Embora o desempenho dependa de muitos fatores tais como o tamanho do farm do SharePoint, as diretrizes gerais são de que um MABS pode ser usado para proteger um farm do SharePoint de 25 TB.

### <a name="whats-not-supported"></a>O que não tem suporte
* O MABS que protege um farm do SharePoint não protege os índices de pesquisa ou os bancos de dados do serviço de aplicativo. Você precisará configurar a proteção desses bancos de dados separadamente.
* O MABS não fornece backup dos bancos de dados do SQL Server do SharePoint hospedados em compartilhamento SOFS (Servidor de Arquivos de Escalabilidade Horizontal).

## <a name="configure-sharepoint-protection"></a>Configurar a proteção do SharePoint
Para poder usar o MABS para proteger o SharePoint, você deve configurar o serviço de Gravador VSS do SharePoint (serviço de Gravador WSS) usando o **ConfigureSharePoint.exe**.

Você pode encontrar o **ConfigureSharePoint.exe** na pasta [Caminho de instalação do MABS]\bin no servidor Web de front-end. Essa ferramenta fornece o agente de proteção com as credenciais do farm do SharePoint. Você a executa em um único servidor WFE. Se você tiver vários servidores WFE, selecione apenas um ao configurar um grupo de proteção.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar o serviço de gravador VSS do SharePoint
1. No servidor WFE, em um prompt de comando, vá para [Local de instalação do MABS]\bin\
2. Insira ConfigureSharePoint -EnableSharePointProtection.
3. Insira as credenciais de administrador do farm. Essa conta deve ser um membro do grupo de administradores local no servidor WFE. Se o administrador do farm não for um administrador local, conceda as seguintes permissões no servidor WFE:
   * Conceda o controle total do grupo WSS_Admin_WPG para a pasta do DPM (%Arquivos de Programas%\Microsoft Azure Backup\DPM).
   * Conceda acesso de leitura do grupo WSS_Admin_WPG à chave do Registro do DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Você precisará executar novamente o ConfigureSharePoint.exe sempre que houver uma alteração nas credenciais de administrador de farm do SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Fazer backup de um farm do SharePoint usando o MABS
Depois de configurar o MABS e o farm do SharePoint conforme explicado anteriormente, o SharePoint pode ser protegido pelo MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger um farm do SharePoint
1. Na guia **Proteção** do Console do Administrador do MABS clique em **Novo**.
    ![Guia Nova Proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na página **Selecionar Tipo do Grupo de Proteção** do assistente **Criar Novo Grupo de Proteção**, selecione **Servidores** e clique em **Avançar**.

    ![Selecionar o tipo de Grupo de Proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na tela **Selecionar Membros do Grupo**, marque a caixa de seleção do servidor do SharePoint que deseja proteger e clique em **Avançar**.

    ![Selecionar membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Com o agente de proteção instalado, você pode ver o servidor no assistente. O MABS também mostra sua estrutura. Como você executou o ConfigureSharePoint.exe, o MABS se comunica com o serviço do Gravador VSS do SharePoint e seus bancos de dados do SQL Server correspondentes e reconhece a estrutura de farm do SharePoint, os bancos de dados de conteúdo associados e todos os itens correspondentes.
   >
   >
4. Na página **Selecionar Método de Proteção de Dados**, insira o nome do **Grupo de Proteção** e selecione seus *métodos de proteção* preferenciais. Clique em **Avançar**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > O método de proteção de disco ajuda a atender os objetivos de tempo de recuperação breves.
   >
   >
5. Na tela **Especificar Objetivos de Curto Prazo**, selecione seu **Período de retenção** preferencial e identifique quando você deseja que os backups ocorram.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Como a recuperação é necessária mais frequentemente para dados com menos de cinco dias, selecionamos um período de retenção de cinco dias no disco e garantimos que o backup ocorra fora dos horários de produção para esse exemplo.
   >
   >
6. Examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção e clique em **Avançar**.
7. Para cada grupo de proteção, o MABS aloca espaço em disco para armazenar e gerenciar réplicas. Neste ponto, o MABS deve criar uma cópia dos dados selecionados. Selecione como e quando você deseja que a réplica seja criada e clique em **Avançar**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Para garantir que o tráfego de rede não seja afetado, selecione um horário fora do horário de produção.
   >
   >
8. O MABS assegura a integridade de dados executando verificações de consistência na réplica. Há duas opções disponíveis. Você pode definir um agendamento para executar verificações de consistência ou o DPM pode executar as verificações de consistência automaticamente na réplica sempre que ela ficar inconsistente. Selecione sua opção desejada e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na página **Especificar Dados da Proteção Online**, selecione o farm do SharePoint que você deseja proteger e clique em **Avançar**.

    ![Proteção do SharePoint do DPM1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na página **Especificar o Agendamento do Backup Online**, selecione seu agendamento desejado e clique em **Avançar**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > O MABS fornece um máximo de dois backups diários para o Azure do ponto de backup de disco mais recente disponível nesses momentos. O Backup do Azure também pode controlar o volume de largura de banda WAN que pode ser usada para backups em horário de pico e fora dos horários de pico usando a [Limitação de Rede do Backup do Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Dependendo do agendamento de backup selecionado, na página **Especificar Política de Retenção Online** , selecione a política de retenção para pontos de backup diários, semanais, mensais e anuais.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > O MABS usa um esquema de retenção de avô-pai-filho em que uma política de retenção diferente pode ser escolhida para pontos de backup distintos.
    >
    >
12. Semelhante ao disco, uma réplica do ponto de referência inicial precisa ser criado no Azure. Selecione sua opção desejada para criar uma cópia de backup inicial no Azure e clique em **Avançar**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Examine as configurações selecionadas na página **Resumo** e clique em **Criar Grupo**. Você verá uma mensagem de êxito depois do grupo de proteção ter sido criado.

    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item do SharePoint do disco usando o MABS
No exemplo a seguir, o *item Recuperando SharePoint* foi excluído acidentalmente e precisa ser recuperado.
![Proteção do SharePoint do MABS 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **Console do Administrador do DPM**. Todos os farms do SharePoint protegidos pelo DPM são mostrados na guia **Proteção** .

    ![Proteção do SharePoint do MABS 3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione a guia **Recuperação** .

    ![Proteção do SharePoint do MABS 5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. É possível pesquisar no SharePoint para localizar o *item Recuperando SharePoint* usando uma pesquisa baseada em curinga dentro de um intervalo de ponto de recuperação.

    ![Proteção do SharePoint do MABS 6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado dentre os resultados da pesquisa, clique com o botão direito do mouse no item e selecione **Recuperar**.
5. Você também pode navegar pelos diversos pontos de recuperação e selecionar um banco de dados ou item para recuperar. Selecione **Data > Hora da recuperação** e escolha o **Banco de Dados > Farm do SharePoint > Ponto de recuperação > Item** correto.

    ![Proteção do SharePoint do MABS 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique com o botão direito do mouse no item e selecione **Recuperar** para abrir o **Assistente de Recuperação**. Clique em **Avançar**.

    ![Rever Seleção de Recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que você deseja executar e clique em **Avançar**.

    ![Tipo de Recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **Recuperar para o original** no exemplo recupera o item para o site do SharePoint original.
   >
   >
8. Selecione o **Processo de Recuperação** que você deseja usar.

   * Selecione **Recuperar sem usar um farm de recuperação** se o farm do SharePoint não tiver sido alterado e for o mesmo que o ponto de recuperação que está sendo restaurado.
   * Selecione **Recuperar usando um farm de recuperação** se o farm do SharePoint tiver sido alterado desde que o ponto de recuperação foi criado.

     ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça um local de preparo da instância do SQL Server para recuperar o banco de dados temporariamente e fornecer um compartilhamento de arquivos de preparo no MABS e no servidor que executa o SharePoint a fim de recuperar o item.

    ![Local de Preparo1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    O MABS anexa o banco de dados de conteúdo que está hospedando o item do SharePoint à instância temporária do SQL Server. Do banco de dados de conteúdo, ele recupera o item e o coloca no local do arquivo de preparo no MABS. O item recuperado no local de preparo agora precisa ser exportado para o local de preparo no farm do SharePoint.

    ![Local de Preparo2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecione **Especificar opções de recuperação**e aplique as configurações de segurança ao farm do SharePoint ou aplique as configurações de segurança do ponto de recuperação. Clique em **Avançar**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Você pode optar por limitar o uso de largura de banda da rede. Isso minimiza o impacto no servidor de produção durante o horário de produção.
    >
    >
11. Examine as informações de resumo e clique em **Recuperar** para iniciar a recuperação do arquivo.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Agora selecione a guia **Monitoramento** no **Console do Administrador do MABS** para exibir o **Status** da recuperação.

    ![Status da Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Agora o arquivo está restaurado. Você pode atualizar o site do SharePoint para verificar o arquivo restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar um banco de dados do SharePoint do Azure usando o DPM
1. Para recuperar um banco de dados de conteúdo do SharePoint, navegue pelos vários pontos de recuperação (conforme mostrado acima) e selecione o ponto de recuperação que você deseja recuperar.

    ![Proteção do SharePoint do MABS 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Como o farm do SharePoint está protegido para retenção de longo prazo no Azure, não há nenhuma informação de catálogo (metadados) disponível no MABS. Desta forma, sempre que um banco de dados de conteúdo do SharePoint pontual precisar ser recuperado, será necessário recatalogar o farm do SharePoint.
   >
   >
3. Clique em **Recatalogar**.

    ![Proteção do SharePoint do MABS 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de status **Recatalogação de Nuvem** é exibida.

    ![Proteção do SharePoint do MABS 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação ser concluída, o status é alterado para *Êxito*. Clique em **Fechar**.

    ![Proteção do SharePoint do MABS 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto do SharePoint mostrado na guia **Recuperação** do MABS para obter a estrutura do banco de dados de conteúdo. Clique com o botão direito do mouse no item apropriado e em **Recuperar**.

    ![Proteção do SharePoint do MABS 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nesse ponto, siga as etapas de recuperação descritas anteriormente neste artigo para recuperar um banco de dados de conteúdo do SharePoint do disco.

## <a name="next-steps"></a>Próximas etapas

Veja o artigo [Back up Exchange server](backup-azure-exchange-mabs.md) (Fazer backup do Exchange Server).
Veja o artigo [Back up SQL Server](backup-azure-sql-mabs.md) (Fazer backup do SQL Server).
