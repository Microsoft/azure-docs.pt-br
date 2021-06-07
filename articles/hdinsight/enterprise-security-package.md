---
title: Enterprise Security Package para o Azure HDInsight
description: Conheça os componentes e as versões do Enterprise Security Package no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 442c21c92ef2124ebef1889f99a8d2b806c8ce10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943307"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Enterprise Security Package para o Azure HDInsight

O Enterprise Security é um pacote opcional que pode ser adicionado ao cluster HDInsight como parte do fluxo de trabalho de criação de cluster. O Pacote de Segurança Enterprise oferece suporte a:

* Integração com o Active Directory para autenticação.

    No passado, você criou clusters HDInsight com o usuário administrador local e o usuário SSH local. O usuário administrador local podia acessar todos os arquivos, pastas, tabelas e colunas.  Com o Enterprise Security Package, você habilita o controle de acesso baseado em função do Azure integrando o HDInsight ao seu Azure Active Directory Domain Services.

    Para obter mais informações, consulte:

    * [Uma introdução à segurança do Apache Hadoop com clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md)

    * [Planejar clusters do Apache Hadoop ingressados no domínio do Azure no HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Configurar ambiente de área restrita ingressado no domínio](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

    * [Configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorização pra dados

  * Integração com o Apache Ranger para autorização de Hive, Spark SQL e filas de Yarn.
  * Você pode configurar o controle de acesso em arquivos e pastas.

    Para obter mais informações, consulte [Configurar políticas de Apache Hive no HDInsight ingressado no domínio](./domain-joined/apache-domain-joined-run-hive.md)

* Exiba os logs de auditoria para monitorar acessos e as políticas configuradas.

## <a name="supported-cluster-types"></a>Tipos de cluster com suporte

Atualmente, apenas os tipos de cluster a seguir oferecem suporte ao Pacote de Segurança Enterprise:

* Hadoop (somente HDInsight 3.6)
* Spark
* Kafka
* HBase
* Consulta Interativa

## <a name="support-for-azure-data-lake-storage"></a>Suporte ao Azure Data Lake Storage

O Enterprise Security Package oferece suporte ao uso do Azure Data Lake Storage como o armazenamento primário e o armazenamento de complemento.

## <a name="pricing-and-service-level-agreement-sla"></a>Preço e contrato de nível de serviço (SLA)

Para obter informações sobre preços e SLA para o Pacote de Segurança Enterprise, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximas etapas

* [Configuração de cluster para o Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Trabalhe no Apache Hadoop no HDInsight a partir de um PC Windows](hdinsight-hadoop-windows-tools.md)
* [Notas sobre a versão do Hortonworks associadas a versões do Azure HDInsight](./hortonworks-release-notes.md)
* [Componentes do Apache no HDInsight](./hdinsight-component-versioning.md)