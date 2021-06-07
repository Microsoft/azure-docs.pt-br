---
title: Migrar dados para Azure Cosmos DB conta de API do Cassandra usando o Striim
description: Saiba como usar o Striim para migrar os dados de um Oracle Database para uma conta de API do Cassandra Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 85c0a113f15a1ce94ca1cccc605085dcd003dce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661385"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrar dados para Azure Cosmos DB conta de API do Cassandra usando o Striim
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A imagem Striim no Azure Marketplace oferece movimentação de dados contínua em tempo real de data warehouses e bancos de dados para o Azure. Ao mover os dados, você pode executar a desnormalização em linha, transformação de dados, Habilitar análise em tempo real e cenários de relatórios de dados. É fácil começar a usar o Striim para mover dados corporativos continuamente para Azure Cosmos DB API do Cassandra. O Azure fornece uma oferta do Marketplace que facilita a implantação de Striim e a migração de dados para Azure Cosmos DB. 

Este artigo mostra como usar o Striim para migrar dados de um **Oracle Database** para uma **conta de API do Cassandra Azure Cosmos DB**.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Um banco de dados Oracle em execução local com algum dado.

## <a name="deploy-the-striim-marketplace-solution"></a>Implantar a solução Striim Marketplace

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso** e pesquise **Striim** no Azure Marketplace. Selecione a primeira opção e **criar**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Localizar item do Marketplace do Striim":::

1. Em seguida, insira as propriedades de configuração da instância Striim. O ambiente Striim é implantado em uma máquina virtual. No painel **básico** , insira o **nome de usuário da VM**, senha da **VM** (essa senha é usada para ssh na VM). Selecione a **assinatura**, o **grupo de recursos** e os detalhes do **local** onde você gostaria de implantar o Striim. Depois de concluído, selecione **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Definir configurações básicas para Striim":::


1. No painel **configurações de cluster do Striim** , escolha o tipo de implantação Striim e o tamanho da máquina virtual.

   |Configuração | Valor | Descrição |
   | ---| ---| ---|
   |Tipo de implantação Striim |Autônomo | Striim pode ser executado em tipos de implantação **autônomos** ou de **cluster** . O modo autônomo implantará o servidor Striim em uma única máquina virtual e você poderá selecionar o tamanho das VMs dependendo do volume de dados. O modo de cluster implantará o servidor Striim em duas ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade e failover automáticos.</br></br> Neste tutorial, você pode selecionar a opção autônomo. Use a VM de tamanho padrão "Standard_F4s". | 
   | Nome do cluster Striim|    <Striim_cluster_Name>|  Nome do cluster Striim.|
   | Striim senha do cluster|   <Striim_cluster_password>|  Senha do cluster.|

   Depois de preencher o formulário, selecione **OK** para continuar.

1. No painel **configurações de acesso do Striim** , configure o **endereço IP público** (escolha os valores padrão), **nome de domínio para Striim**, **senha de administrador** que você gostaria de usar para fazer logon na interface do usuário do Striim. Configure uma VNET e uma sub-rede (escolha os valores padrão). Depois de preencher os detalhes, selecione **OK** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Configurações de acesso do Striim":::

1. O Azure validará a implantação e verificará se tudo está correto; a validação leva alguns minutos para ser concluída. Depois que a validação for concluída, selecione **OK**.
  
1. Por fim, examine os termos de uso e selecione **criar** para criar sua instância do Striim. 

## <a name="configure-the-source-database"></a>Configurar o banco de dados de origem

