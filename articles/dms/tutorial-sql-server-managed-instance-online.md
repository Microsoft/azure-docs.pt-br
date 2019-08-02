---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para realizar uma migração online do SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como realizar uma migração online do SQL Server local para uma instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/14/2019
ms.openlocfilehash: 4e45251147561f2376ac4b044ebdf3a599092dcf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67126096"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migrar SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure online usando DMS

Use o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para uma [instância gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance.md) com tempo de inatividade mínimo. Para obter métodos adicionais que possam exigir algum esforço manual, confira o artigo [Migração da instância do SQL Server para a instância gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migrará o banco de dados do **Adventureworks2012** de uma instância local do SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure com tempo de inatividade mínimo usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração e inicie uma migração online usando o Serviço de Migração de Banco de Dados do Azure.
> * Monitorar a migração.
> * Substitua a migração quando você estiver pronto.

> [!IMPORTANT]
> Para migrações online do SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure, é necessário fornecer o backup de banco de dados completo e os backups de log seguintes no compartilhamento de rede SMB que o serviço pode usar para migrar seus bancos de dados. O Serviço de Migração de Banco de Dados do Azure não inicia backups, mas em vez disso, usa os backups existentes, que você já possa ter como parte de seu plano de recuperação de desastre, para a migração.
> Lembre-se de fazer [backups usando a opção WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Além disso, lembre-se de não acrescentar vários backups (ou seja, completo e t-log) em uma única mídia de backup; faça cada backup em um arquivo de backup separado.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure. Para uma migração offline, consulte [Migrar SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure offline usando DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Criar uma VNET (Rede Virtual) do Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Saiba mais sobre as topologias de rede para migrações da instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi). Para obter mais informações sobre como criar uma VNET, confira a [Documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNET, se você usar ExpressRoute com emparelhamento de rede para Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a Internet.

* Verifique se as regras do Grupo de Segurança de Rede da VNET não bloqueiam as seguintes portas de comunicação de entrada com o Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445, 12000. Veja mais detalhes sobre a filtragem de tráfego do NSG da VNet do Azure no artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
* Caso esteja executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez seja preciso habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada em seu servidor de origem.
* Se estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez seja preciso adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, além de arquivos por meio da porta 445 do SMB.
* Crie uma instância gerenciada do Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar uma instância gerenciada do Banco de Dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
* Verifique se os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino são membros da função de servidor sysadmin.
* Forneça um compartilhamento de rede SMB que contém todos os seus arquivos de backup de banco de dados completo do banco de dados e arquivos de backup de log de transações subsequentes que o Serviço de Migração de Banco de Dados do Azure pode usar para a migração de banco de dados.
* Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
* Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Serviço de Migração de Banco de Dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
* Crie uma ID do aplicativo do Azure Active Directory que gera a chave da ID do aplicativo que o serviço do DMS pode usar para se conectar à instância gerenciada do Banco de Dados SQL do Azure e o Contêiner do Armazenamento do Microsoft Azure de destino. Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > O DMS requer a permissão de Colaborador na assinatura para a ID do aplicativo especificado. Estamos trabalhando ativamente para reduzir esses requisitos de permissões.

* Criar ou anote o **Nível de desempenho Standard**, a Conta de Armazenamento do Azure, que permite que o serviço do DMS faça upload dos arquivos de backup do banco de dados para usá-los na migração de bancos de dados.  Crie a conta de armazenamento do Azure na mesma região que o serviço do DMS criado.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise **Serviço de Migração de Banco de Dados do Azure** e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma VNET existente ou crie uma.

    A VNET fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao SQL Server de origem e à instância gerenciada do Banco de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, confira o artigo [Topologias de rede para migrações da Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

6. Selecione um SKU do tipo de preço Premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, procure o nome da instância que você criou e, em seguida, selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto; na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e, em seguida, em **Escolher tipo de atividade**, selecione **Migração de dados online**.

   ![Criar projeto do DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões SSL que são criptografadas usando um certificado autoassinado não fornecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no SSL usando certificados autoassinados em um ambiente de produção ou em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Clique em **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usa o SSIS (SQL Server Integration Services), o DMS não dá suporte, no momento, à migração do banco de dados de catálogo dos seus projetos/pacotes do SSIS (SSISDB) do SQL Server para a instância gerenciada do Banco de Dados SQL do Azure. No entanto, é possível provisionar o SSIS no ADF (Azure Data Factory) e reimplantar os projetos/pacotes do SSIS no SSISDB de destino hospedado pela instância gerenciada do Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes de destino de migração**, especifique a **ID do aplicativo** e a **Chave** que a instância de DMS pode usar para se conectar à instância de destino da instância gerenciada do Banco de Dados SQL do Azure e a Conta de Armazenamento do Azure.

    Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selecione a **Assinatura** que contém a instância de destino da instância gerenciada do Banco de Dados SQL do Azure e selecione-a.

    Se você ainda não tiver provisionado a instância gerenciada do Banco de Dados SQL do Azure, selecione o [link](https://aka.ms/SQLDBMI) para ajudar você a provisionar a instância. Quando a instância gerenciada do Banco de Dados SQL do Azure estiver pronta, retorne a este projeto específico para executar a migração.

3. Forneça o **Usuário do SQL** e a **Senha** para se conectar à instância gerenciada do Banco de Dados SQL do Azure.

    ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Clique em **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Clique em **Salvar**.

## <a name="configure-migration-settings"></a>Definir as configurações de migração

1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | | |
    |--------|---------|
    |**Compartilhamento do local de rede SMB** | O compartilhamento de rede SMB local que contém os seus arquivos de backup de banco de dados completos e os arquivo de backup de log de transações que o Serviço de Migração de Banco de Dados do Azure pode usar para a migração. A conta de serviço que executa a instância do SQL Server de origem precisa ter privilégios de leitura\gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”.|
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Serviço de Migração de Banco de Dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração. |
    |**Senha** | Senha do usuário. |
    |**Assinatura da Conta de Armazenamento do Azure** | Selecione a assinatura que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento do Azure** | Selecione a Conta de Armazenamento do Azure da qual o DMS pode fazer upload dos arquivos de backup para o compartilhamento de rede do SMB e usá-los para a migração de banco de dados.  É recomendável selecionar a Conta de Armazenamento na mesma região que o serviço do DMS para um desempenho ideal de upload de arquivo. |

    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Clique em **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Analise e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Executar e monitorar a migração

1. Selecione **Executar migração**.

2. Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    É possível expandir ainda mais as categorias de logon e banco de dados para monitorar o status da migração dos respectivos objetos de servidor.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Executar a substituição de migração

Depois que o backup do banco de dados completo for restaurado na instância de destino da instância gerenciada do Banco de Dados SQL do Azure, o banco de dados estará disponível para executar uma substituição de migração.

1. Quando você estiver pronto para concluir a migração de banco de dados online, selecione **Iniciar substituição**.

2. Interrompa todo o tráfego de entrada para os bancos de dados de origem.

3. Obtenha o [backup da parte final do log], disponibilize o arquivo de backup no compartilhamento de rede SMB e aguarde até que esse backup de log de transações final seja restaurado.

    Nesse ponto, você verá **Alterações pendentes** definido como 0.

4. Selecione **Confirmar**e, em seguida, **Aplicar**.

    ![Preparar para a substituição completa](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando o status de migração de banco de dados mostrar **Concluído**, conecte seus aplicativos à nova instância de destino da instância gerenciada do Banco de Dados SQL do Azure.

    ![Substituição concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter um tutorial mostrando como migrar um banco de dados para uma instância gerenciada usando o comando T-SQL RESTORE, confira [Restore a backup to a managed instance using the restore command](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) (Restaurar um backup para uma Instância Gerenciada usando o comando restaurar).
- Para saber mais sobre a instância gerenciada, confira [O que é uma instância gerenciada](../sql-database/sql-database-managed-instance.md).
- Para saber mais sobre como conectar aplicativos a uma instância gerenciada, confira [Conectar aplicativos](../sql-database/sql-database-managed-instance-connect-app.md).
