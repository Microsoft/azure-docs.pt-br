---
title: Usar armazenamentos de metadados externos - Azure HDInsight
description: Use armazenamentos de metadados externos com clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: d36c8f1f592bbe714a9e31cad8131523049f29ad
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931365"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

O HDInsight permite que você assuma o controle de seus dados e metadados com armazenamentos de dados externos. Esse recurso está disponível para [Apache Hive metastore](#custom-metastore), o [Apache Oozie metastore](#apache-oozie-metastore)e o [banco de dados Apache Ambari](#custom-ambari-db).

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório de esquema central. O metastore é usado por outras ferramentas de acesso Big Data, como Apache Spark, consulta interativa (LLAP), presto ou Apache Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

![Arquitetura de armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight cria um metastore com cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:

* Sem custo adicional. O HDInsight cria um metastore com cada tipo de cluster sem nenhum custo adicional para você.

* Cada metastore padrão é parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados correspondentes também são excluídos.

* Não é possível compartilhar o metastore padrão com outros clusters.

* O metastore padrão é recomendado apenas para cargas de trabalho simples. Cargas de trabalho que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.

> [!IMPORTANT]
> O metastore padrão fornece um banco de dados SQL do Azure com um **limite de DTU básico de camada 5 (não atualizável)**! Adequado para fins de teste básico. Para cargas de trabalho grandes ou de produção, é recomendável migrar para um metastore externo.

## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:

* Você pode especificar seu próprio Banco de Dados SQL do Azure com o metastore.

* O ciclo de vida do metastore não está vinculado a um ciclo de vida de clusters, para que você possa criar e excluir clusters sem perder metadados. Os metadados, como os esquemas Hive, serão mantidos até mesmo depois de você excluir e recriar o cluster HDInsight.

* Um metastore personalizado permite que você anexe vários clusters e tipos de cluster ao metastore. Por exemplo, um único metastore pode ser compartilhado em clusters Interactive Query, Hive e Spark no HDInsight.

* Você paga pelo custo de um metastore (banco de dados SQL do Azure) de acordo com o nível de desempenho escolhido.

* Você pode expandir o metastore conforme a necessidade.

* O cluster e o metastore externo devem ser hospedados na mesma região.

![Caso de uso do armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Criar e configurar o banco de dados SQL do Azure para o metastore personalizado

Crie ou tenha um banco de dados SQL do Azure existente antes de configurar um metastore do Hive personalizado para um cluster HDInsight.  Para obter mais informações, consulte [início rápido: criar um banco de dados individual no banco de dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Ao criar o cluster, o serviço HDInsight precisa se conectar ao metastore externo e verificar suas credenciais. Configure as regras de firewall do banco de dados SQL do Azure para permitir que os serviços e recursos do Azure acessem o servidor. Habilite essa opção no portal do Azure selecionando **definir Firewall do servidor**. Em seguida, selecione **não** abaixo de **negar acesso à rede pública** e **Sim** abaixo **permitir que os serviços e recursos do Azure acessem este servidor para o** banco de dados SQL do Azure. Para obter mais informações, consulte [criar e gerenciar regras de firewall de IP](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

Pontos de extremidade privados para repositórios SQL só têm suporte nos clusters criados com `outbound` ResourceProviderConnection. Para saber mais, Confira esta [documentação](./hdinsight-private-link.md).

![botão Definir Firewall do servidor](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![permitir acesso aos serviços do Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um banco de dados SQL do Azure criado anteriormente a qualquer momento. Para a criação do cluster por meio do portal, a opção é especificada nas **configurações de metastore > de armazenamento**.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Diretrizes de metastore do Hive

> [!NOTE]
> Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore). Comece com a camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.

* Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dados separado para o metastore em cada cluster. Se você compartilhar um metastore entre vários clusters do HDInsight, isso significa que os clusters usam os mesmos metadados e arquivos de dados do usuário subjacentes.

* Faça backup de seu metastore personalizado periodicamente. O Banco de Dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../azure-sql/database/automated-backups-overview.md).

* Localize seu metastore e o cluster HDInsight na mesma região. Essa configuração fornecerá o melhor desempenho e os menores encargos de saída de rede.

* Monitore seu metastore para obter desempenho e disponibilidade usando as ferramentas de monitoramento do banco de dados SQL do Azure ou logs de Azure Monitor.

* Quando uma nova versão mais recente do Azure HDInsight é criada em um banco de dados de metastore personalizado existente, o sistema atualiza o esquema do metastore. A atualização é irreversível sem restaurar o banco de dados do backup.

* Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Diferentes versões do Hive usam diferentes esquemas de banco de dados metastore. Por exemplo, você não pode compartilhar um metastore em clusters do hive 2,1 e com versão do hive 3,1.

* No HDInsight 4.0, o Spark e o Hive usam catálogos independentes para acessar tabelas SparkSQL ou Hive. Uma tabela criada pelo Spark reside no catálogo do Spark. Uma tabela criada pelo Hive reside no catálogo do Hive. Esse comportamento é diferente no HDInsight 3.6, onde o Hive e o Spark compartilham o catálogo comum. A integração do Hive e do Spark no HDInsight 4.0 depende do Hive Warehouse Connector (HWC). O HWC funciona como uma ponte entre o Spark e o Hive. [Saiba mais sobre o conector do depósito do hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* No HDInsight 4.0, se quiser compartilhar o metastore entre o Hive e o Spark, você poderá alterar a propriedade metastore.catalog.default para hive no cluster do Spark. Você pode encontrar essa propriedade no spark2-hive-site-override do Ambari Advanced. É importante entender que o compartilhamento do metastore só funciona para tabelas externas do Hive. Isso não funcionará se você tiver tabelas internas/gerenciadas do Hive ou tabelas ACID.  

## <a name="apache-oozie-metastore"></a>Metastore do Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre fluxos de trabalho. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore fornece acesso aos dados do trabalho Oozie depois que você exclui o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>BD Ambari personalizado

Para usar seu próprio banco de dados externo com o Apache Ambari no HDInsight, consulte [banco de dados personalizado do Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Próximas etapas

* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)