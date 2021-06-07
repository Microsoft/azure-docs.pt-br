---
title: 'Tutorial: Migrar o MongoDB offline para a API do Azure Cosmos DB para MongoDB'
titleSuffix: Azure Database Migration Service
description: Faça uma migração offline do MongoDB local para a API do Azure Cosmos DB para MongoDB usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443860"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Tutorial: Migrar o MongoDB offline para a API do Azure Cosmos DB para MongoDB

Use o Serviço de Migração de Banco de Dados do Azure para fazer uma migração offline (única) de bancos de dados de uma instância local ou de nuvem do MongoDB para a API do Azure Cosmos DB para MongoDB.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

Neste tutorial, você migrará um conjunto de dados no MongoDB que é hospedado em uma máquina virtual do Azure. Usando o Serviço de Migração de Banco de Dados do Azure, você migrará o conjunto de dados para a API do Azure Cosmos DB para MongoDB. Se ainda não houver uma origem do MongoDB configurada, confira [Instalar e configurar o MongoDB em uma VM do Windows no Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Concluir as etapas de pré-migração](../cosmos-db/mongodb-pre-migration.md), como estimar a taxa de transferência e escolher uma chave de partição.
* [Criar uma conta da API do Azure Cosmos DB para MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o Azure Resource Manager. Este modelo de implantação fornece conectividade site a site com os servidores de origem locais usando o [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [documentação da Rede Virtual do Azure](../virtual-network/index.yml) e, especificamente, os artigos de "início rápido" com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL ou ponto de extremidade do Azure Cosmos DB)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Verifique se as regras do NSG (grupo de segurança de rede) da rede virtual não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para obter mais informações, consulte [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor de origem do MongoDB, que, por padrão, é a porta TCP 27017.
* Ao usar um dispositivo de firewall na frente dos seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

## <a name="configure-the-server-side-retry-feature"></a>Configurar o recurso de Repetição do Lado do Servidor

Você poderá se beneficiar das funcionalidades de governança de recursos se migrar do MongoDB para o Azure Cosmos DB. Com essas funcionalidades, você poderá fazer uso completo das suas unidades de solicitação (RU/s) provisionadas de taxa de transferência. O Azure Cosmos DB poderá restringir determinada solicitação do Serviço de Migração de Banco de Dados no decorrer da migração se essa solicitação exceder as RU/s provisionadas do contêiner. Em seguida, essa solicitação precisará ser repetida.

O Serviço de Migração de Banco de Dados tem a capacidade de executar repetições. É importante entender que o tempo de ida e volta envolvido no salto de rede entre o Serviço de Migração de Banco de Dados e o Azure Cosmos DB afeta o tempo de resposta geral dessa solicitação. Aprimorar o tempo de resposta para solicitações limitadas pode reduzir o tempo total necessário para a migração.

O recurso Repetição do Lado do Servidor do Azure Cosmos DB permite que o serviço intercepte os códigos de erro de restrição e repita a operação com um tempo de ida e volta muito menor, aprimorando significativamente os tempos de resposta da solicitação.

Para usar a Repetição do Lado do Servidor, no portal do Azure Cosmos DB, selecione **Recursos** > **Repetição do Lado do Servidor**.

![Captura de tela que mostra a localização do recurso Repetição do Lado do Servidor.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Se o recurso estiver desabilitado, selecione **Habilitar**.

![Captura de tela que mostra como habilitar a Repetição do Lado do Servidor.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Captura de tela que mostra as assinaturas do portal.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Captura de tela que mostra os provedores de recursos.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Captura de tela que mostra como registrar o provedor de recursos.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Captura de tela que mostra o Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Captura de tela que mostra como criar uma instância do Serviço de Migração de Banco de Dados do Azure.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Em **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recursos novo ou existente.

4. Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure. 

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure o acesso à instância de origem do MongoDB e à conta de destino do Azure Cosmos DB.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Captura de tela que mostra as definições de configuração da instância do Serviço de Migração de Banco de Dados do Azure.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar o serviço, localize-o no portal do Azure e abra-o. Em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Captura de tela que mostra como localizar todas as instâncias do Serviço de Migração de Banco de Dados do Azure.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, pesquise o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

3. Selecione **+ Novo Projeto de Migração**.

4. Em **Novo projeto de migração**, especifique um nome para o projeto e, na caixa de texto **Tipo de servidor de origem**, selecione **MongoDB**. Na caixa de texto **Tipo de servidor de destino**, selecione **CosmosDB (API do MongoDB)** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**. 

    ![Captura de tela que mostra as opções do projeto.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhes da origem**, especifique os detalhes da conexão do servidor de origem do MongoDB.

   > [!IMPORTANT]
   > O Serviço de Migração de Banco de Dados do Azure não dá suporte ao Azure Cosmos DB como origem.

    Há três modos para se conectar a uma origem:
   * O **modo padrão**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, um número da porta e as credenciais de conexão.
   * O **modo de cadeia de conexão**, que aceita uma cadeia de conexão do MongoDB, conforme descrito no artigo [Formato de URI da cadeia de conexão](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento do Azure**, que aceita um URL de SAS de contêiner de blob. Selecione **O blob contém despejos BSON** se o contêiner de blob tiver despejos BSON produzidos pela [ferramenta bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) do MongoDB. Não selecione essa opção se o contêiner contiver arquivos JSON.

     Se você escolher essa opção, verifique se a cadeia de conexão da conta de armazenamento é exibida no seguinte formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Encontre essa cadeia de conexão SAS do contêiner de blob no Gerenciador de Armazenamento do Azure. A criação da SAS para o contêiner em questão fornece a você a URL no formato solicitado.
     
     Além disso, com base nas informações de despejo de tipo do Armazenamento do Azure, tenha os seguintes detalhes em mente:

     * Para despejos BSON, os dados do contêiner de blob precisam estar no formato bsondump. Coloque os arquivos de dados em pastas com os mesmos nomes dos bancos de dados no formato *collection.bson*. Nomeie os arquivos de metadados usando o formato *collection.metadata.json*.

     * Para despejos JSON, os arquivos no contêiner de blobs devem ser inseridos em pastas nomeadas de acordo com os bancos de dados que as contêm. Dentro de cada pasta de banco de dados, os arquivos de dados precisam ser colocados em uma subpasta chamada *data* e nomeados com o formato *collection.json*. Coloque os arquivos de metadados em uma subpasta chamada *metadata* e nomeados com o mesmo formato, *collection.json*. Os arquivos de metadados devem estar no mesmo formato que o produzido pela ferramenta bsondump MongoDB.

    > [!IMPORTANT]
    > Não recomendamos que você use um certificado autoassinado no servidor MongoDB. Caso precise usar um, conecte-se ao servidor usando o modo de cadeia de conexão e verifique se a cadeia de conexão tem aspas ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Use também o endereço IP para situações em que a resolução de nomes DNS não seja possível.

   ![Captura de tela que mostra a especificação dos detalhes de origem.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino de migração**, especifique os detalhes de conexão para o banco de dados de destino do Azure Cosmos DB. Essa conta é a API do Azure Cosmos DB pré-provisionada para a conta do MongoDB para a qual você está migrando os dados do MongoDB.

    ![Captura de tela que mostra a especificação dos detalhes de destino.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Clique em **Salvar**.

## <a name="map-to-target-databases"></a>Mapear para bancos de dados de destino

1. No **são mapeados para os bancos de dados de destino** tela, a origem e o banco de dados de destino para migração do mapa.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    Se **Criar** for exibido ao lado do nome do banco de dados, isso indicará que o Serviço de Migração de Banco de Dados do Azure não encontrou o banco de dados de destino e o serviço criará o banco de dados para você.

    Neste momento da migração, é possível [provisionar a taxa de transferência](../cosmos-db/set-throughput.md). No Azure Cosmos DB, você pode provisionar a taxa de transferência no nível do banco de dados ou individualmente para cada coleção. A taxa de transferência é medida em [unidades de solicitação](../cosmos-db/request-units.md). Saiba mais sobre o [preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Captura de tela que mostra o mapeamento para bancos de dados de destino.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Clique em **Salvar**.
3. Na tela **Configuração de coleção**, expanda e analise a lista de coleções a ser migrada.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as coleções na instância de origem do MongoDB que não estão presentes na conta de destino do Azure Cosmos DB. Caso deseje migrar novamente as coleções que já contêm dados, selecione as coleções neste painel de maneira explícita.

    É possível especificar o número de RUs a serem usados nas coleções. O Serviço de Migração de Banco de Dados do Azure sugere padrões inteligentes com base no tamanho da coleção.

    > [!NOTE]
    > Execute a migração e a coleção de banco de dados em paralelo. Se necessário, você pode usar várias instâncias do Serviço de Migração de Banco de Dados do Azure para acelerar a execução.

    Também é possível especificar uma chave fragmentada para aproveitar o [particionamento no Azure Cosmos DB](../cosmos-db/partitioning-overview.md) e obter a escalabilidade ideal. Examine as [melhores práticas para escolher uma chave de fragmentação/partição](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Captura de tela que mostra a seleção de tabelas de coleções.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Clique em **Salvar**.

5. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

    ![Captura de tela que mostra o resumo da migração.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

Selecione **Executar migração**. A janela da atividade de migração será exibida, e o status da atividade será **Não iniciada**.

![Captura de tela que mostra o status da atividade.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição até que o status da migração seja exibido como **Concluído**.

> [!NOTE]
> Selecione a atividade para obter detalhes sobre as métricas de migração no nível do banco de dados e da coleção.

![Captura que mostra o status da atividade concluído.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Verificar os dados no Azure Cosmos DB

Após a conclusão da migração, verifique sua conta do Azure Cosmos DB para confirmar se todas as coleções foram migradas com êxito.

![Captura de tela que mostra onde verificar a sua conta do Azure Cosmos DB para confirmar se todas as coleções foram migradas com êxito.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Otimização após a migração

Depois de migrar os dados armazenados no banco de dados do MongoDB para a API do Azure Cosmos DB para MongoDB, você poderá se conectar ao Azure Cosmos DB e gerenciar os dados. Você também poderá executar outras etapas de otimização pós-migração. Isso pode incluir a otimização da política de indexação, a atualização do nível de consistência padrão ou a configuração da distribuição global para sua conta do Azure Cosmos DB. Para obter mais informações, confira [Otimização pós-migração](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Próximas etapas

Examine as diretrizes de migração para obter outros cenários no [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).