Nesta seção, você configurará o banco de dados Oracle como a origem para a movimentação.  Você precisará do [Driver do Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para se conectar ao Oracle. Para ler as alterações do banco de dados Oracle de origem, você pode usar as APIs [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ou [XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). O driver do Oracle JDBC deve estar presente no classpath Java do Striim para ler, gravar ou manter dados do banco de dados Oracle.

Baixe o driver [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) no computador local. Você irá instalá-lo no cluster Striim mais tarde.

## <a name="configure-target-database"></a>Configurar banco de dados de destino

Nesta seção, você configurará a conta de API do Cassandra Azure Cosmos DB como o destino para a movimentação de dados.

1. Crie uma [conta de API do Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) usando o portal do Azure.

1. Navegue até o painel de **Data Explorer** em sua conta do cosmos do Azure. Selecione **nova tabela** para criar um novo contêiner. Suponha que você esteja migrando *produtos* e *pedidos* de dados do banco de dados Oracle para Azure Cosmos DB. Crie um novo keyspace chamado **StriimDemo** com um contêiner Orders. Provisione o contêiner com **1000 RUS**(Este exemplo usa 1000 Rus, mas você deve usar a taxa de transferência estimada para sua carga de trabalho) e **/ORDER_ID** como a chave primária. Esses valores serão diferentes dependendo dos dados de origem. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="Criar conta de API do Cassandra":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configurar o fluxo de dados do Oracle para Azure Cosmos DB

1. Agora, vamos voltar ao Striim. Antes de interagir com o Striim, instale o driver do Oracle JDBC que você baixou anteriormente.

1. Navegue até a instância de Striim implantada no portal do Azure. Selecione o botão **conectar** na barra de menus superior e, na guia **SSH** , copie a URL no campo **logon usando a conta local da VM** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Obter a URL do SSH":::

1. Abra uma nova janela de terminal e execute o comando SSH que você copiou do portal do Azure. Este artigo usa o terminal em um MacOS, você pode seguir as instruções semelhantes usando a reutilização ou um cliente SSH diferente em um computador Windows. Quando solicitado, digite **Sim** para continuar e insira a **senha** que você definiu para a máquina virtual na etapa anterior.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Conectar-se à VM Striim":::

1. Agora, abra uma nova guia terminal para copiar o arquivo **ojdbc8. jar** baixado anteriormente. Use o seguinte comando SCP para copiar o arquivo JAR do computador local para a pasta tmp da instância Striim em execução no Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Copie o arquivo JAR do computador do local para Striim":::

1. Em seguida, navegue de volta para a janela em que você realizou o SSH para a instância Striim e faça logon como sudo. Mova o arquivo **ojdbc8. jar** do diretório **/tmp** para o diretório **lib** da sua instância do Striim com os seguintes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Mover o arquivo JAR para a pasta lib":::


1. Na mesma janela do terminal, reinicie o servidor Striim executando os seguintes comandos:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim levará um minuto para ser inicializado. Se você quiser ver o status, execute o seguinte comando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Agora, navegue de volta para o Azure e copie o endereço IP público da VM Striim. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Copiar endereço IP da VM Striim":::

1. Para navegar até a interface do usuário da Web do amStriim, abra uma nova guia em um navegador e copie o IP público seguido por: 9080. Entre usando o nome de usuário do **administrador** , junto com a senha de administrador que você especificou na portal do Azure.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Entrar no Striim":::

1. Agora você chegará às home page do Striim. Há três painéis diferentes – **dashboards**, **aplicativos** e **SourcePreview**. O painel painéis permite que você mova dados em tempo real e visualize-os. O painel aplicativos contém seus pipelines de dados de streaming ou fluxos de dados. No lado direito da página está SourcePreview onde você pode visualizar os dados antes de movê-los.

1. Selecione o painel **aplicativos** , vamos nos concentrar neste painel por enquanto. Há uma variedade de aplicativos de exemplo que você pode usar para aprender sobre o Striim, no entanto, neste artigo, você criará o nosso. Selecione o botão **Adicionar aplicativo** no canto superior direito.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Adicionar o aplicativo Striim":::

1. Há algumas maneiras diferentes de criar aplicativos Striim. Selecione **Iniciar do zero** para este cenário.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="Iniciar o aplicativo do zero":::

1. Dê um nome amigável para seu aplicativo, algo como **oraToCosmosDB** e selecione **salvar**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="Crie um novo aplicativo":::

1. Você chegará ao designer de fluxo, onde poderá arrastar e soltar conectores para criar seus aplicativos de streaming. Digite **Oracle** na barra de pesquisa, arraste e solte a fonte **Oracle CDC** na tela do aplicativo.  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Origem do Oracle CDC":::

1. Insira as propriedades de configuração de origem da sua instância do Oracle. O nome de origem é apenas uma Convenção de nomenclatura para o aplicativo Striim, você pode usar um nome como  **src_onPremOracle**. Insira também outros detalhes, como tipo de adaptador, URL de conexão, nome de usuário, senha, nome da tabela. Selecione **salvar** para continuar.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="Configurar parâmetros de origem":::

1. Agora, clique no ícone de onda do fluxo para conectar a instância de Azure Cosmos DB de destino. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="Conectar-se ao destino":::

1. Antes de configurar o destino, verifique se você adicionou um [certificado raiz Baltimore ao ambiente Java do Striim](/bingmaps/articles/ssl-certificate-validation-for-java-applications#configuring-root-certificates).

1. Insira as propriedades de configuração de sua instância de Azure Cosmos DB de destino e selecione **salvar** para continuar. Aqui estão os principais parâmetros a serem observados:

   * **Adaptador** -use **DatabaseWriter**. Ao gravar em Azure Cosmos DB API do Cassandra, DatabaseWriter é necessário. O driver Cassandra 3.6.0 é fornecido com Striim. Se o DatabaseWriter exceder o número de RUs provisionado no contêiner Cosmos do Azure, o aplicativo falhará.

   * **URL de conexão** -ESPECIFIQUE sua URL de conexão Azure Cosmos DB JDBC. A URL está no formato     `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * Nome de **usuário** – especifique o nome da conta do Azure Cosmos.
   
   * **Senha** -especifique a chave primária da sua conta do Azure Cosmos.

   * **Tabelas** -as tabelas de destino devem ter chaves primárias e chaves primárias não podem ser atualizadas.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="Captura de tela que mostra as propriedades de destino configuráveis.":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="Configurar propriedades de destino":::

1. Agora, vamos continuar e executar o aplicativo Striim. Na barra de menus superior, selecione **criado** e, em seguida, **implantar aplicativo**. Na janela de implantação, você pode especificar se deseja executar determinadas partes do seu aplicativo em partes específicas de sua topologia de implantação. Como estamos executando em uma topologia de implantação simples por meio do Azure, usaremos a opção padrão.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="Implantar o aplicativo":::


1. Agora, vamos Visualizar o fluxo para ver os dados que fluem pelo Striim. Clique no ícone de onda e clique no ícone de olho ao lado dele. Após a implantação, você pode visualizar o fluxo para ver os dados que fluem. Selecione o ícone de **onda** e o **olho** ao lado dele. Selecione o botão **implantado** na barra de menus superior e selecione **Iniciar aplicativo**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="Iniciar o aplicativo":::

1. Usando um leitor **CDC (Change Data Capture)** , o Striim obterá apenas as novas alterações no banco de dados. Se você tiver dados que fluem pelas tabelas de origem, você o verá. No entanto, como essa é uma tabela de exemplo, a origem que não está conectada a nenhum aplicativo. Se você usar um gerador de dados de exemplo, você poderá inserir uma cadeia de eventos em seu Oracle Database.

1. Você verá dados que fluem pela plataforma Striim. O Striim pega todos os metadados associados à sua tabela também, o que é útil para monitorar os dados e certificar-se de que os dados estão no destino correto.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="Configurar o pipeline CDC":::

1. Por fim, vamos entrar no Azure e navegar até sua conta do Azure Cosmos. Atualize o Data Explorer, e você pode ver que os dados chegaram. 

Usando a solução Striim no Azure, você pode migrar dados continuamente para Azure Cosmos DB de várias fontes, como Oracle, Cassandra, MongoDB e várias outras para Azure Cosmos DB. Para saber mais, visite o [site do Striim](https://www.striim.com/), [Baixe uma avaliação gratuita de 30 dias do Striim](https://go2.striim.com/download-free-trial)e para problemas ao configurar o caminho de migração com o Striim, registre uma [solicitação de suporte.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Próximas etapas

* Se você estiver migrando dados para Azure Cosmos DB API do SQL, consulte [como migrar dados para API do Cassandra conta usando o Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Monitorar e depurar seus dados com Azure Cosmos DB métricas](use-metrics.md)