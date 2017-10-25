---
title: Use o Apache Phoenix e o SQLLine com o HBase no Azure HDInsight | Microsoft Docs
description: "Saiba como usar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar o SQLLine no seu computador para se conectar a um cluster HBase no HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: 41bfef95059b7c5343d952b4fe372e3baae6f9d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Usar o Apache Phoenix com clusters do HBase baseados em Linux no HDInsight
Saiba como usar o [Apache Phoenix](http://phoenix.apache.org/) no Azure HDInsight e como usar o SQLLine. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para conhecer a gramática do Phoenix, consulte [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Para obter informações sobre a versão do Phoenix no HDInsight, consulte [Novidades nas versões de cluster Hadoop fornecidas pelo HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Usar o SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder usar o SQLLine, você deve ter os seguintes itens:

* **Um cluster HBase no HDInsight**. Para criar um, veja [Introdução ao Apache HBase no HDInsight](./hdinsight-hbase-tutorial-get-started.md).

Ao se conectar a um cluster HBase, é necessário se conectar a uma das VMs do ZooKeeper. Cada cluster HDInsight tem três VMs do ZooKeeper.

**Para descobrir o nome do host do ZooKeeper**

1. Abra o Ambari em **https://\<nome do cluster\>.azurehdinsight.net**.
2. Para se conectar, insira o nome de usuário HTTP (cluster) e a senha.
3. No menu à esquerda, selecione **ZooKeeper**. Três instâncias do **Servidor do ZooKeeper** serão listadas.
4. Clique em uma das instâncias do **Servidor do ZooKeeper** listadas. No painel **Resumo**, encontre o **Nome do host**. Ele é semelhante a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usar SQLLine**

1. Conecte-se ao cluster usando o SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. No SSH, use os seguintes comandos para executar o SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Execute os seguintes comandos para criar uma tabela do HBase e insira alguns dados:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para obter mais informações, consulte o [Manual do SQLLine](http://sqlline.sourceforge.net/#manual) e [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Apache Phoenix no HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].
  O HBase é um banco de dados NoSQL de código aberto Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
* [Provisionar clusters HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet].
  Com a integração da rede virtual, os clusters HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
* [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-replication.md). Saiba como configurar a replicação do HBase em dois datacenters Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
