---
title: 'Tutorial: Migrar o SQL Server para uma Instância Gerenciada de SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server para uma Instância Gerenciada de SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 2dfcb4ade9da76d5ec4883e9f4b5b3cb3e0c8682
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076829"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Tutorial: Migrar o SQL Server para a Instância Gerenciada de SQL do Azure offline usando DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma Instância do SQL Server para uma [Instância Gerenciada de SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Para conhecer os métodos adicionais que podem exigir algum esforço manual, confira o artigo [Migração da instância do SQL Server para a Instância Gerenciada de SQL](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

Neste tutorial, você migrará o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> - Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> - Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> - Executar a migração.
> - Monitorar a migração.
> - Baixe um relatório de migração.

> [!IMPORTANT]
> Para migrações offline do SQL Server para uma Instância Gerenciada de SQL, o Serviço de Migração de Banco de Dados do Azure pode criar os arquivos de backup para você. Como alternativa, você pode fornecer o backup de banco de dados completo mais recente no compartilhamento de rede SMB que o serviço usará para migrar os bancos de dados. Não acrescente vários backups em uma única mídia de backup; faça cada backup em um arquivo de backup separado. Observe que você pode usar backups compactados também, a fim de reduzir a probabilidade de ocorrência de possíveis problemas com a migração de backups grandes.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para um Instância Gerenciada de SQL. Para uma migração online, confira [Migrar o SQL Server para um Instância Gerenciada de SQL online usando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Conheça as topologias de rede das migrações da Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure](./resource-network-topologies.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

- Verifique se as regras do grupo de segurança de rede da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433. Se a instância padrão estiver escutando em alguma outra porta, adicione-a ao firewall.
- Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
- Se você estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez precise adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
- Crie um Instância Gerenciada de SQL seguindo os detalhes do artigo [Criar uma Instância Gerenciada de SQL no portal do Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
- Verifique se os logons usados para conectar o SQL Server de origem e a instância gerenciada de SQL de destino são membros da função de servidor sysadmin.

    >[!NOTE]
    >Por padrão, o Serviço de Migração de Banco de Dados do Azure é compatível apenas com a migração de logons SQL. No entanto, você pode habilitar a capacidade de migrar logons do Windows executando as seguintes ações:
    >
    >- Garantindo que a Instância Gerenciada de SQL de destino tenha acesso de leitura ao AAD, que pode ser configurado por meio do portal do Azure por um usuário com a função de **Administrador Global**.
    >- Configurando sua instância do Serviço de Migração de Banco de Dados do Azure para habilitar as migrações de logon de usuário/grupo do Windows, que são configuradas por meio do portal do Azure, na página de Configuração. Depois de habilitar essa configuração, reinicie o serviço para que as alterações entrem em vigor.
    >
    > Depois de reiniciar o serviço, os logons de usuário/grupo do Windows aparecerão na lista de logons disponíveis para migração. Para todo logon de usuário/grupo do Windows que você migrar, será solicitado que forneça o nome de domínio associado. As contas de usuário de serviço (conta com nome de domínio NT AUTHORITY) e as contas de usuário virtual (nome da conta com o nome de domínio NT SERVICE) não são compatíveis.

- Crie um compartilhamento de rede que pode ser usado pelo Serviço de Migração de Banco de Dados do Azure para fazer backup do banco de dados de origem.
- Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
- Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Serviço de Migração de Banco de Dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner do Armazenamento do Azure para a operação de restauração.
- Crie um contêiner de blobs e recupere seu URI SAS usando as etapas descritas no artigo [Gerenciar os recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) e lembre-se de selecionar todas as permissões (Leitura, Gravação, Exclusão, Lista) na janela da política ao criar o URI SAS. Esse detalhe fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento para carregar os arquivos de backup usados ao migrar bancos de dados para a Instância Gerenciada de SQL.

    > [!NOTE]
    > O Serviço de Migração de Banco de Dados do Azure não é compatível com o uso de um token SAS no nível da conta ao definir as configurações da Conta de Armazenamento durante a etapa [Definir as Configurações de Migração](#configure-migration-settings).
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise **Serviço de Migração de Banco de Dados do Azure** e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à Instância Gerenciada de SQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

    Para obter detalhes adicionais, confira o artigo [Topologias de rede para migrações da Instância Gerenciada de SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](./resource-network-topologies.md).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, procure o nome da instância que você criou e, em seguida, selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada de SQL do Azure**; e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

   ![Criar projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não fornecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Clique em **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usa o SSIS (SQL Server Integration Services), no momento, o DMS não dá suporte à migração do banco de dados de catálogo dos seus projetos e pacotes do SSIS (SSISDB) do SQL Server para a Instância Gerenciada de SQL. No entanto, é possível provisionar o SSIS no ADF (Azure Data Factory) e reimplantar os projetos e os pacotes do SSIS no SSISDB de destino hospedado pela Instância Gerenciada de SQL. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).

5. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino de migração**, especifique os detalhes da conexão de destino, que é a Instância Gerenciada de SQL pré-provisionada para a qual o banco de dados **AdventureWorks2012** está sendo migrado.

    Se você ainda não provisionou a Instância Gerenciada de SQL, clique no [link](../azure-sql/managed-instance/instance-create-quickstart.md) para obter ajuda nessa tarefa. É possível continuar assim mesmo com a criação do projeto e, em seguida, quando a Instância Gerenciada de SQL estiver pronta, retornar a este projeto específico para executar a migração.

    ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Clique em **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Clique em **Salvar**.

## <a name="select-logins"></a>Selecionar logons

1. Na tela **Selecionar logons**, selecione os logons que deseja migrar.

    >[!NOTE]
    >Por padrão, o Serviço de Migração de Banco de Dados do Azure é compatível apenas com a migração de logons SQL. Para habilitar o suporte à migração de logons do Windows, consulte a seção **Pré-requisitos** deste tutorial.

    ![Selecionar logons](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Clique em **Salvar**.

## <a name="configure-migration-settings"></a>Definir as configurações de migração

1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | Parâmetro | Descrição |
    |--------|---------|
    |**Escolher opção de backup de origem** | Escolha a opção **Fornecerei os arquivos de backup mais recentes** quando você já tiver arquivos de backup completos disponíveis para uso pelo DMS para a migração de banco de dados. Escolha a opção **Permitirei que o Serviço de Migração de Banco de Dados do Azure crie arquivos de backup** quando desejar que o DMS faça o backup completo do banco de dados de origem primeiro e use-o para a migração. |
    |**Compartilhamento do local da rede** | O compartilhamento de rede SMB local no qual o Serviço de Migração de Banco de Dados do Azure pode fazer os backups do banco de dados de origem. A conta de serviço que executa a instância do SQL Server de origem deve ter privilégios de gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”.|
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Serviço de Migração de Banco de Dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner do Armazenamento do Azure para a operação de restauração. Se os bancos de dados habilitados para TDE forem selecionados para migração, o usuário do Windows acima deve ser a conta de administrador interno e [Controle de Conta de Usuário](/windows/security/identity-protection/user-account-control/user-account-control-overview) deve ser desabilitado para o Serviço de Migração de Banco de Dados carregar e excluir os arquivos de certificados). |
    |**Senha** | Senha do usuário. |
    |**Configurações da conta de armazenamento** | O URI SAS que fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento em que o serviço carrega os arquivos de backup e que é usado para a migração de bancos de dados para a Instância Gerenciada de SQL. [Saiba como obter o URI SAS do contêiner de blobs](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Esse URI de SAS deve ser para o contêiner de BLOB, não para a conta de armazenamento.|
    |**Configurações de TDE** | Se você estiver migrando os bancos de dados de origem com a criptografia TDE (Transparent Data Encryption) habilitada, será necessário ter privilégios de gravação na Instância Gerenciada de SQL de destino.  Selecione a assinatura na qual foi provisionada a Instância Gerenciada de SQL no menu suspenso.  Selecione a **Instância Gerenciada do Banco de Dados SQL do Azure** de destino no menu suspenso. |

    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Clique em **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Expanda a seção **Opção de validação** para exibir a tela **Escolher a opção de validação**, especifique se é preciso validar os bancos de dados migrados para correção do esquema e, em seguida, selecione **Salvar**.

3. Analise e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Clique em **Salvar**.

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Executar migração**.

  A janela de atividade de migração aparece e o status da atividade está **Pendente**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição.

   ![Captura de tela que mostra a tela de atividade de migração e o botão Atualizar.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    É possível expandir ainda mais as categorias de logon e banco de dados para monitorar o status da migração dos respectivos objetos de servidor.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Baixar relatório** para obter um relatório que lista os detalhes associados ao processo de migração.

3. Verifique se o banco de dados de destino está no ambiente da Instância Gerenciada de SQL de destino.

## <a name="next-steps"></a>Próximas etapas

- Para ver um tutorial que mostra como migrar um banco de dados para a Instância Gerenciada de SQL usando o comando T-SQL RESTORE, confira [Restaurar um backup para a Instância Gerenciada de SQL usando o comando RESTORE](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Para obter informações sobre a Instância Gerenciada de SQL, confira [O que é a Instância Gerenciada de SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Para obter informações sobre como conectar aplicativos à Instância Gerenciada de SQL, confira [Conectar aplicativos](../azure-sql/managed-instance/connect-application-instance.md).
