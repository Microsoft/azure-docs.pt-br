---
title: Fazer backup de bancos de dados do SQL Server em VMs do Azure | Microsoft Docs
description: Saiba como fazer backup de bancos de dados do SQL Server em VMs do Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: vijayts
ms.openlocfilehash: 422f4b6bf7f22cf7653ad75836c613e4c1ea01b9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704945"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SQL Server nas VMs do Azure

Bancos de dados do SQL Server são cargas de trabalho críticas que exigem um objetivo de baixa de ponto de recuperação (RPO) e a retenção de longo prazo. Você pode fazer backup de bancos de dados do SQL Server em execução em máquinas virtuais (VMs) usando [Backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados do SQL Server que está executando em uma VM do Azure para um cofre dos serviços de recuperação de Backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar os backups.
> * Configurar a proteção automática para bancos de dados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de você fazer backup de um banco de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie uma [cofre dos Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região ou a localidade que a VM que hospeda a instância do SQL Server.
2. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Certifique-se de que os bancos de dados do SQL Server execute o [diretrizes de nomenclatura de banco de dados para o Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Especificamente para o SQL 2008 e 2008 R2, [adicionar a chave do registro](#add-registry-key-to-enable-registration) para habilitar o registro do servidor. Essa etapa não ser será necessária quando o recurso está disponível.
5. Verifique se você não tem qualquer outras soluções de backup habilitadas para o banco de dados. Desabilite todos os outros backups do SQL Server antes de fazer backup do banco de dados.

> [!NOTE]
> Você pode habilitar o Backup do Azure para uma VM do Azure e também para um banco de dados do SQL Server em execução na máquina virtual sem conflito.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Todas as operações, uma VM do SQL Server exige conectividade com endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) falharem sem conectividade com endereços IP públicos do Azure.

Estabelece conectividade, usando uma das seguintes opções:

- **Permitir que os intervalos de IP do datacenter do Azure**. Essa opção permite [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para acessar um grupo de segurança de rede (NSG), use o cmdlet Set-AzureNetworkSecurityRule. Se você está protegido apenas específica da região IPs da lista de destinatários, você também precisará atualizar a lista de destinatários seguros a marca de serviço do Azure Active Directory (Azure AD) para habilitar a autenticação.

- **Permitir o acesso usando marcas NSG**. Se você usar NSGs para restringir a conectividade, essa opção adiciona uma regra para o NSG que permita o acesso de saída para o Backup do Azure usando a marca AzureBackup. Além dessa marca, você também precisará correspondente [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e o armazenamento do Azure para permitir a conectividade para autenticação e transferência de dados. A marca AzureBackup só está disponível atualmente no PowerShell. Para criar uma regra usando a marca AzureBackup:

    - Adicione as credenciais da conta do Azure e atualize as nuvens nacionais<br/>
    `Add-AzureRmAccount`

    - Selecione a assinatura de NSG<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Adicionar permitir regra de saída para a marca de serviço de Backup do Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Salve o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Permitir o acesso usando marcas de Firewall do Azure**. Se você estiver usando o Firewall do Azure, crie uma regra de aplicativo usando o AzureBackup [marca FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). Isso permite que o acesso de saída para o Backup do Azure.
- **Implantar um servidor proxy HTTP para rotear o tráfego**. Quando você faz backup de um banco de dados do SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs de HTTPS para enviar comandos de gerenciamento para o Backup do Azure e os dados no armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

Opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Não há custos adicionais | Complexo de gerenciar porque os intervalos de endereços IP mudam ao longo do tempo <br/><br/> Fornece acesso a todo do Azure, não apenas o armazenamento do Azure
Usar marcas de serviço do NSG | Mais fácil de gerenciar como alterações de intervalo são mescladas automaticamente <br/><br/> Não há custos adicionais <br/><br/> | Pode ser usado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Usar marcas de FQDN de Firewall do Azure | Mais fácil de gerenciar como os FQDNs necessários são automaticamente gerenciados | Pode ser usado apenas com o Firewall do Azure
Usar um proxy HTTP | Controle granular no proxy do armazenamento de URLs permitidas <br/><br/> Único ponto de acesso à internet para VMs <br/><br/> Não estão sujeitas a alterações de endereço IP do Azure | Custos adicionais para executar uma VM com o software de proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomenclatura para o Azure Backup de banco de dados

Evite usando os seguintes elementos em nomes de banco de dados:

  * À direita e espaços à esquerda
  * À direita de pontos de exclamação (!)
  * Colchetes fechamento (])
  * Ponto e vírgula ';'
  * Barra '/'

Alias está disponível para caracteres sem suporte, mas é recomendável evitá-los. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

### <a name="add-registry-key-to-enable-registration"></a>Adicionar a chave do registro para habilitar o registro

1. Abra Regedit
2. Crie o caminho do diretório de registro: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (você precisará criar o TestHook 'Key' sob WorkloadBackup que por sua vez, precisa ser criado em Microsoft).
3. Sob o caminho do diretório de registro, crie um novo 'valor de cadeia de caracteres' com o nome de cadeia de caracteres **AzureBackupEnableWin2K8R2SP1** e valor: **True**

    ![RegEdit para habilitar o registro](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

Como alternativa, você pode automatizar esta etapa, executando o arquivo. reg com o seguinte comando:

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

Como descobrir bancos de dados em execução em uma VM:

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação usado para fazer backup do banco de dados.

2. No **cofre dos serviços de recuperação** dashboard, selecione **Backup**.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **meta de Backup**, defina **onde sua carga de trabalho é executada?** para **Azure**.

4. Em **Do que você deseja fazer backup**, selecione **SQL Server em uma VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Meta de Backup** > **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar as VMs não protegidas na assinatura. Essa pesquisa pode demorar um pouco, dependendo do número de VMs desprotegidos na assinatura.

   - As VMs não protegidas devem ser exibidas na lista após a descoberta, listadas por nome e grupo de recursos.
   - Se uma VM não estiver listada, conforme o esperado, ver se ele já é feito em um cofre.
   - Várias VMs podem ter o mesmo nome, mas vai pertencem a diferentes grupos de recursos.

     ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM que executa o banco de dados do SQL Server > **Descobrir BDs**.

7. Descoberta de banco de dados de controle no **notificações**. O tempo necessário para esta ação depende do número de bancos de dados da VM. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Backup do Azure descobre todos os bancos de dados do SQL Server na VM. Durante a descoberta, os elementos a seguir ocorrem em segundo plano:

    - O Backup do Azure registra a VM com o Cofre de backup de carga de trabalho. Todos os bancos de dados na VM registrado podem ser feitos neste cofre somente.
    - O Backup do Azure instala a extensão de AzureBackupWindowsWorkload na VM. Nenhum agente é instalado em um banco de dados SQL.
    - O Backup do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      - Todas as operações de backup e restauração usam a conta de serviço.
      - NT Service\AzureWLBackupPluginSvc requer permissões de sysadmin do SQL. Todas as VMs do SQL Server criado no Marketplace são fornecidas com o SqlIaaSExtension instalado. A extensão de AzureBackupWindowsWorkload usa o SQLIaaSExtension para obter automaticamente as permissões necessárias.
    - Se você não criou a VM do Marketplace ou se você estiver usando o SQL 2008 e 2008 R2, a VM pode não ter o SqlIaaSExtension instalado e a operação de descoberta falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir esse problema, siga as instruções em [permissões de VM do conjunto](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar o backup  

1. Na **meta de Backup** > **etapa 2: Configurar o Backup**, selecione **Configurar Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Na **selecionar itens para backup**, você verá todos os grupos de disponibilidade registrado e instâncias do SQL Server autônomo. Selecione a seta à esquerda de uma linha para expandir a lista de todos os bancos de dados desprotegidos nessa instância ou grupo de disponibilidade Always On.  

    ![Exibindo todas as Instâncias do SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todos os bancos de dados que você deseja proteger e, em seguida, selecione **Okey**.

   ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Backup do Azure define um número máximo de bancos de dados em um trabalho de backup como 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Para habilitar [ ](#enable-auto-protection) toda a instância ou o grupo de disponibilidade Always On. No **AUTOPROTECT** lista suspensa, selecione **ON**e, em seguida, selecione **Okey**.

    > [!NOTE]
    > O [proteção automática](#enable-auto-protection) recurso não só permite a proteção em todos os bancos de dados existentes ao mesmo tempo, mas protege automaticamente quaisquer novos bancos de dados adicionados a essa instância ou o grupo de disponibilidade.  

4. Selecione **Okey** para abrir **política de Backup**.

    ![Habilitar a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de Backup**, escolha uma política e, em seguida, selecione **Okey**.

   - Selecione a política padrão como HourlyLogBackup.
   - Escolher uma política de backup existente criada anteriormente para SQL.
   - Definir uma nova política baseada no seu período de retenção e o RPO.

     ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **Backup**, selecione **habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área  **Notificações**  do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
- Quando você cria uma política de backup, um backup completo diário é o padrão.
- Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente.
- Saiba mais sobre [diferentes tipos de políticas de backup](backup-architecture.md#sql-server-backup-types).

Para criar uma política de backup:

1. No cofre, selecione **políticas de Backup** > **Add**.
2. Na **Add**, selecione **SQL Server na VM do Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **Nome da política**, insira um nome para a nova política.
4. Na **política de Backup completo**, selecione um **frequência de Backup**. Escolha **diária** ou **semanal**.

   - Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar.
   - Para **Semanal**, selecione o dia da semana, a hora e o fuso horário do início do trabalho de backup.
   - Executar um backup completo, pois não é possível desativar o **Backup completo** opção.
   - Selecione **Backup completo** para exibir a política.
   - Você não pode criar backups diferenciais para backups diários completos.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Na **período de retenção**, todas as opções são selecionadas por padrão. Limpe qualquer período de retenção limita o que você não deseja usar e, em seguida, definir os intervalos de usar.

    - Período de retenção mínimo para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    - Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    - O backup para um dia específico é marcado e mantido com base no período de retenção semanal e a configuração de retenção semanal.
    - Intervalos de retenção mensal e anual se comportam de maneira semelhante.

       ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Configurações do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu da política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.

    - Você pode disparar apenas um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por até 180 dias. Para a retenção mais longa, use backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

10. Para adicionar uma política de backup de log transacional, selecione **Backup de Log**.
11. Em **Backup de Log**, selecione **Habilitar** e, em seguida, defina os controles de retenção e frequência. Backups de log podem ocorrer tão frequentemente quanto a cada 15 minutos e podem ser retidos por até 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **Política de backup**, escolha se deseja habilitar a **Compactação de Backup SQL**.
    - A compactação está desabilitada por padrão.
    - No back-end, o Backup do Azure usa compactação de backup nativo do SQL.

14. Depois de concluir as edições à política de backup, selecione **OK**.


### <a name="modify-policy"></a>Modificar política
Modificar a política para alterar o intervalo de frequência ou retenção de backup.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospectively para todos os pontos de recuperação mais antigos além de novos.

No painel do cofre, acesse **Manage** > **políticas de Backup** e escolha a política que você deseja editar.

  ![Gerenciar política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Habilitar a proteção automática  

Você pode habilitar a proteção automática para backup automaticamente todos os bancos de dados existentes e futuros para uma instância do SQL Server autônomo ou para um grupo de disponibilidade Always On.

- Não há nenhum limite no número de bancos de dados que podem ser selecionados para proteção automática de uma só vez.
- Você não pode proteger ou excluir bancos de dados da proteção em uma instância no momento em que você habilitar a proteção automática seletivamente.
- Se sua instância já inclua alguns bancos de dados protegidos, eles serão permanecem protegidos em suas respectivas políticas mesmo depois que você ativar a proteção automática. Todos os bancos de dados desprotegidos adicionados posteriormente terá apenas uma única política que você definir no momento da habilitação da proteção automática, listada sob **configurar o Backup**. No entanto, você pode alterar a política associada a um banco de dados protegida automaticamente mais tarde.  

Para habilitar a proteção automática:

  1. Em **Itens para backup**, selecione a instância na qual deseja habilitar a proteção automática.
  2. Selecione a lista suspensa sob **AUTOPROTECT**, escolha **ON**e, em seguida, selecione **Okey**.

      ![Habilitar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. O backup é configurado para todos os bancos de dados juntos e pode ser acompanhado em **Trabalhos de Backup**.

Se você precisar desativar proteção automática, selecione o nome da instância sob **configurar o Backup**e, em seguida, selecione **desabilitar Autoprotect** para a instância. Todos os bancos de dados continuará a ser feito, mas bancos de dados futuros não estarão protegidos automaticamente.

![Desabilite a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Próximas etapas

Saiba como:

- [Restaurar bancos de dados do backup do SQL Server](restore-sql-database-azure-vm.md)
- [Gerenciar bancos de dados do backup do SQL Server](manage-monitor-sql-database-backup.md)
