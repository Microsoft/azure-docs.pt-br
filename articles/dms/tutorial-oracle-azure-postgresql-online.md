---
title: 'Tutorial: Migrar Oracle online para Banco de Dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba como fazer a migração online do Oracle local ou em máquinas virtuais para um Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 76ab2204a81d3cbfb559bfa4591f1f332b1707ee
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388067"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Migrar Oracle para Banco de Dados do Azure para PostgreSQL online usando o DMS (visualização)

> [!IMPORTANT]
> O cenário de migração do **Oracle para Banco de Dados do Azure para PostgreSQL** (em versão prévia no momento) não estará mais disponível após 1º de maio de 2021. Continuaremos a fornecer suporte por meio de ferramentas alternativas (como Ora2pg) e proporcionar a melhor experiência de migração para migrações do Oracle para o PostgreSQL. Para obter as melhores práticas de migração, confira o [Guia de migração do Oracle para Banco de Dados do Azure para PostgreSQL](https://aka.ms/OracletoPGguide). 

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados do Oracle hospedados localmente ou em máquinas virtuais para o [Banco de Dados do Azure para PostgreSQL](../postgresql/index.yml) com um tempo de inatividade mínimo. Em outras palavras, a migração pode ser concluída com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você vai migrar o banco de dados de exemplo **RH** de uma instância local ou máquina virtual do Oracle 11g para o Banco de Dados do Azure para PostgreSQL usando a atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Avaliar o esforço de migração usando a ferramenta ora2pg.
> * Migrar o esquema de exemplo usando a ferramenta ora2pg.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como executar uma migração online do Oracle para o Banco de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixar e instalar o [Oracle 11g Versão 2 (Standard Edition, Standard Edition One ou Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Baixar a amostra do banco de dados de **RH**[aqui](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Baixar e [instalar ora2pg no Windows ou no Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Criar uma instância no Banco de Dados do Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Conectar-se à instância e criar um banco de dados usando a instrução deste [documento](../postgresql/tutorial-design-database-using-azure-portal.md).
* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

  > [!NOTE]
  > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
  >
  > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
  > * Ponto de extremidade de armazenamento
  > * Ponto de extremidade do barramento de serviço
  >
  > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Verifique se as regras do NSG (grupo de segurança de rede) da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor de origem do Oracle, que, por padrão, é a porta TCP 1521.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível de servidor para o Banco de Dados do Azure para PostgreSQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* Habilite o acesso aos bancos de dados do Oracle de origem.

  > [!NOTE]
  > A função do DBA é necessária para que um usuário se conecte à fonte Oracle.

  * Os logs de restauração do arquivo são necessários para sincronização incremental no Serviço de Migração de Banco de Dados do Azure para capturar a mudança de dados. Siga estas etapas para configurar a fonte do Oracle:
    * Entre usando o privilégio SYSDBA executando o seguinte comando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Desligue a instância de banco de dados, executando o comando a seguir.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Aguarde a confirmação `'ORACLE instance shut down'`.

    * Inicie a nova instância e montagem (mas não abra) do banco de dados para habilitar ou desabilitar o arquivamento bu executando o seguinte comando:

      ```
      STARTUP MOUNT;
      ```

      O banco de dados será montado. Aguarde a confirmação “Instância do Oracle Iniciada”.

    * Altere o modo do banco de dados de arquivamento executando o seguinte comando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Abra o banco de dados para as operações normais executando o seguinte comando:

      ```
      ALTER DATABASE OPEN;
      ```

      Você talvez precise reiniciar para que arquivo ARC apareça.

    * Para verificar, execute o seguinte:

      ```
      SELECT log_mode FROM v$database;
      ```

      Você receberá uma resposta `'ARCHIVELOG'`. Se a resposta for `'NOARCHIVELOG'`, o requisito não será atendido.

  * Habilite o log suplementar para replicação usando uma das opções a seguir.

    * **Opção 1**.
      Altere o log suplementar de nível de banco dados para cobrir todas as tabelas com PK e índice exclusivo. A consulta de detecção retornará `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Altere o log suplementar de nível de tabela. Execute somente para as tabelas que têm manipulação de dados e não tem PKs ou índices exclusivos.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opção 2**.
      Altere o log suplementar de nível de banco dados para cobrir todas as tabelas e a consulta de detecção retornará `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Altere o log suplementar de nível de tabela. Siga a lógica abaixo para executar apenas uma instrução para cada tabela.

      Se a tabela tiver uma chave primária:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se a tabela tiver um índice exclusivo:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Caso contrário, execute o seguinte comando:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Para verificar, execute o seguinte:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Você receberá uma resposta `'YES'`.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Avalie o esforço para um Oracle para a migração do Banco de Dados do Azure para PostgreSQL

É recomendável usar o ora2pg para avaliar o esforço necessário para migrar do Oracle para o Banco de Dados do Azure para PostgreSQL. Use a diretiva `ora2pg -t SHOW_REPORT` para criar um relatório listando todos os objetos do Oracle, o custo de migração estimado (em dias de desenvolvedor) e determinados objetos de banco de dados que podem exigir atenção especial como parte da conversão.

A maioria dos clientes gastará um tempo considerável revisando relatório de avaliação e considerando o esforço de conversão automática e manual.

Para configurar e executar ora2pg para criar um relatório de avaliação, consulte a **Pré-migração: Seção de** avaliação do [Oracle para o Guia de Banco de Dados do Azure para PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Um exemplo de um relatório de avaliação ora2pg está disponível para referência [aqui](https://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exportar o esquema do Oracle

É recomendável que você use ora2pg para converter o esquema do Oracle e outros objetos do Oracle (tipos, procedimentos, funções etc.) em um esquema que seja compatível com o Banco de Dados do Azure para PostgreSQL. O ora2pg inclui muitas diretivas para ajudá-lo a definir previamente determinados tipos de dados. Por exemplo, você pode usar a diretiva `DATA_TYPE` para substituir todos os todos os NUMBER(*,0) por bigint em vez de NUMERIC(38).

Você pode executar o ora2pg para exportar cada um dos objetos de banco de dados em arquivos .sql. Em seguida, você pode examinar os arquivos .sql antes de importá-los ao Banco de Dados do Azure para PostgreSQL usando psql ou você pode executar o script .SQL no PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Por exemplo:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Para configurar e executar o ora2pg para conversão de esquema, consulte a seção **Migração: Esquema e dados** do [Oracle para o Guia de Banco de Dados do Azure para PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurar o esquema no Banco de Dados do Azure para PostgreSQL

Você pode optar por converter esquemas de tabela, procedimentos armazenados, pacotes e outros objetos de banco de dados do Oracle para torná-los compatíveis com Postgres usando ora2pg antes de iniciar um pipeline de migração no Serviço de Migração de Banco de Dados do Azure. Confira os links abaixo para saber como trabalhar com ora2pg:

* [Instalar o ora2pg no Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Manual de migração do Oracle para o PostgreSQL do Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

O Serviço de Migração de Banco de Dados do Azure também pode criar o esquema de tabela PostgreSQL. O serviço acessa o esquema de tabela na fonte do Oracle conectada e cria um esquema de tabela compatível no Banco de Dados do Azure para PostgreSQL. Certifique-se de validar e verificar o formato do esquema no Banco de Dados do Azure para PostgreSQL depois que o Serviço de Migração de Banco de Dados do Azure terminar de criar o esquema e mover os dados.

> [!IMPORTANT]
> O Serviço de Migração de Banco de Dados do Azure cria apenas o esquema de tabela; outros objetos de banco de dados, como procedimentos armazenados, pacotes, índices etc. não são criados.

Não deixe de remover a chave estrangeira no banco de dados de destino para a carga total ser executada. Consulte a seção **Migrar o esquema de exemplo** do artigo [aqui](./tutorial-postgresql-azure-postgresql-online.md) para um script que você pode usar para descartar a chave de referência. Use o Serviço de Migração de Banco de Dados do Azure para executar para carga plena e sincronizar.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Quando o esquema de tabela do PostgreSQL já existe

Se você criar um esquema do PostgreSQL usando ferramentas como ora2pg antes de iniciar a movimentação de dados com o Serviço de Migração de Banco de Dados do Azure, mapeie as tabelas de origem para as tabelas de destino Serviço de Migração de Banco de Dados do Azure.

1. Ao criar um novo projeto de migração do Oracle para o Banco de Dados do Azure para PostgreSQL, você será solicitado a selecionar o banco de dados de destino e o esquema de destino na etapa Selecionar esquemas. Preencha o banco de dados de destino e o esquema de destino.

   ![A captura de tela mostra o Mapa para os bancos de dados de destino.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. A tela **Configurações de migração** apresenta uma lista de tabelas na origem do Oracle. O Serviço de Migração de Banco de Dados do Azure tenta fazer a correspondência entre as tabelas de origem e de destino com base no nome da tabela. Se houver várias tabelas de destino correspondentes com diferenças de maiúsculas e minúsculas, você poderá selecionar a tabela de destino para a qual mapear.

    ![A captura de tela mostra as configurações da Migração.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Se precisar mapear os nomes de tabela de origem para tabelas com nomes diferentes, envie um email para [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) e poderemos fornecer um script para automatizar o processo.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Quando o esquema de tabela do PostgreSQL não existe

Se o banco de dados PostgreSQL de destino não contiver nenhuma informação de esquema de tabela, o Serviço de Migração de Banco de Dados do Azure converterá o esquema de origem e o recriará no banco de dados de destino. Lembre-se de que o Serviço de Migração de Banco de Dados do Azure cria apenas o esquema de tabela, e não outros objetos de banco de dados como procedimentos armazenados, pacotes e índices.
Para que o Serviço de Migração de Banco de Dados do Azure crie o esquema para você, verifique se o ambiente de destino inclui um esquema sem tabelas existentes. Se o Serviço de Migração de Banco de Dados do Azure descobrir qualquer tabela, o serviço vai pressupor que o esquema foi criado por uma ferramenta externa, como ora2pg.

> [!IMPORTANT]
> O Serviço de Migração de Banco de Dados do Azure exige que todas as tabelas sejam criadas da mesma maneira, usando o Serviço de Migração de Banco de Dados do Azure ou uma ferramenta como o ora2pg, mas não ambos.

Introdução:

1. Criar um esquema no banco de dados de destino com base nos requisitos do aplicativo. Por padrão, o esquema de tabela do PostgreSQL e os nomes das colunas ficam menos em minúsculas. As colunas e o esquema de tabela do Oracle, por outro lado, ficam em maiúsculas.
2. Na etapa Selecionar esquemas, especifique o banco de dados de destino e o esquema de destino.
3. Com base no esquema que criado no Banco de Dados do Azure para PostgreSQL, o Serviço de Migração de Banco de Dados do Azure usa as seguintes regras de transformação:

    Se o nome do esquema na origem do Oracle corresponder ao que está no Banco de Dados do Azure para PostgreSQL, o Serviço de Migração de Banco de Dados do Azure *criará o esquema de tabela usando o mesmo padrão do destino*.

    Por exemplo:

    | Esquema de origem do Oracle | Esquema do Banco de Dados PostgreSQL de destino | schema.table.column criado pelo DMS |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.HR | "HR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.Hr | *Não é possível mapear usos mistos de maiúsculas e minúsculas |

    *Para criar um esquema misto de tabelas com uso de maiúsculas e minúsculas no PostgreSQL de destino, contate [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com). Podemos fornecer um script para configurar um esquema misto de tabelas com uso de maiúsculas e minúsculas no Banco de dados PostgreSQL de destino.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Escolha uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso à instância ao Oracle de origem e ao Banco de Dados do Azure para instância do PostgreSQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Em **Novo projeto de migração**, especifique um nome de projeto; na caixa de texto **Tipo de servidor de origem**, selecione **Oracle** e, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados do Azure para PostgreSQL**.
5. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

   ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Selecione **Salvar**, observe os requisitos para usar com êxito o Serviço de Migração de Banco de Dados do Azure para executar uma migração online e, em seguida, selecione **Criação e execução de atividade**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

* Na tela **Adicionar Detalhes de Origem**, especifique os detalhes da conexão da instância do Oracle de origem.

  ![Tela Adicionar Detalhes da Origem](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Carregar o driver do Oracle OCI

1. Selecione **Salvar** e, em seguida, na tela **Instalar driver OCI**, entre em sua conta do Oracle e baixe o driver **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (Soma de verificação SHA1: 865082268) a partir [daqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Faça o download do driver para uma pasta compartilhada.

   Verifique se a pasta é compartilhada com o nome de usuário que você especificou com o mínimo de acesso somente leitura. O Serviço de Migração de Banco de Dados do Azure acessa e lê a partir do compartilhamento para carregar o driver OCI no Azure, representando o nome de usuário que você especificar.

   O nome de usuário que você especificar deve ser uma conta de usuário do Windows.

   ![Instalação do driver OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, na tela **Detalhes de destino**, especifique os detalhes de conexão do servidor do Banco de Dados para PostgreSQL de destino, que é a instância previamente provisionada do Banco de Dados do Azure para PostgreSQL na qual o esquema **RH** foi implantado.

    ![Tela de detalhes do destino](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

  A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

     ![Status da atividade – em execução](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Em **Nome do banco de dados**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    O carregamento de dados completo mostrará o status de migração da carga inicial e a sincronização de dados incremental mostrará o status da CDC (Captura de Dados de Alterações).

     ![Status da atividade: carregamento completo concluído](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Status da atividade: sincronização de dados incrementais](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.

   ![Iniciar substituição](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecione **Confirmar** e, em seguida, **Aplicar**.
4. Quando o status de migração do banco de dados mostrar **Concluído**, conecte os aplicativos à nova instância do Banco de Dados do Azure para PostgreSQL.

 > [!NOTE]
 > Por padrão, o PostgreSQL tem o schema.table.column em letras minúsculas, você poderá reverter de letras maiúsculas em minúsculas usando o script na seção **Configurar o esquema no Banco de Dados do Azure para PostgreSQL** anteriormente neste artigo.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, confira o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](../postgresql/overview.md).
