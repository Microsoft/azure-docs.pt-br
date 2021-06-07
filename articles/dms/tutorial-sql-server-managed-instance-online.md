---
title: 'Tutorial: Migrar online o SQL Server para a Instância Gerenciada de SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como executar uma migração online do SQL Server para uma Instância Gerenciada de SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 5585eb210b54dfa016d25c430256508e1b0b9f61
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076880"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Migrar online o SQL Server para uma Instância Gerenciada de SQL do Azure usando o DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server para uma [Instância Gerenciada de SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) com um tempo de inatividade mínimo. Para conhecer os métodos adicionais que podem exigir um pouco de esforço manual, confira o artigo [Migração da instância do SQL Server para a Instância Gerenciada de SQL do Azure](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

Neste tutorial, você migrará o banco de dados **Adventureworks2012** de uma instância local do SQL Server para um Instância Gerenciada de SQL com um tempo de inatividade mínimo usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração e inicie uma migração online usando o Serviço de Migração de Banco de Dados do Azure.
> * Monitorar a migração.
> * Realize a substituição da migração quando você estiver pronto.

> [!IMPORTANT]
> Para migrações online do SQL Server para uma Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure, é necessário fornecer o backup completo do banco de dados e os backups de log seguintes no compartilhamento de rede SMB que o serviço pode usar para migrar os bancos de dados. O Serviço de Migração de Banco de Dados do Azure não inicia backups, mas em vez disso, usa os backups existentes, que você já possa ter como parte de seu plano de recuperação de desastre, para a migração.
> Lembre-se de fazer [backups usando a opção WITH CHECKSUM](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). Além disso, lembre-se de não acrescentar vários backups (ou seja, completo e t-log) em uma única mídia de backup; faça cada backup em um arquivo de backup separado. Por fim, você pode usar backups compactados para reduzir a probabilidade de ocorrência de problemas potenciais associados à migração de backups de grande porte.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure do banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

