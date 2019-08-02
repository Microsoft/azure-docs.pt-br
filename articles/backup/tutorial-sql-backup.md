---
title: Fazer backup de bancos de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica como fazer backup do SQL Server para o Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 99e1e8194594d204a5080287794362dbe028a1f5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688413"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SQL Server nas VMs do Azure



Este artigo mostra como fazer backup de um banco de dados do SQL Server em execução em uma VM do Azure em um cofre dos Serviços de Recuperação do Backup do Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configurar a proteção automática para bancos de dados.
> * Executar um backup ad hoc.


## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de seu banco de dados do SQL Server, verifique as condições a seguir:

1. Identifique ou [crie](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre dos Serviços de Recuperação na mesma região ou localidade da VM que hospeda a Instância do SQL Server.
2. [Verifique as permissões da VM](backup-azure-sql-database.md#set-vm-permissions) necessárias para fazer backup dos bancos de dados SQL.
3. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verifique se os bancos de dados do SQL Server estão nomeados de acordo com as [diretrizes de nomenclatura](#verify-database-naming-guidelines-for-azure-backup) do Backup do Azure.
5. Verifique se não há nenhuma outra solução de backup habilitada para o banco de dados. Desabilite todos os outros backups do SQL Server antes de configurar esse cenário. Você pode habilitar o Backup do Azure para uma VM do Azure, juntamente com o Backup do Azure para um banco de dados do SQL Server em execução na VM sem nenhum conflito.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual (VM) do SQL Server precisa ter conectividade com os endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configuração de backups, agendamento de backups, restauração de pontos de recuperação e assim por diante) falharão sem conectividade com os endereços IP públicos. Estabeleça a conectividade com uma destas opções:

- **Permitir os intervalos de IP do datacenter do Azure**: Permita os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para acessar um NSG (Grupo de Segurança de Rede), use o cmdlet **Set-AzureNetworkSecurityRule**.
- **Implantar um servidor proxy HTTP para rotear o tráfego**: Quando você faz backup de um banco de dados do SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o Backup do Azure e dados para o Armazenamento do Azure. A extensão de backup também usa o Azure AD (Azure Active Directory) para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

Cada opção traz vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custo adicional. | É complexa de ser gerenciada porque os intervalos de endereços IP mudam com o tempo. <br/><br/> Fornece acesso a todo o Azure, não somente ao Armazenamento do Azure.
Usar um proxy HTTP   | É permitido o controle granular no proxy em relação às URLs de armazenamento. <br/><br/> Único ponto de acesso à Internet para VMs. <br/><br/> Não está sujeito a alterações do endereço IP do Azure | Custos adicionais para executar uma VM com o software de proxy.

### <a name="set-vm-permissions"></a>Definir permissões da VM

O Backup do Azure realiza uma série de ações quando você configura o backup de um banco de dados do SQL Server:

- Adiciona a extensão **AzureBackupWindowsWorkload**.
- Para descobrir bancos de dados na máquina virtual, o Backup do Azure cria uma conta **NT SERVICE\AzureWLBackupPluginSvc**. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL.
- O Backup do Azure aproveita a conta **NT AUTHORITY\SYSTEM** para descoberta/consulta de banco de dados e, portanto, essa conta precisa ser um logon público no SQL.

Se você não criou a VM do SQL Server no Azure Marketplace, talvez você receba um erro **UserErrorSQLNoSysadminMembership**. Se isso ocorrer, [siga estas instruções](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificar as diretrizes de nomenclatura de banco de dados do Backup do Azure

Evite usar o seguinte em nomes de banco de dados:

  * Espaços à esquerda ou à direita
  * '!' à direita
  * Colchete de fechamento ‘]’
  * Nomes de bancos de dados que começam com “F:\”

Temos nomes alternativos para caracteres não compatíveis com a tabela do Azure, mas recomendamos evitá-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

Descubra os bancos de dados em execução na VM.

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação usado para fazer backup do banco de dados.

2. No painel **Cofre dos serviços de recuperação**, selecione **Backup**.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **Meta de Backup**, defina **Localização na qual a carga de trabalho está sendo executada** como **Azure** (o padrão).

4. Em **Do que você deseja fazer backup**, selecione **SQL Server em uma VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Meta de Backup** > **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar as VMs não protegidas na assinatura. Isso poderá levar alguns instantes, dependendo do número de máquinas virtuais não protegidas na assinatura.

   - As VMs não protegidas devem ser exibidas na lista após a descoberta, listadas por nome e grupo de recursos.
   - Se uma VM não estiver listada conforme o esperado, verifique se ela já foi copiada em backup em um cofre.
   - Várias VMs podem ter o mesmo nome, mas elas pertencerão a diferentes grupos de recursos.

     ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM que executa o banco de dados do SQL Server > **Descobrir BDs**.

7. Acompanhe a descoberta de banco de dados na área **Notificações**. Poderá levar alguns instantes para o trabalho ser concluído, dependendo de quantos bancos de dados existem na VM. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Backup do Azure descobre todos os bancos de dados do SQL Server na VM. Durante a descoberta, ocorre o seguinte em segundo plano:

    - O Backup do Azure registra a VM no cofre para backup da carga de trabalho. Todos os bancos de dados na VM registrada só podem ser copiados em backup nesse cofre.
    - O Backup do Azure instala a extensão **AzureBackupWindowsWorkload** na VM. Nenhum agente é instalado no Banco de Dados SQL.
    - O Backup do Azure cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      - Todas as operações de backup e restauração usam a conta de serviço.
      - **NT Service\AzureWLBackupPluginSvc** precisa de permissões de sysadmin do SQL. Todas as VMs do SQL Server criadas no Azure Marketplace são fornecidas com a **SqlIaaSExtension** instalada. A extensão **AzureBackupWindowsWorkload** usa a **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    - Se você não criou a VM por meio do marketplace, a VM não tem a **SqlIaaSExtension** instalada e a operação de descoberta falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as [instruções](backup-azure-sql-database.md#set-vm-permissions) para corrigir esse problema.

        ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar o backup  

Configure o backup da seguinte maneira:

1. Em **Meta de Backup**, selecione **Configurar Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Clique em **Configurar Backup**, e a folha **Selecionar itens para backup** será exibida. Ela lista todos os grupos de disponibilidade registrados e SQL Servers autônomos. Expanda a divisa à esquerda da linha para ver todos os bancos de dados desprotegidos nessa instância ou AG Always on.  

    ![Exibindo todas as Instâncias do SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todos os bancos de dados que deseja proteger > **OK**.

   ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Backup do Azure define um número máximo de bancos de dados em um trabalho de backup como 50.

    
     * Como alternativa, você pode habilitar a proteção automática no grupo instância inteira ou grupo de disponibilidade Always On selecionando a opção **ATIVAR** na lista suspensa correspondente na coluna **PROTEÇÃO AUTOMÁTICA**. O recurso de proteção automática não só permite a proteção em todos os bancos de dados existentes de uma só vez, mas protege automaticamente quaisquer novos bancos de dados que serão adicionados no futuro para essa instância ou o grupo de disponibilidade.  

4. Clique em **OK** para abrir a folha **Política de backup**.

    ![Habilite a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Em **Escolher política de backup**, selecione uma política e, em seguida, clique em **OK**.

   - Selecionar a política padrão: HourlyLogBackup.
   - Escolher uma política de backup existente criada anteriormente para SQL.
   - Definir uma nova política baseada no seu período de retenção e o RPO.

     ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. No menu **Backup**, selecione **Habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
- Quando você cria uma política de backup, um backup completo diário é o padrão.
- Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente.
- [Saiba mais sobre](backup-architecture.md#sql-server-backup-types) os diferentes tipos de políticas de backup.

Para criar uma política de backup:

1. No cofre, clique em **Políticas de backup** > **Adicionar**.
2. No menu **Adicionar**, clique em **SQL Server em uma VM do Azure**. Isso define o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **Nome da política**, insira um nome para a nova política.
4. Em **Política de Backup Completo**, selecione uma **Frequência de Backup** escolhendo **Diária** ou **Semanal**.

   - Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar.
   - É necessário executar um backup completo, já que não é possível desativar a opção **Backup Completo**.
   - Clique em **Backup Completo** para exibir a política.
   - Você não pode criar backups diferenciais para backups diários completos.
   - Para **Semanal**, selecione o dia da semana, a hora e o fuso horário do início do trabalho de backup.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Em **Período de Retenção**, por padrão, todas as opções estão selecionadas. Desmarque os limites de período de retenção que não deseja usar e defina os intervalos a serem usados.

    - O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de 7 dias.
    - Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    - O backup para um dia específico é marcado e mantido com base no intervalo de retenção semanal e sua configuração de retenção semanal.
    - Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

   ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Configurações do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu da política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.

    - No máximo, você pode acionar um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

10. Para adicionar uma política de backup de log transacional, selecione **Backup de Log**.
11. Em **Backup de Log**, selecione **Habilitar** e, em seguida, defina os controles de retenção e frequência. Os Backups de log podem ocorrer a cada 15 minutos e podem ser mantidos por 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **Política de backup**, escolha se deseja habilitar a **Compactação de Backup SQL**.
    - A compactação está desabilitada por padrão.
    - No back-end, o Backup do Azure usa compactação de backup nativo do SQL.

14. Depois de concluir as edições à política de backup, selecione **OK**.

## <a name="run-an-ad-hoc-backup"></a>Executar um backup ad hoc

1. No cofre dos Serviços de Recuperação, escolha os itens de Backup.
2. Clique em "SQL na VM do Azure".
3. Clique com botão direito do mouse em um banco de dados e escolha "Fazer Backup agora".
4. Escolha o tipo de Backup (Completo/Diferencial/Log/Copiar somente completo) e a compactação (Habilitar/Desabilitar)
5. Selecione OK para iniciar o backup.
6. Monitore o trabalho de backup indo até o cofre dos Serviços de Recuperação e escolhendo "Trabalhos de Backup".


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o portal do Azure para:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configurar a proteção automática para bancos de dados.
> * Executar um backup ad hoc.

Continue no próximo tutorial para restaurar uma máquina virtual do Azure de um disco.

> [!div class="nextstepaction"]
> [Restaurar bancos de dados do SQL Server em VMs do Azure](./restore-sql-database-azure-vm.md)
 

