---
title: Migrar dados para Azure Cosmos DB conta da API do SQL usando Striim
description: Saiba como usar o Striim para migrar os dados de um Oracle Database para uma conta Azure Cosmos DB API do SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 136853182e353ad5cd71981db5935fc3babe162e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339582"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrar dados para Azure Cosmos DB conta da API do SQL usando Striim
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
A imagem Striim no Azure Marketplace oferece movimentação de dados contínua em tempo real de data warehouses e bancos de dados para o Azure. Ao mover os dados, você pode executar a desnormalização em linha, transformação de dados, Habilitar análise em tempo real e cenários de relatórios de dados. É fácil começar a usar o Striim para mover continuamente dados corporativos para Azure Cosmos DB API do SQL. O Azure fornece uma oferta do Marketplace que facilita a implantação de Striim e a migração de dados para Azure Cosmos DB. 

Este artigo mostra como usar o Striim para migrar dados de um **Oracle Database** para uma **conta Azure Cosmos DB API do SQL**.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Um banco de dados Oracle em execução local com algum dado.

## <a name="deploy-the-striim-marketplace-solution"></a>Implantar a solução Striim Marketplace

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso** e pesquise **Striim** no Azure Marketplace. Selecione a primeira opção e **criar**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Localizar item do Marketplace do Striim":::

1. Em seguida, insira as propriedades de configuração da instância Striim. O ambiente Striim é implantado em uma máquina virtual. No painel **básico** , insira o **nome de usuário da VM** , senha da **VM** (essa senha é usada para ssh na VM). Selecione a **assinatura** , o **grupo de recursos** e os detalhes do **local** onde você gostaria de implantar o Striim. Depois de concluído, selecione **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Definir configurações básicas para Striim":::

1. No painel **configurações de cluster do Striim** , escolha o tipo de implantação Striim e o tamanho da máquina virtual.

   |Setting | Valor | Descrição |
   | ---| ---| ---|
   |Tipo de implantação Striim |Autônomo | Striim pode ser executado em tipos de implantação **autônomos** ou de **cluster** . O modo autônomo implantará o servidor Striim em uma única máquina virtual e você poderá selecionar o tamanho das VMs dependendo do volume de dados. O modo de cluster implantará o servidor Striim em duas ou mais VMs com o tamanho selecionado. Ambientes de cluster com mais de 2 nós oferecem alta disponibilidade e failover automáticos.</br></br> Neste tutorial, você pode selecionar a opção autônomo. Use a VM de tamanho padrão "Standard_F4s".  | 
   | Nome do cluster Striim|    <Striim_cluster_Name>|  Nome do cluster Striim.|
   | Striim senha do cluster|   <Striim_cluster_password>|  Senha do cluster.|

   Depois de preencher o formulário, selecione **OK** para continuar.

1. No painel **configurações de acesso do Striim** , configure o **endereço IP público** (escolha os valores padrão), **nome de domínio para Striim** , **senha de administrador** que você gostaria de usar para fazer logon na interface do usuário do Striim. Configure uma VNET e uma sub-rede (escolha os valores padrão). Depois de preencher os detalhes, selecione **OK** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Configurações de acesso do Striim":::

1. O Azure validará a implantação e verificará se tudo está correto; a validação leva alguns minutos para ser concluída. Depois que a validação for concluída, selecione **OK**.
  
1. Por fim, examine os termos de uso e selecione **criar** para criar sua instância do Striim. 

## <a name="configure-the-source-database"></a>Configurar o banco de dados de origem 

