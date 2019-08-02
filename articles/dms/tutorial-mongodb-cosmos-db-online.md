---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para migração online do MongoDB para a API do Azure Cosmos DB para MongoDB | Microsoft Docs'
description: Saiba como fazer uma migração online do MongoDB local para a API do Azure Cosmos DB para MongoDB usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 17f1b36ba5d5b699cce621db3917ef92654047ff
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565571"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Tutorial: Migrar o MongoDB para a API do Azure Cosmos DB para MongoDB online usando o DMS

Use o Serviço de Migração de Banco de Dados do Azure para fazer uma migração online (com tempo de inatividade mínimo) de bancos de dados de uma instância local ou de nuvem do MongoDB para a API do Azure Cosmos DB para MongoDB.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Conclua a migração quando você estiver pronto.

Neste tutorial, você migrará um conjunto de dados do MongoDB hospedado em uma Máquina Virtual do Azure para a API do Azure Cosmos DB para MongoDB com tempo de inatividade mínimo usando o Serviço de Migração de Banco de Dados do Azure. Se não houver uma origem do MongoDB configurada, confira o artigo [Instalar e configurar o MongoDB em uma VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do MongoDB à API do Azure Cosmos DB para MongoDB. Para uma migração offline, confira [Migração offline do MongoDB para a API do Azure Cosmos DB para MongoDB usando o DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Concluir as etapas de pré-migração](../cosmos-db/mongodb-pre-migration.md), tais como estimar a taxa de transferência, escolher uma chave de partição e a política de indexação.
* [Criar uma conta da API do Azure Cosmos DB para MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Criar uma VNET (Rede Virtual) do Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Durante a configuração da VNET, se você usar ExpressRoute com emparelhamento de rede para Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Verifique se as regras do NSG (Grupo de Segurança de Rede) da VNET não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Veja mais detalhes sobre a filtragem de tráfego do NSG da VNet do Azure no artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor de origem do MongoDB, que, por padrão, é a porta TCP 27017.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure.

5. Selecione uma VNET existente ou crie uma.

   A VNET fornece ao Serviço de Migração de Banco de Dados do Azure o acesso à instância de origem do MongoDB e à conta de destino do Azure Cosmos DB.

   Para mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um SKU do tipo de preço Premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium. Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, pesquise o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

    Como alternativa, você pode descobrir a instância do Serviço de Migração de Banco de Dados do Azure no painel de pesquisa do portal do Azure.

    ![Usar o painel de Pesquisa no portal do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto. Na caixa de texto **Tipo de servidor de origem**, selecione **MongoDB**; na caixa de texto **Tipo de servidor de destino**, selecione **CosmosDB (API do MongoDB)** . Em **Escolher tipo de atividade**, selecione **Migração de dados online [versão prévia]** .

    ![Criar o projeto do Serviço de Migração de Banco de Dados](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Selecione **Salvar** e, em seguida, **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhes da origem**, especifique os detalhes da conexão do servidor de origem do MongoDB.

    Há três modos para se conectar a uma origem:
   * O **modo padrão**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, o número da porta e as credenciais de conexão.
   * O **modo de cadeia de conexão**, que aceita uma cadeia de conexão do MongoDB, conforme descrito no artigo [Formato de URI de cadeia de conexão](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento do Azure**, que aceita um URL de SAS de contêiner de blob. Selecione **O blob contém despejos BSON** se o contêiner de blob tiver despejos BSON produzidos pela [ferramenta bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) do MongoDB e anule a seleção se o contêiner contiver arquivos JSON.

     Se você selecionar essa opção, verifique se a cadeia de conexão da conta de armazenamento é exibida no formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Além disso, com base nas informações de despejo de tipo no armazenamento do Azure, tenha os detalhes a seguir em mente.

     * Para despejos BSON, os dados dentro do contêiner de blobs devem estar no formato bsondump, tal que os arquivo de dados sejam inseridos em pastas nomeadas de acordo com os bancos de dados que as contêm no formato collection.bson. Arquivos de metadados (se houver) devem ser nomeados usando o formato *coleção*.metadata.json.

     * Para despejos JSON, os arquivos no contêiner de blobs devem ser inseridos em pastas nomeadas de acordo com os bancos de dados que as contêm. Dentro de cada pasta de banco de dados, os arquivos de dados devem ser inseridos em uma subpasta chamada “dados” e nomeados usando o formato *collection*.json. Arquivos de metadados (se houver) devem ser inseridos em uma subpasta chamada “metadados” e nomeada usando o mesmo formato, *collection*.json. Os arquivos de metadados devem estar no mesmo formato que o produzido pela ferramenta bsondump MongoDB.

    > [!IMPORTANT]
    > Não é recomendado usar um certificado autoassinado no servidor do Mongo. No entanto, se um certificado for usado, conecte-se ao servidor usando o **modo de cadeia de conexão** e verifique se a cadeia de conexão tem “”
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Você pode usar o endereço IP para situações em que a resolução de nome DNS não é possível.

   ![Especifique as configurações de origem](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Clique em **Salvar**.

   > [!NOTE]
   > O endereço do Servidor de origem deve ser o endereço do primário se a origem for um conjunto de réplicas e do roteador se a origem for um cluster fragmentado do MongoDB. Para um cluster fragmentado do MongoDB, o Serviço de Migração de Banco de Dados do Azure deve ser capaz de se conectar aos fragmentos individuais no cluster, o que pode exigir a abertura do firewall em mais computadores.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino da migração**, especifique os detalhes de conexão da conta de destino do Azure Cosmos DB, que é a conta pré-provisionada da API do Azure Cosmos DB para MongoDB para a qual os dados do MongoDB serão migrados.

    ![Detalhes do destino favorito](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Clique em **Salvar**.

## <a name="map-to-target-databases"></a>Mapear para bancos de dados de destino

1. No **são mapeados para os bancos de dados de destino** tela, a origem e o banco de dados de destino para migração do mapa.

   Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

   Se a cadeia de caracteres **Criar** for exibida ao lado do nome do banco de dados, isso indicará que o Serviço de Migração de Banco de Dados do Azure não encontrou o banco de dados de destino e o serviço criará o banco de dados para você.

   Nesse momento da migração, caso deseje a taxa de transferência de compartilhamento no banco de dados, especifique uma RU de taxa de transferência. No Cosmos DB, você pode provisionar a taxa de transferência no nível do banco de dados ou individualmente para cada coleção. A taxa de transferência é medida em [RUs](https://docs.microsoft.com/azure/cosmos-db/request-units) (Unidades de Solicitação). Saiba mais sobre o [preço do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapear para bancos de dados de destino](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Clique em **Salvar**.

3. Na tela **Configuração de coleção**, expanda e analise a lista de coleções a ser migrada.

   O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as coleções na instância de origem do MongoDB que não estão presentes na conta de destino do Azure Cosmos DB. Se quiser migrar novamente as coleções que já contêm dados, você precisará selecionar as coleções nesta tela de forma explícita.

   É possível especificar o número de RUs a serem usados nas coleções. Na maioria dos casos, um valor entre 500 (no mínimo 1000 para coleções fragmentadas) e 4000 deve ser suficiente. O Serviço de Migração de Banco de Dados do Azure sugere padrões inteligentes com base no tamanho da coleção.

    > [!NOTE]
    > Execute a migração de banco de dados e a coleção em paralelo usando várias instâncias do Serviço de Migração de Banco de Dados do Azure, se necessário, para acelerar a execução.

   Também é possível especificar uma chave fragmentada para aproveitar o [particionamento no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) e obter a escalabilidade ideal. Não se esqueça de analisar as [melhores práticas para selecionar uma chave fragmentada/de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Se você não tem uma chave de partição, pode usar **_id** como a chave de fragmentação para obter melhor taxa de transferência.

   ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Clique em **Salvar**.

5. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

   A janela de atividade de migração aparece e o **Status** da atividade é exibido.

   ![Status da atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

* Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Reproduzindo**.

   > [!NOTE]
   > É possível selecionar a Atividade para saber detalhes sobre as métricas de migração no nível do banco de dados e da coleção.

   ![Status de atividade Reproduzindo](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Verificar os dados no Cosmos DB

1. Faça alterações em seu banco de dados MongoDB de origem.
2. Conecte-se ao COSMOS DB para verificar se os dados estão replicados do servidor MongoDB de origem.

    ![Status de atividade Reproduzindo](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Concluir a migração

* Depois que todos os documentos da origem estiverem disponíveis no COSMOS DB de destino, selecione **Concluir** no menu de contexto da atividade de migração para concluir a migração.

    Esta ação finalizará a reprodução de todas as alterações pendentes e concluirá a migração.

    ![Status de atividade Reproduzindo](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Otimização após a migração

Depois de migrar os dados armazenados no banco de dados do MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar ao Azure Cosmos DB e gerenciar os dados. Você também pode executar outras etapas de otimização pós-migração, como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para sua conta do Azure Cosmos DB. Para obter mais informações, confira o artigo [Otimização pós-migração](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Informações sobre o serviço do Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Próximas etapas

* Revisar as diretrizes de migração para saber sobre mais cenários no [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/) da Microsoft.
