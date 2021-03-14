---
title: Fazer backup de um banco de dados SAP HANA no Azure com o Backup do Azure
description: Neste artigo, saiba como fazer backup de um banco de dados SAP HANA em máquinas virtuais do Azure com o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719603"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SAP HANA em VMs do Azure

Os bancos de dados SAP HANA são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. Você pode fazer backup de bancos de dados SAP HANA em execução em VMs (máquinas virtuais) do Azure usando o [Backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de bancos de dados SAP HANA em execução em VMs do Azure em um cofre dos Serviços de Recuperação do Backup do Azure.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups
> * Executar um trabalho de backup sob demanda

>[!NOTE]
>Desde 1º de agosto de 2020, o backup do SAP HANA para RHEL (7.4, 7.6, 7.7 e 8.1) está em disponibilidade geral.

>[!NOTE]
>**A exclusão temporária para o SQL Server na VM do Azure e a exclusão temporária para SAP HANA nas cargas de trabalho de VM do Azure** já estão disponíveis na versão prévia.<br>
>Para se inscrever na versão prévia, escreva para nós em [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Pré-requisitos

Veja as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [O que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) para configurar o banco de dados de backup.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, um banco de dados SAP HANA em execução em uma VM do Azure requer conectividade com o serviço de Backup do Azure, o Armazenamento do Azure e o Azure Active Directory. Isso pode ser feito usando pontos de extremidade privados ou concedendo acesso aos endereços IP públicos ou FQDNs necessários. Não permitir a conectividade adequada com os serviços do Azure necessários pode levar a uma falha em operações como descoberta de banco de dados, configuração de backup, realização de backups e restauração de dados.

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

De maneira semelhante, é possível criar regras de segurança de saída de NSG para o Armazenamento do Azure e o Azure AD. Para obter mais informações sobre as marcas de serviço, confira este [artigo](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Marcas do Firewall do Azure

Se estiver usando o Firewall do Azure, crie uma regra de aplicativo usando a [tag de FQDN do Firewall do Azure](../firewall/fqdn-tags.md) *AzureBackup*. Isso permite todo o acesso de saída ao Backup do Azure.

#### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso aos intervalos de IP de serviço

Se você optar por permitir o acesso a IPs de serviço, confira os intervalos de IP no arquivo JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Será necessário permitir o acesso a IPs correspondentes ao Backup do Azure, ao Armazenamento do Azure e ao Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso a FQDNs de serviço

Também é possível usar os seguintes FQDNs para permitir o acesso aos serviços necessários de seus servidores:

| Serviço    | Nomes de domínio a serem acessados                             |
| -------------- | ------------------------------------------------------------ |
| Backup do Azure  | `*.backup.windowsazure.com`                             |
| Armazenamento do Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Permitir acesso a FQDNs nas seções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usar um servidor proxy HTTP para rotear o tráfego

Quando você faz backup de um banco de dados SAP HANA em execução em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento ao Backup do Azure e dados ao Armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. Use a lista de IPs e FQDNs mencionados acima para permitir o acesso aos serviços necessários. Não há suporte para os servidores proxy autenticados.

> [!NOTE]
> Não há suporte para proxy de nível de serviço. Ou seja, o tráfego pelo proxy de apenas alguns serviços ou selecionados (serviços de backup do Azure) não tem suporte. Todo o tráfego ou dados pode ser roteado por proxy ou não.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **Introdução**, selecione **Backup**. Em **Em que local sua carga de trabalho é executada?** , selecione **SAP HANA em VM do Azure**.
2. Selecione **Iniciar Descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre.

   * Após a descoberta, as VMs não protegidas aparecem no portal, listadas por nome e grupo de recursos.
   * Se uma VM não estiver listada conforme o esperado, verifique se ela já foi copiada em backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas elas pertencerão a diferentes grupos de recursos.

3. Em **Selecionar Máquinas Virtuais**, selecione o link para baixar o script que fornece permissões para que o serviço de Backup do Azure acesse as VMs SAP HANA para descoberta de banco de dados.
4. Execute o script em cada VM que hospeda os bancos de dados SAP HANA dos quais você deseja fazer backup.
5. Depois de executar o script nas VMs, em **Selecionar Máquinas Virtuais**, selecione as VMs. Em seguida, selecione **Descobrir BDs**.
6. O Backup do Azure descobre todos os bancos de dados do SAP HANA na VM. Durante a descoberta, o Backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente é instalado no banco de dados.

    ![Descobrir bancos de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar o backup  

Agora, habilite o backup.

1. Na etapa 2, selecione **Configurar backup**.

    ![Configurar backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Em **Selecionar itens para fazer backup**, selecione todos os bancos de dados que deseja proteger > **OK**.

    ![Selecionar itens para fazer backup](./media/backup-azure-sap-hana-database/select-items.png)
3. Em **Política de Backup** > **Escolher política de backup**, crie uma política de backup para os bancos de dados, de acordo com as instruções abaixo.

    ![Escolher política de backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Depois de criar a política, no menu **backup** , selecione **habilitar backup**.

    ![Habilitar backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

>[!NOTE]
>O Backup do Azure não é ajustado automaticamente para alterações do horário de verão ao fazer backup de um banco de dados SAP HANA em execução em uma VM do Azure.
>
>Modifique a política manualmente, se necessário.

Especifique as configurações de política da seguinte maneira:

1. Em **Nome da política**, insira um nome para a nova política.

   ![Inserir o nome da política](./media/backup-azure-sap-hana-database/policy-name.png)
1. Em **Política de Backup Completo**, selecione uma **Frequência de Backup** escolhendo **Diária** ou **Semanal**.
   * **Diariamente**: selecione a hora e fuso horário em que o trabalho de backup começa.
       * Você precisa executar um backup completo. Você não pode desativar essa opção.
       * Selecione **Backup Completo** para exibir a política.
       * Você não pode criar backups diferenciais para backups diários completos.
   * **Semanalmente**: selecione o dia da semana, a hora e o fuso horário em que o trabalho de backup é executado.

   ![Selecionar a frequência de backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. Em **Período de Retenção**, defina as configurações de retenção para o backup completo.
    * Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e marque os que você deseja.
    * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    * O backup para um dia específico é marcado e mantido com base na configuração e no período de retenção semanal.
    * Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

1. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
1. Selecione **Backup Diferencial** para adicionar uma política diferencial.
1. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    * No máximo, você pode acionar um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

    ![Política de backup diferencial](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Você pode escolher um diferencial ou um incremental como um backup diário, mas não ambos.
1. Em **Política de Backup Incremental**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    * No máximo, você pode disparar um backup incremental por dia.
    * Backups incrementais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

    ![Política de backup incremental](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
1. Selecione **Backup de Log** para adicionar uma política de backup de log transacional.
    * Em **Backup de Log**, selecione **Habilitar**.  Isso não pode ser desabilitado, pois SAP HANA gerencia todos os backups de log.
    * Defina a frequência e os controles de retenção.

    > [!NOTE]
    > Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.

1. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
1. Depois de terminar de definir a política de backup, selecione **OK**.

> [!NOTE]
> Cada backup de log é encadeado ao backup completo anterior para formar uma cadeia de recuperação. Esse backup completo será retido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é mantido por um período extra para garantir que todos os logs possam ser recuperados. Vamos supor que um usuário tenha um backup completo semanal, os logs diferenciais diários e de 2 horas. Todos eles são retidos por 30 dias. Porém, a semana completa pode ser realmente limpa/excluída somente depois que o próximo backup completo estiver disponível, ou seja, após 30 a 7 dias. Por exemplo, um backup completo semanal ocorre em 16 de novembro. De acordo com a política de retenção, ela deve ser retida até 16 de dezembro. O último backup de log para esse completo ocorre antes do próximo completo agendado, em 22 de novembro. Até que esse log esteja disponível até 22 de dezembro, o completo de 16 de novembro não poderá ser excluído. Portanto, o completo de 16 de novembro é mantido até 22 de dezembro.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:

1. No menu do cofre, selecione **itens de backup**.
2. Em **itens de backup**, selecione a VM que executa o banco de dados SAP Hana e, em seguida, selecione **fazer backup agora**.
3. Em **fazer backup agora**, escolha o tipo de backup que você deseja executar. Depois, selecione **OK**. Esse backup será mantido de acordo com a política associada a este item de backup.
4. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho do banco dados, a criação do backup inicial pode demorar um pouco.

Por padrão, a retenção de backups sob demanda é de 45 dias.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar o backup do SAP HANA Studio em um banco de dados com o Backup do Azure habilitado

Se você quiser fazer um backup local (usando o HANA Studio) de um banco de dados cujo backup está sendo feito com o Backup do Azure, faça o seguinte:

1. Aguarde até que os backups completos ou de log do banco de dados sejam concluídos. Verifique o status no SAP HANA Studio/Cockpit.
1. Desabilite os backups de log e defina o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
1. Para fazer isso, clique duas vezes em **systemdb** > **Configuração** > **Selecionar Banco de Dados** > **Filtrar (Log)** .
1. Defina **enable_auto_log_backup** como **Não**.
1. Defina **log_backup_using_backint** como **Falso**.
1. Defina **catalog_backup_using_backint** como **false**.
1. Faça um backup completo sob demanda do banco de dados.
1. Aguarde até que o backup completo e o backup do catálogo sejam concluídos.
1. Reverter as configurações anteriores para as do Azure:
    * Defina **enable_auto_log_backup** como **Sim**.
    * Defina **log_backup_using_backint** como **Verdadeiro**.
    * Defina **catalog_backup_using_backint** como **true**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar bancos de dados do SAP HANA em execução em VMs do Azure](./sap-hana-db-restore.md)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](./sap-hana-db-manage.md)
