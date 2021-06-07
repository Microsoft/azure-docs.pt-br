---
title: Fazer backup de várias VMs do SQL Server por meio do cofre
description: Neste artigo, saiba como fazer backup de bancos de dados SQL Server em máquinas virtuais do Azure com o backup do Azure do cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 798dc81012ad968c3ecc287717240513a08a1349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "105567197"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Fazer backup de várias VMs SQL Server do cofre dos serviços de recuperação

Os bancos de dados do SQL Server são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. Você pode fazer backup de bancos de dados do SQL Server em execução em VMs (máquinas virtuais) do Azure usando o [Backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados do SQL Server em execução em uma VM do Azure em um cofre dos Serviços de Recuperação do Backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configurar a proteção automática para bancos de dados.

>[!NOTE]
>**A exclusão temporária para o SQL Server na VM do Azure e a exclusão temporária para SAP HANA nas cargas de trabalho de VM do Azure** já estão disponíveis na versão prévia.<br>
>Para se inscrever na versão prévia, escreva para nós em AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de um banco de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um [cofre dos Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e assinatura da VM que hospeda a Instância do SQL Server.
1. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
1. Verifique se os bancos de dados do SQL Server seguem as [diretrizes de nomenclatura de banco de dados do Backup do Azure](#database-naming-guidelines-for-azure-backup).
1. Certifique-se de que o comprimento combinado do nome da VM SQL Server e o nome do grupo de recursos não exceda 84 caracteres para Azure Resource Manager VMs (ou 77 caracteres para VMs clássicas). Essa limitação é porque alguns caracteres são reservados pelo serviço.
1. Verifique que não haja nenhuma outra solução de backup habilitada para o banco de dados. Desabilite todos os outros backups do SQL Server antes de você fazer backup do banco de dados.
1. Ao usar SQL Server 2008 R2 ou SQL Server 2012, você pode encontrar o problema de fuso horário para o backup, conforme descrito [aqui](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of). Verifique se você está nas atualizações cumulativas mais recentes para evitar o problema relacionado ao fuso horário descrito acima. Se a aplicação das atualizações na instância de SQL Server na VM do Azure não for viável, desabilite o horário de verão para o fuso horário na máquina virtual.

> [!NOTE]
> Você pode habilitar o Backup do Azure para uma VM do Azure e também para um banco de dados do SQL Server em execução na VM sem nenhum conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, uma VM do SQL Server requer conectividade com o serviço de Backup do Azure, o Armazenamento do Azure e o Azure Active Directory. Isso pode ser feito usando pontos de extremidade privados ou concedendo acesso aos endereços IP públicos ou FQDNs necessários. Não permitir a conectividade adequada com os serviços do Azure necessários pode levar a uma falha em operações como descoberta de banco de dados, configuração de backup, realização de backups e restauração de dados.

A seguinte tabela lista as várias alternativas que você pode usar para estabelecer a conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos de extremidade privados                 | Permitir backups por IPs privados dentro da rede virtual  <br><br>   Fornecer controle granular na rede e no lado do cofre | Incorre em [custos](https://azure.microsoft.com/pricing/details/private-link/) de ponto de extremidade privado padrão |
| Marcas de serviço do NSG                  | Mais fácil de ser gerenciada, pois as alterações de intervalo são mescladas automaticamente   <br><br>   Sem custos adicionais | Pode ser usada somente com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Marcas de FQDN do Firewall do Azure          | Mais fácil de ser gerenciada, pois os FQDNs necessários são gerenciados automaticamente | Pode ser usada somente com o Firewall do Azure                         |
| Permitir o acesso a FQDNs/IPs de serviço | Sem custos adicionais   <br><br>  Funciona com todos os dispositivos e firewalls de segurança de rede | Pode ser solicitado acesso a um amplo conjunto de IPs ou FQDNs   |
| Usar um proxy HTTP                 | Único ponto de acesso à Internet para VMs                       | Custos adicionais para execução de uma VM com o software de proxy         |

Mais detalhes sobre como usar essas opções são compartilhados abaixo:

#### <a name="private-endpoints"></a>Pontos de extremidade privados

Os pontos de extremidade privados permitem que você se conecte com segurança de servidores dentro de uma rede virtual ao seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](./private-endpoints.md).

#### <a name="nsg-tags"></a>Marcas do NSG

Se você usar o NSG (grupo de segurança de rede), use a tag de serviço *AzureBackup* para permitir o acesso de saída ao Backup do Azure. Além da marca do Backup do Azure, você também precisará permitir a conectividade para autenticação e transferência de dados criando [regras de NSG](../virtual-network/network-security-groups-overview.md#service-tags) semelhantes para o Azure AD (*AzureActiveDirectory*) e o Armazenamento do Azure (*Armazenamento*).  As seguintes etapas descrevem o processo para criar uma regra para a tag do Backup do Azure:

1. Em **Todos os Serviços**, acesse **Grupos de segurança de rede** e selecione o grupo de segurança de rede.

1. Selecione **Regras de segurança de saída** em **Configurações**.

1. Selecione **Adicionar**. Insira todos os detalhes necessários para criar uma regra, conforme descrito em [Configurações da regra de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Verifique se a opção **Destino** está definida como *Tag de Serviço* e se **Marca de serviço de destino** está definida como *AzureBackup*.

1. Selecione **Adicionar** para salvar a regra de segurança de saída recém-criada.

De maneira semelhante, é possível criar regras de segurança de saída de NSG para o Armazenamento do Azure e o Azure AD.

#### <a name="azure-firewall-tags"></a>Marcas do Firewall do Azure

Se estiver usando o Firewall do Azure, crie uma regra de aplicativo usando a [tag de FQDN do Firewall do Azure](../firewall/fqdn-tags.md) *AzureBackup*. Isso permite todo o acesso de saída ao Backup do Azure.

#### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso aos intervalos de IP de serviço

Se você optar por permitir o acesso a IPs de serviço, confira os intervalos de IP no arquivo JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Será necessário permitir o acesso a IPs correspondentes ao Backup do Azure, ao Armazenamento do Azure e ao Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso a FQDNs de serviço

Também é possível usar os seguintes FQDNs para permitir o acesso aos serviços necessários de seus servidores:

| Serviço    | Nomes de domínio a serem acessados                             | Portas
| -------------- | ------------------------------------------------------------ | ---
| Backup do Azure  | `*.backup.windowsazure.com`                             | 443
| Armazenamento do Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | Permitir acesso a FQDNs nas seções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) | Conforme aplicável

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usar um servidor proxy HTTP para rotear o tráfego

Quando você faz backup de um banco de dados do SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o Backup do Azure e dados para o Armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. Use a lista de IPs e FQDNs mencionados acima para permitir o acesso aos serviços necessários. Não há suporte para os servidores proxy autenticados.

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomenclatura de banco de dados para o Backup do Azure

Evite usar os seguintes elementos em nomes de banco de dados:

* Espaços à esquerda ou à direita
* Pontos de exclamação (!) à direita
* Colchetes de fechamento (])
* Ponto e vírgula ';'
* Barra '/'

O antialiasing está disponível para caracteres não compatíveis com a tabela do Azure, mas recomendamos evitá-los. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](/rest/api/storageservices/understanding-the-table-service-data-model).

>[!NOTE]
>A operação de **configuração de proteção** para bancos de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Você pode alterar o nome do banco de dados ou habilitar a **Proteção Automática**, que deverá proteger com êxito esses bancos de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

Como descobrir os bancos de dados em execução em uma VM:

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação usado para fazer backup do banco de dados.

2. No painel **Cofre dos Serviços de Recuperação**, selecione **Backup**.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. No menu **Meta de Backup**, defina **Localização na qual a carga de trabalho está sendo executada?** como **Azure**.

4. Em **Do que você deseja fazer backup**, selecione **SQL Server em uma VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Meta de Backup** > **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar as VMs não protegidas na assinatura. Essa pesquisa poderá levar alguns instantes, dependendo do número de máquinas virtuais não protegidas na assinatura.

   * As VMs não protegidas devem ser exibidas na lista após a descoberta, listadas por nome e grupo de recursos.
   * Se uma VM não estiver listada conforme o esperado, veja se ela já foi copiada em backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas elas pertencerão a diferentes grupos de recursos.

     ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM que executa o banco de dados do SQL Server > **Descobrir BDs**.

7. Acompanhe a descoberta de banco de dados em **Notificações**. O tempo necessário para essa ação depende do número de bancos de dados de VM. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Backup do Azure descobre todos os bancos de dados do SQL Server na VM. Durante a descoberta, ocorrem os seguintes eventos em segundo plano:

    * O Backup do Azure registra a VM no cofre para backup da carga de trabalho. O backup de todos os bancos de dados na VM registrada pode ser realizado apenas para esse cofre.
    * O Backup do Azure instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente é instalado em um Banco de Dados SQL.
    * O Backup do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      * Todas as operações de backup e restauração usam a conta de serviço.
      * NT Service\AzureWLBackupPluginSvc precisa de permissões de sysadmin do SQL. Todas as VMs do SQL Server criadas no Marketplace são fornecidas com a SqlIaaSExtension instalada. A extensão AzureBackupWindowsWorkload usa a SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se você não criou a VM por meio do Marketplace ou se você está em um SQL 2008/2008 R2, a VM provavelmente não tem a SqlIaaSExtension instalada e a operação de descoberta falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir esse problema, siga as instruções em [Definir permissões de VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar o backup  

1. Em **Meta de Backup** > **Etapa 2: Configurar Backup**, selecione **Configurar Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Selecione **Adicionar recursos** para ver todos os grupos de disponibilidade registrados e instâncias de SQL Server autônomas.

    ![Selecione Adicionar recursos](./media/backup-azure-sql-database/add-resources.png)

1. Na tela **selecionar itens para backup** , selecione a seta à esquerda de uma linha para expandir a lista de todos os bancos de dados desprotegidos nessa instância ou Always on grupo de disponibilidade.

    ![Selecionar itens para backup](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Selecione todos os bancos de dados que deseja proteger e selecione **OK**.

   ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Backup do Azure define um número máximo de bancos de dados em um trabalho de backup como 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Para [habilitar](#enable-auto-protection) a instância inteira ou o grupo de disponibilidade Always On, na lista suspensa **PROTEÇÃO AUTOMÁTICA**, selecione **ATIVADA** e, em seguida, selecione **OK**.

         > [!NOTE]
         > O recurso de [proteção automática](#enable-auto-protection) não só permite a proteção em todos os bancos de dados existentes de uma só vez, mas protege automaticamente quaisquer novos bancos de dados adicionados a essa instância ou ao grupo de disponibilidade.  

1. Defina a **política de backup**. Você tem as seguintes opções:

   * Selecione a política padrão como *HourlyLogBackup*.
   * Escolher uma política de backup existente criada anteriormente para SQL.
   * Definir uma nova política baseada no seu período de retenção e o RPO.

     ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

1. Selecione **habilitar backup** para enviar a operação **Configurar proteção** e acompanhar o progresso da configuração na área **notificações** do Portal.

   ![Acompanhar o progresso da configuração](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
* Quando você cria uma política de backup, um backup completo diário é o padrão.
* Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente.
* Saiba mais sobre os [diferentes tipos de políticas de backup](backup-architecture.md#sql-server-backup-types).

Para criar uma política de backup:

1. No cofre, selecione **Políticas de backup** > **Adicionar**.
1. No menu **Adicionar**, selecione **SQL Server em uma VM do Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

1. Em **Nome da política**, insira um nome para a nova política.

    ![Inserir o nome da política](./media/backup-azure-sql-database/policy-name.png)

1. Selecione o link **Editar** correspondente, para **backup completo**, para modificar as configurações padrão.

   * Selecione uma **frequência de backup**. Escolha uma opção entre **Diária** ou **Semanal**.
   * Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar. Você não pode criar backups diferenciais para backups diários completos.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

1. Em **PERÍODO DE RETENÇÃO**, por padrão, todas as opções estão selecionadas. Desmarque os limites de período de retenção que não deseja usar e defina os intervalos a serem usados.

    * O período de retenção mínimo para qualquer tipo de backup (completo, diferencial e de log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    * O backup para um dia específico é marcado e mantido com base no período de retenção semanal e a configuração de retenção semanal.
    * Os períodos de retenção mensal e anual comportam-se de maneira semelhante.

       ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

1. Selecione **OK** para aceitar a configuração de backups completos.
1. Selecione o link **Editar** correspondente ao **backup diferencial** para modificar as configurações padrão.

    * Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    * Você pode acionar apenas um backup diferencial por dia. Um backup diferencial não pode ser disparado no mesmo dia que um backup completo.
    * Backups diferenciais podem ser retidos por até 180 dias.
    * O backup diferencial não tem suporte para o banco de dados mestre.

      ![Política de backup diferencial](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Selecione o link **Editar** correspondente ao **backup de log** para modificar as configurações padrão

    * Em **Backup de Log**, selecione **Habilitar** e, em seguida, defina os controles de retenção e frequência.
    * Os Backups de log podem ocorrer a cada 15 minutos e podem ser mantidos por 35 dias.
    * Se o banco de dados estiver no [modelo de recuperação simples](/sql/relational-databases/backup-restore/recovery-models-sql-server), o agendamento de backup de log para esse banco de dados será pausado e, portanto, nenhum backup de log será disparado.
    * Se o modelo de recuperação do banco de dados for alterado de **completo** para **simples**, os backups de log serão pausados dentro de 24 horas da alteração no modelo de recuperação. Da mesma forma, se o modelo de recuperação for alterado de **simples**, implicando backups de log que agora podem ser suportados para o banco de dados, os agendamentos de backups de log serão habilitados dentro de 24 horas da alteração no modelo de recuperação.

      ![Política de backup de log](./media/backup-azure-sql-database/log-backup-policy.png)

1. No menu **Política de backup**, escolha se deseja ou não habilitar a **Compactação de Backup SQL**. Essa opção está desabilitada por padrão. Se habilitada, o SQL Server enviará um fluxo de backup compactado para o VDI. O backup do Azure substitui os padrões de nível de instância pela cláusula COMPRESSION/NO_COMPRESSION dependendo do valor desse controle.

1. Depois de concluir as edições à política de backup, selecione **OK**.

> [!NOTE]
> Cada backup de log é encadeado ao backup completo anterior para formar uma cadeia de recuperação. Esse backup completo será retido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é mantido por um período extra para garantir que todos os logs possam ser recuperados. Vamos supor que você tenha um backup completo semanal, logs diferenciais diários e 2 horas. Todos eles são retidos por 30 dias. Porém, a semana completa pode ser realmente limpa/excluída somente depois que o próximo backup completo estiver disponível, ou seja, após 30 a 7 dias. Por exemplo, um backup completo semanal ocorre em 16 de novembro. De acordo com a política de retenção, ela deve ser retida até 16 de dezembro. O último backup de log para esse completo ocorre antes do próximo completo agendado, em 22 de novembro. Até que esse log esteja disponível até 22 de dezembro, o completo de 16 de novembro não poderá ser excluído. Portanto, o completo de 16 de novembro é mantido até 22 de dezembro.

## <a name="enable-auto-protection"></a>Habilitar a proteção automática  

É possível habilitar a proteção automática para fazer backup automaticamente de todos os bancos de dados existentes e dos bancos de dados que serão adicionados no futuro a uma instância autônoma do SQL Server ou a um grupo de disponibilidade Always On.

* Não há limite para o número de bancos de dados que você pode selecionar para proteção automática de cada vez. A descoberta normalmente é executada a cada oito horas. No entanto, você pode descobrir e proteger novos bancos de dados imediatamente se executar uma descoberta manualmente selecionando a opção **redescobrir bancos** de dados.
* Você não pode proteger bancos de dados seletivamente nem excluí-los da proteção em uma instância no momento em que habilita a proteção automática.
* Se sua instância já inclui alguns bancos de dados protegidos, eles permanecerão protegidos sob as respectivas políticas mesmo depois de você ativar a proteção automática. Todos os bancos de dados desprotegidos adicionados posteriormente terão apenas uma política que você define no momento da habilitação da proteção automática, listada em **Configurar Backup**. No entanto, você pode alterar posteriormente a política associada a um banco de dados protegido automaticamente.  

Para habilitar a proteção automática:

  1. Em **Itens para backup**, selecione a instância na qual deseja habilitar a proteção automática.
  2. Selecione a lista suspensa em **PROTEÇÃO AUTOMÁTICA**, escolha **ATIVADA** e, em seguida, selecione **OK**.

      ![Habilitar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. O backup é configurado para todos os bancos de dados juntos e pode ser acompanhado em **Trabalhos de Backup**.

Caso precise desabilitar a proteção automática, selecione o nome da instância em **Configurar Backup** e selecione **Desabilitar Proteção Automática** para a instância. Todos os bancos de dados continuarão a ser submetidos a backup, mas os bancos de dados futuros não serão protegidos automaticamente.

![Desabilitar a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como:

* [Restaurar backups de bancos de dados do SQL Server](restore-sql-database-azure-vm.md)
* [Gerenciar backups de bancos de dados do SQL Server](manage-monitor-sql-database-backup.md)