Nesta seção, você configurará o banco de dados Oracle como a origem para a movimentação.  Você precisará do [Driver do Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para se conectar ao Oracle. Para ler as alterações do banco de dados Oracle de origem, você pode usar as APIs [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) ou [XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). O driver do Oracle JDBC deve estar presente no classpath Java do Striim para ler, gravar ou manter dados do banco de dados Oracle.

Baixe o driver [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) no computador local. Você irá instalá-lo no cluster Striim mais tarde.

## <a name="configure-the-target-database"></a>Configurar o banco de dados de destino

Nesta seção, você configurará a Azure Cosmos DB conta da API do SQL como o destino para a movimentação de dados.

1. Crie uma [conta de API do SQL Azure Cosmos DB](create-cosmosdb-resources-portal.md) usando o portal do Azure.

1. Navegue até o painel de **Data Explorer** em sua conta do cosmos do Azure. Selecione **novo contêiner** para criar um novo contêiner. Suponha que você esteja migrando *produtos* e *pedidos* de dados do banco de dados Oracle para Azure Cosmos DB. Crie um novo banco de dados chamado **StriimDemo** com um contêiner chamado **Orders**. Provisione o contêiner com **1000 RUS** (Este exemplo usa 1000 Rus, mas você deve usar a taxa de transferência estimada para sua carga de trabalho) e **/ORDER_ID** como a chave de partição. Esses valores serão diferentes dependendo dos dados de origem. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Criar uma conta da API do SQL":::

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

1. Agora você chegará às home page do Striim. Há três painéis diferentes – **dashboards** , **aplicativos** e **SourcePreview**. O painel painéis permite que você mova dados em tempo real e visualize-os. O painel aplicativos contém seus pipelines de dados de streaming ou fluxos de dados. No lado direito da página está SourcePreview onde você pode visualizar os dados antes de movê-los.

1. Selecione o painel **aplicativos** , vamos nos concentrar neste painel por enquanto. Há uma variedade de aplicativos de exemplo que você pode usar para aprender sobre o Striim, no entanto, neste artigo, você criará o nosso. Selecione o botão **Adicionar aplicativo** no canto superior direito.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Adicionar o aplicativo Striim":::

1. Há algumas maneiras diferentes de criar aplicativos Striim. Selecione **Iniciar com modelo** para iniciar com um modelo existente.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Iniciar o aplicativo com o modelo":::

1. No campo **Pesquisar modelos** , digite "Cosmos" e selecione **destino: Azure Cosmos DB** e, em seguida, selecione **Oracle CDC para Azure Cosmos DB**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Selecione Oracle CDC para Cosmos DB":::

1. Na próxima página, nomeie seu aplicativo. Você pode fornecer um nome como **oraToCosmosDB** e, em seguida, selecionar **salvar**.

1. Em seguida, insira a configuração de origem da instância do Oracle de origem. Insira um valor para o **nome de origem**. O nome de origem é apenas uma Convenção de nomenclatura para o aplicativo Striim, você pode usar algo como **src_onPremOracle**. Insira valores para o restante dos parâmetros de origem **URL** , **nome de usuário** , **senha** , escolha **LogMiner** como o leitor para ler dados do Oracle. Selecione **Avançar** para continuar.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Configurar parâmetros de origem":::

1. O Striim verificará seu ambiente e garantirá que ele possa se conectar à instância do Oracle de origem, ter os privilégios certos e que o CDC tenha sido configurado corretamente. Depois que todos os valores forem validados, selecione **Avançar**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Validar parâmetros de origem":::

1. Selecione as tabelas do banco de dados Oracle que você gostaria de migrar. Por exemplo, vamos escolher a tabela Orders e selecionar **Next**. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Selecionar tabelas de origem":::

1. Depois de selecionar a tabela de origem, você pode fazer operações mais complicadas, como mapeamento e filtragem. Nesse caso, você criará apenas uma réplica da tabela de origem no Azure Cosmos DB. Portanto, selecione **Avançar** para configurar o destino

1. Agora, vamos configurar o destino:

   * **Nome de destino** – forneça um nome amigável para o destino. 
   * **Entrada de** -na lista suspensa, selecione o fluxo de entrada do que você criou na configuração do Oracle de origem. 
   * **Coleções** – Insira as propriedades de configuração de Azure Cosmos DB de destino. A sintaxe de coleções é **SourceSchema. SourceTable, TargetDatabase. TargetContainer**. Neste exemplo, o valor seria "SYSTEM. ORDERs, StriimDemo. Orders ". 
   * **AccessKey** -a PrimaryKey da sua conta do Azure Cosmos.
   * **ServiceEndpoint** – o URI de sua conta do Azure Cosmos, que pode ser encontrado na seção **chaves** do portal do Azure. 

   Selecione **salvar** e **Avançar**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Configurar parâmetros de destino":::


1. Em seguida, você chegará ao designer de fluxo, no qual você pode arrastar e soltar os conectores do box para criar seus aplicativos de streaming. Você não fará nenhuma modificação no fluxo neste ponto. Então, vá em frente e implante o aplicativo selecionando o botão **implantar aplicativo** .
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Implantar o aplicativo":::

1. Na janela de implantação, você pode especificar se deseja executar determinadas partes do seu aplicativo em partes específicas de sua topologia de implantação. Como estamos executando em uma topologia de implantação simples por meio do Azure, usaremos a opção padrão.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Usar a opção padrão":::

1. Após a implantação, você pode visualizar o fluxo para ver os dados que fluem. Selecione o ícone de **onda** e o olho ao lado dele. Selecione o botão **implantado** na barra de menus superior e selecione **Iniciar aplicativo**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Iniciar o aplicativo":::

1. Usando um leitor **CDC (Change Data Capture)** , o Striim obterá apenas as novas alterações no banco de dados. Se você tiver dados que fluem pelas tabelas de origem, você o verá. No entanto, como esta é uma tabela de demonstração, a origem não está conectada a nenhum aplicativo. Se você usar um gerador de dados de exemplo, você poderá inserir uma cadeia de eventos em seu Oracle Database.

1. Você verá dados que fluem pela plataforma Striim. O Striim pega todos os metadados associados à sua tabela também, o que é útil para monitorar os dados e certificar-se de que os dados estão no destino correto.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Configurar pipeline CDC":::

1. Por fim, vamos entrar no Azure e navegar até sua conta do Azure Cosmos. Atualize o Data Explorer, e você pode ver que os dados chegaram.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Validar dados migrados no Azure":::

Usando a solução Striim no Azure, você pode migrar dados continuamente para Azure Cosmos DB de várias fontes, como Oracle, Cassandra, MongoDB e várias outras para Azure Cosmos DB. Para saber mais, visite o [site do Striim](https://www.striim.com/), [Baixe uma avaliação gratuita de 30 dias do Striim](https://go2.striim.com/download-free-trial)e para problemas ao configurar o caminho de migração com o Striim, registre uma [solicitação de suporte.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Próximas etapas

* Se você estiver migrando dados para Azure Cosmos DB API do SQL, consulte [como migrar dados para API do Cassandra conta usando o Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitorar e depurar seus dados com Azure Cosmos DB métricas](use-metrics.md)