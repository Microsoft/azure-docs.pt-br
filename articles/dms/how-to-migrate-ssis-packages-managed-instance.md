---
title: Migrar pacotes do SSIS para o SQL Instância Gerenciada
titleSuffix: Azure Database Migration Service
description: Saiba como migrar pacotes do SQL Server Integration Services (SSIS) e projetos para um SQL do Azure Instância Gerenciada usando o serviço de migração de banco de dados do Azure ou o Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1f6df64a66c0700f3a13a40ea5046515a2bf1a51
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643840"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>Migrar pacotes SQL Server Integration Services para um SQL do Azure Instância Gerenciada
Se você usar SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes do SSIS do SSISDB de origem hospedado por SQL Server para o SSISDB de destino, hospedado por um Instância Gerenciada SQL do Azure, você poderá usar o serviço de migração de banco de dados do Azure.

Se a versão do SSIS usada for anterior à 2012 ou você usar tipos de armazenamento de pacotes não SSISDB, antes de migrar seus projetos/pacotes do SSIS, você precisará convertê-los usando o assistente de conversão de projeto Integration Services, que também pode ser iniciado no SSMS. Para obter mais informações, consulte o artigo [Converter projetos para o modelo de implantação de projeto](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert).

> [!NOTE]
> O serviço de migração de banco de dados do Azure (DMS) atualmente não dá suporte ao banco de dados SQL do Azure como destino de migração de destino. Para reimplantar projetos/pacotes do SSIS no banco de dados SQL do Azure, consulte o artigo [reimplantar SQL Server Integration Services pacotes no banco de dados SQL do Azure](./how-to-migrate-ssis-packages.md).

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* Para criar um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações, consulte o artigo [topologias de rede para SQL instância gerenciada migrações usando o serviço de migração de banco de dados do Azure]( https://aka.ms/dmsnetworkformi). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.
* Para garantir que suas regras de grupo de segurança de rede de rede virtual não bloqueiem a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Para configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados de origem](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Para abrir o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
* Caso esteja executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez seja preciso habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada em seu servidor de origem.
* Se estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez seja preciso adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, além de arquivos por meio da porta 445 do SMB.
* Um Instância Gerenciada do SQL para hospedar o SSISDB. Se você precisar criar um, siga os detalhes no artigo [criar um instância gerenciada de SQL do Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Para garantir que os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino sejam membros da função de servidor sysadmin.
* Para verificar se o SSIS é provisionado no Azure Data Factory (ADF) contendo Azure-SSIS Integration Runtime (IR) com o arquivo de destino SSISDB hospedado por um Instância Gerenciada SQL (conforme descrito no artigo [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](../data-factory/create-azure-ssis-integration-runtime.md)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação da origem SSISDB ainda não esteja integrada ao DMA (Assistente de Migração de banco de dados), seus projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino, hospedado em um Instância Gerenciada SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise **Serviço de Migração de Banco de Dados do Azure** e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece o serviço de migração de banco de dados do Azure com acesso ao SQL Server de origem e ao Instância Gerenciada de destino do Azure SQL.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

    Para obter detalhes adicionais, consulte o artigo [topologias de rede para Azure SQL instância gerenciada migrações usando o serviço de migração de banco de dados do Azure](./resource-network-topologies.md).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, procure o nome da instância que você criou e, em seguida, selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **SQL Server**, na caixa de texto tipo de **servidor de destino** , selecione **SQL do Azure instância gerenciada** e, em seguida, para **escolher tipo de atividade**, selecione **migração de pacote SSIS**.

   ![Criar projeto do DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não fornecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

   ![Detalhes da origem](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **detalhes do destino de migração** , especifique os detalhes de conexão para o destino.

     ![Detalhes do destino](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Para os **projetos do SSIS e a opção de substituição de ambiente (s)**, especifique se deseja substituir ou ignorar projetos e ambientes SSIS existentes.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Analise e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

## <a name="next-steps"></a>Próximas etapas

* Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).