> [!IMPORTANT]
> Reduza a duração do processo de migração online o máximo possível para minimizar o risco de interrupção causada pela reconfiguração da instância ou pela manutenção planejada. Caso um desses eventos ocorra, o processo de migração será iniciado desde o começo. No caso de manutenção planejada, há um período de carência de 36 horas antes da reinicialização do processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para um Instância Gerenciada de SQL. Para uma migração offline, confira [Migrar offline o SQL Server para um Instância Gerenciada de SQL usando o DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Conheça as topologias de rede das migrações da Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure](./resource-network-topologies.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o Serviço de Migração de Banco de Dados do Azure no modo híbrido (versão prévia). O modo híbrido beneficia-se de um trabalho de migração local junto com uma instância do Serviço de Migração de Banco de Dados do Azure em execução na nuvem. Para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido, confira o artigo [Criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido usando o portal do Azure](./quickstart-create-data-migration-service-portal.md).

    > [!IMPORTANT]
    > Em relação à conta de armazenamento usada como parte da migração, é necessário:
    > * Optar por permitir que toda a rede acesse a conta de armazenamento.
    > * Ative a [delegação de sub-rede](../virtual-network/manage-subnet-delegation.md) na sub-rede da MI e atualize as regras de firewall da Conta de Armazenamento para permitir essa sub-rede.

* Verifique se as regras do grupo de segurança de rede da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433. Se a instância padrão estiver escutando em alguma outra porta, adicione-a ao firewall.
* Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
* Se você estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez precise adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
* Crie um Instância Gerenciada de SQL seguindo os detalhes do artigo [Criar uma Instância Gerenciada de SQL no portal do Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Verifique se os logons usados para conectar o SQL Server de origem e a Instância Gerenciada de SQL de destino são membros da função de servidor sysadmin.
* Forneça um compartilhamento de rede SMB que contém todos os arquivos de backup completo do banco de dados e os arquivos de backup de log de transações seguintes que o Serviço de Migração de Banco de Dados do Azure pode usar para a migração de banco de dados.
* Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
* Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Serviço de Migração de Banco de Dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner do Armazenamento do Azure para a operação de restauração.
* Crie uma ID do Aplicativo do Azure Active Directory que gere a chave da ID do Aplicativo que poderá ser usada pelo Serviço de Migração de Banco de Dados do Azure para se conectar com a Instância Gerenciada do Banco de Dados do Azure de destino e ao Contêiner do Armazenamento do Azure. Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > O Serviço de Migração de Banco de Dados do Azure exige a permissão de Colaborador na assinatura para a ID do aplicativo especificada. Como alternativa, você pode criar funções personalizadas que concedem as permissões específicas que o Serviço de Migração de Banco de Dados do Azure exige. Para obter diretrizes passo a passo de como usar funções personalizadas, confira o artigo [Funções personalizadas para migrações online do SQL Server para a Instância Gerenciada de SQL](./resource-custom-roles-sql-db-managed-instance.md).

* Criar ou anote o **Nível de desempenho Standard**, a Conta de Armazenamento do Azure, que permite que o serviço do DMS faça upload dos arquivos de backup do banco de dados para usá-los na migração de bancos de dados.  Crie a conta do Armazenamento do Azure na mesma região da instância do Serviço de Migração de Banco de Dados do Azure.

  > [!NOTE]
  > Ao migrar um banco de dados protegido pela [Transparent Data Encryption](../azure-sql/database/transparent-data-encryption-tde-overview.md) para uma instância gerenciada usando a migração online, o certificado correspondente da instância do SQL Server da VM do Azure ou local precisa ser migrado antes da restauração do banco de dados. Para obter as etapas detalhadas, confira [Migrar um certificado TDE para uma instância gerenciada](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

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

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à Instância Gerenciada de SQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

    Para obter detalhes adicionais, confira o artigo [Topologias de rede para migrações de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure](./resource-network-topologies.md).

6. Selecione um SKU do tipo de preço Premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, procure o nome da instância que você criou e, em seguida, selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada de SQL do Azure** e, em seguida, em **Escolher o tipo de atividade**, selecione **Migração de dados online**.

   ![Criar um projeto do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não fornecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Clique em **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usa o SSIS (SQL Server Integration Services), no momento, o DMS não dá suporte à migração do banco de dados de catálogo dos seus projetos e pacotes do SSIS (SSISDB) do SQL Server para a Instância Gerenciada de SQL. No entanto, é possível provisionar o SSIS no ADF (Azure Data Factory) e reimplantar os projetos e os pacotes do SSIS no SSISDB de destino hospedado pela Instância Gerenciada de SQL. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).

5. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino da migração**, especifique a **ID do aplicativo** e a **Chave** que a instância do DMS pode usar para se conectar com a instância de destino da Instância Gerenciada de SQL e a Conta de Armazenamento do Azure.

    Para obter mais informações, confira o artigo [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar os recursos](../active-directory/develop/howto-create-service-principal-portal.md).

2. Selecione a **Assinatura** que contém a instância de destino da Instância Gerenciada de SQL e, em seguida, escolha a instância de destino.

    Se você ainda não provisionou a Instância Gerenciada de SQL, clique no [link](../azure-sql/managed-instance/instance-create-quickstart.md) para obter ajuda nessa tarefa. Quando a Instância Gerenciada de SQL estiver pronta, retorne a esse projeto específico para executar a migração.

3. Forneça o **Usuário do SQL** e a **Senha** para se conectar com a Instância Gerenciada de SQL.

    ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Clique em **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Clique em **Salvar**.

## <a name="configure-migration-settings"></a>Definir as configurações de migração

1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | Parâmetro | Descrição |
    |--------|---------|
    |**Compartilhamento da localização de rede SMB** | O compartilhamento de rede SMB local ou o compartilhamento de arquivo do Azure que contém os arquivos de backup do banco de dados completos e os arquivos de backup do log de transações que o Serviço de Migração de Banco de Dados do Azure pode usar para migração. A conta de serviço que executa a instância do SQL Server de origem precisa ter privilégios de leitura\gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”. Para aprimorar o desempenho, é recomendado usar uma pasta separada para cada banco de dados a ser migrado. Você pode fornecer o caminho de compartilhamento de arquivo no nível do banco de dados usando a opção **Configurações Avançadas**. Se você estiver encontrando problemas para se conectar com o compartilhamento SMB, confira [Compartilhamento SMB](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Serviço de Migração de Banco de Dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner do Armazenamento do Azure para a operação de restauração. Se estiver usando o compartilhamento de arquivo do Azure, use o nome da conta de armazenamento com o prefixo AZURE \ como nome de usuário. |
    |**Senha** | Senha do usuário. Se estiver usando o compartilhamento de arquivo do Azure, use uma chave de conta de armazenamento como senha. |
    |**Assinatura da Conta de Armazenamento do Azure** | Selecione a assinatura que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento do Azure** | Selecione a Conta de Armazenamento do Azure da qual o DMS pode fazer upload dos arquivos de backup para o compartilhamento de rede do SMB e usá-los para a migração de banco de dados.  É recomendável selecionar a Conta de Armazenamento na mesma região que o serviço do DMS para um desempenho ideal de upload de arquivo. |

    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se o Serviço de Migração de Banco de Dados do Azure mostrar o ‘Erro do Sistema 53’ ou o ‘Erro do Sistema 57’, a causa poderá ser a incapacidade do Serviço de Migração de Banco de Dados do Azure de acessar o compartilhamento de arquivo do Azure. Se encontrar um desses erros, permita acesso à conta de armazenamento por meio da rede virtual usando as instruções disponíveis [aqui](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se a funcionalidade de verificação de loopback estiver habilitada e o SQL Server de origem e o compartilhamento de arquivo estiverem no mesmo computador, a origem não poderá acessar os arquivos aqui usando o FQDN. Para corrigir esse problema, desabilite a funcionalidade de verificação de loopback seguindo as instruções [aqui](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

   ![Status da atividade de migração](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Executar a substituição de migração

Depois que o backup completo do banco de dados for restaurado na instância de destino da Instância Gerenciada de SQL, o banco de dados estará disponível para executar uma substituição de migração.

1. Quando você estiver pronto para concluir a migração de banco de dados online, selecione **Iniciar substituição**.

2. Interrompa todo o tráfego de entrada para os bancos de dados de origem.

3. Obtenha o [backup da parte final do log], disponibilize o arquivo de backup no compartilhamento de rede SMB e aguarde até que esse backup de log de transações final seja restaurado.

    Nesse ponto, você verá **Alterações pendentes** definido como 0.

4. Selecione **Confirmar** e, em seguida, **Aplicar**.

    ![Preparar para a substituição completa](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Após a substituição, a disponibilidade da Instância Gerenciada de SQL somente com a camada de serviço Comercialmente Crítico pode demorar significativamente mais do que com a camada de Uso Geral, pois três réplicas secundárias precisam ser propagadas para o grupo de Alta Disponibilidade Always On. A duração dessa operação depende do tamanho dos dados. Para obter mais informações, confira [Duração das operações de gerenciamento](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Quando o status da migração do banco de dados for **Concluído**, conecte seus aplicativos com a nova instância de destino da Instância Gerenciada de SQL.

    ![Substituição concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Próximas etapas

* Para ver um tutorial que mostra como migrar um banco de dados para a Instância Gerenciada de SQL usando o comando T-SQL RESTORE, confira [Restaurar um backup para a Instância Gerenciada de SQL usando o comando RESTORE](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Para obter informações sobre a Instância Gerenciada de SQL, confira [O que é a Instância Gerenciada de SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Para obter informações sobre como conectar aplicativos à Instância Gerenciada de SQL, confira [Conectar aplicativos](../azure-sql/managed-instance/connect-application-instance.md).
