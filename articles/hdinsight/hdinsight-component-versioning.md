---
title: "Componentes e versões do Hadoop – Azure HDInsight | Microsoft Docs"
description: "Conheça os componentes e versões do Hadoop no HDInsight e os níveis de serviço disponíveis na distribuição de nuvem do Hortonworks Data Platform."
keywords: "versões do hadoop, componentes do ecossistema do hadoop, componentes do hadoop, como verificar a versão do hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.openlocfilehash: 412fc8b76d2b5ac45532d3ccb7a2ab7361d53596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes e versões do Hadoop disponíveis com o HDInsight?

Saiba mais sobre as versões e componentes do ecossistema Apache Hadoop no Microsoft Azure HDInsight, bem como os níveis de serviço Standard e Premium. Além disso, saiba como verificar as versões dos componentes do Hadoop no HDInsight. 

Cada versão do HDInsight é uma distribuição de nuvem de uma versão do HDP (Hortonworks Data Platform).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes do Hadoop disponíveis com diferentes versões do HDInsight
O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP e um conjunto de componentes contidos nessa distribuição. A partir de 17 de fevereiro de 2017, versão padrão do cluster usada pelo Azure HDInsight é a 3.5, baseada em HDP 2.5.

As versões do componente associadas às versões do cluster HDInsight são listadas na tabela a seguir. 

> [!NOTE]
> A versão padrão para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar clusters com o .NET SDK com o Azure PowerShell e a CLI do Azure.

| Componente | HDInsight 3.6 (padrão) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2,0 |
| Apache Hadoop e YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive e HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| HBase no Apache |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| O Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| O Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (somente Linux) |1.6.2 + 2.0 (somente Linux) |1.6.0 (somente Linux) |1.5.2 (somente build experimental do Linux) |1.3.1 (somente Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Verificar informações atuais de versão do componente do Hadoop

As versões de componente do ecossistema Hadoop associadas às versões do cluster HDInsight podem ser alteradas com atualizações ao HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para obter detalhes, consulte a [Documentação do Ambari][ambari-docs].

Para clusters do Windows, outra maneira de verificar a versão do componente é fazer logon em um cluster usando a Área de Trabalho Remota e examinar diretamente o conteúdo do diretório C:\apps\dist\.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou posterior. Para obter mais informações, confira [Baixa do Windows no HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Notas de versão

Consulte [Notas de versão do HDInsight](hdinsight-release-notes.md) para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte
A tabela a seguir lista as versões do HDInsight que estão disponíveis atualmente no Portal do Azure. As versões HDP que correspondem a cada versão do HDInsight estão listadas juntamente com as datas de lançamento do produto. As datas de expiração e de desativação de suporte também são fornecidas, quando elas são conhecidas.

> [!NOTE]
> Depois que o suporte para uma versão tiver expirado, ela poderá não estar disponível por meio do Portal clássico do Microsoft Azure. No entanto, as versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando do Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) e o SDK .NET até a data de baixa da versão.
> 
> Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight versão 2.1 e posterior. Eles não estão disponíveis para clusters HDInsight versão 1.6.

| Versão do HDInsight | Versão do HDP | SO da VM | Alta disponibilidade | Data do lançamento | Disponibilidade no Portal do Azure | Data de expiração do suporte | Data de baixa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16 |Sim |4 de abril de 2017 |Sim | | |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16 |Sim |30 de setembro de 2016 |Sim |5 de setembro de 2017 |31 de maio de 2018 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sim |29 de março de 2016 |Sim |29 de dezembro de 2016 |9 de janeiro de 2018 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |Sim |2 de dezembro de 2015 |Sim |27 de junho de 2016 |31 de julho de 2018 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Sim |2 de dezembro de 2015 |Sim |27 de junho de 2016 |31 de julho de 2017 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |Sim |18 de fevereiro de 2015 |Não |1º de março de 2016 |1º de abril de 2017 |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |Sim |24 de junho de 2014 |Não |18 de maio de 2015 |30 de junho de 2016 |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |Sim |11 de fevereiro de 2014 |Não |17 de setembro de 2014 |30 de junho de 2015 |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |Sim |28 de outubro de 2013 |Não |12 de maio de 2014 |31 de maio de 2015 |
| HDInsight 1.6 |HDP 1.1 | |Não |28 de outubro de 2013 |Não |26 de abril de 2014 |31 de maio de 2015 |

## <a name="hdinsight-windows-retirement"></a>Baixa do Windows do HDInsight
A versão 3.3 do Microsoft Azure HDInsight foi a última versão do HDInsight no Windows. A data de baixa do HDInsight no Windows é 31 de julho de 2018. Se você tiver clusters HDInsight no Windows 3.3 ou anterior, você deverá migrar para HDInsight no Linux (HDInsight versão 3.5 ou posterior) antes de 31 de julho de 2018. Migrar para o sistema operacional Linux permite que você retenha a capacidade de criar ou redimensionar os clusters HDInsight. O suporte ao HDInsight versão 3.3 no Windows expirou em 27 de junho de 2016.

Do HDInsight versão 3.4 em diante, a Microsoft lançou o HDInsight apenas no sistema operacional Linux. Como resultado, alguns dos componentes do HDInsight estão disponíveis somente para Linux. Isso inclui Apache Ranger, Kafka, Consulta Interativa, Spark, aplicativos do HDInsight e o Azure Data Lake Store como o sistema de arquivos primário. Versões futuras do HDInsight estarão disponíveis apenas no sistema operacional Linux. Não haverá nenhuma versão futura do HDInsight no Windows. 

## <a name="faqs"></a>Perguntas frequentes

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Qual é a linha do tempo para baixa do HDInsight no Windows?
A data de baixa do HDInsight no Windows é 31 de julho de 2018. Se a data de baixa planejada for diferente para a sua região, você será notificado separadamente. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Qual é o impacto da baixa do HDInsight no Windows para clientes existentes?
Após a baixa do HDInsight no Windows, não é possível criar um novo cluster HDInsight do Windows nem redimensionar um cluster HDInsight do Windows existente. O suporte ao HDInsight versão 3.3 expirou em 27 de junho de 2016. Portanto, não há nenhum suporte ou correções de bug para HDInsight 3.3 ou versões anteriores. Versões futuras do HDInsight estarão disponíveis apenas no sistema operacional Linux. Não haverá nenhuma versão futura do HDInsight no Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Quais versões do HDInsight no Windows são afetadas?
A versão 3.3 do Azure HDInsight é a última versão do HDInsight para Windows. Antes de ocorrer a baixa do HDInsight no Windows, todos os clusters HDInsight do Windows versão 3.3 ou anterior devem ser migrados para o HDInsight no Linux versão 3.5 ou posterior. A migração de seus clusters HDInsight no Linux permite que você retenha a capacidade de criar novos clusters ou redimensionar clusters existentes. 

### <a name="what-do-i-need-to-do"></a>O que preciso fazer?
Migre seus clusters HDInsight do Windows para um cluster HDInsight do Linux com suporte antes de 31 de julho de 2018. Saiba mais no [documento de migração do HDInsight](hdinsight-migrate-from-windows-to-linux.md). Para obter detalhes sobre as versões do Azure HDInsight, consulte a lista de [versões com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Onde posso encontrar o tipo de sistema operacional do cluster?
No Portal do Azure, vá para a página de visão geral do Cluster HDInsight e localize **Tipo de cluster** em **Essentials**. Os tipos de sistema operacional do cluster estão listados nessa página. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Não consigo migrar para um cluster HDInsight do Linux até 31 de julho de 2018. Qual é o impacto no meu cluster HDInsight do Windows?
O cluster HDInsight do Windows é executado como está, mas você não pode criar um novo cluster HDInsight do Windows nem redimensionar um cluster HDInsight do Windows existente. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>O cluster tem uma dependência do .NET. Como fazer para resolver essa dependência no Linux?
Você pode resolver a dependência do cluster Linux usando o [projeto Mono](http://www.mono-project.com/). Essa implementação de software livre do .NET está disponível para clusters HDInsight do Linux. Saiba mais no [documento de migração do HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Sou um novo cliente para HDInsight no Windows. Como criar um cluster HDInsight do Windows?
A partir de 3 de julho de 2017, apenas clientes existentes do HDInsight do Windows podem criar novos clusters HDInsight do Windows. Novos clientes não podem criar um cluster HDInsight do Windows no Portal do Azure usando o PowerShell ou o SDK. É recomendável que novos clientes criem um cluster HDInsight do Linux. Os clientes existentes podem criar novas janelas de clusters HDInsight até a data de baixa do HDInsight no Windows. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Há um impacto no preço associado à migração do HDInsight no Windows para HDInsight no Linux?
Não, o preço é o mesmo para o HDInsight em qualquer um dos sistemas operacionais. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Quais são as vantagens do cliente associadas com a mudança para usar somente o HDInsight no Linux?
* Menor tempo para comercialização para tecnologias de Big Data de software livre por meio do serviço HDInsight
* Uma grande comunidade e ecossistema de suporte
* Capacidade de exercitar desenvolvimento ativo pela comunidade de software livre para o Hadoop e outras tecnologias de Big Data

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight no Linux oferece funcionalidade adicional além do que está disponível no HDInsight no Windows?
Do HDInsight versão 3.4 em diante, a Microsoft lançou o HDInsight apenas no sistema operacional Linux. Como resultado, alguns dos componentes do HDInsight estão disponíveis somente para Linux. Isso inclui Apache Ranger, Kafka, Consulta Interativa, Spark, aplicativos do HDInsight e o Azure Data Lake Store como o sistema de arquivos primário. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões do cluster HDInsight
O Contrato de Nível de Serviço (SLA) é definido em termos de uma _Janela de Suporte_. A janela de suporte é o período de tempo que uma versão do cluster HDInsight tem suporte pelo Atendimento e Suporte ao Cliente da Microsoft. Se a versão tiver uma _Data de Expiração do Suporte_ que já passou, o cluster HDInsight ficará fora da janela de suporte. Para obter mais informações sobre versões com suporte, consulte a lista de [versões de cluster do HDInsight com suporte](hdinsight-migrate-from-windows-to-linux.md). A data de expiração do suporte de uma versão especificada do HDInsight (assim que uma versão X+1 mais nova estiver disponível) é calculada como a mais posterior de:  

* Fórmula 1: adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
* Fórmula 2: adicionar 90 dias à data em que o cluster HDInsight versão X+1 foi disponibilizado no Portal do Azure.

A _data de baixa_ é a data após a qual a versão do cluster não pode ser criada no HDInsight. Desde 31 de julho de 2017, você não pode redimensionar um cluster HDInsight após sua data de baixa. 

> [!NOTE]
> Os clusters HDInsight do Windows (incluindo as versões 2.1, 3.0, 3.1, 3.2 e 3.3) são executados na Família 4 de SOs Convidados do Azure, que usa a versão de 64 bits do Windows Server 2012 R2. A Família de SOs Convidados do Azure versão 4 dá suporte ao .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de versão do Hortonworks associadas a versões do HDInsight

A seção fornece links para notas de versão para as distribuições do Hortonworks Data Platform e componentes do Apache que são usados com o HDInsight.
* O cluster do HDInsight versão 3.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* O cluster do HDInsight versão 3.5 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Versão 3.5 do cluster HDInsight é o cluster Hadoop _padrão_ que é criado no Portal do Azure.
* O cluster HDInsight versão 3.4 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* O cluster HDInsight versão 3.3 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * As [notas de versão do Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) estão disponíveis no site do Apache.
  * As [notas de versão do Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) estão disponíveis no site do Apache.
* O cluster do HDInsight versão 3.2 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.2][hdp-2-2].

  * Notas de versão para componentes específicos do Apache estão disponíveis conforme a seguir: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* O cluster HDInsight versão 3.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Os clusters do HDInsight 3.1 criados antes de 7 de novembro de 2014 são baseados no [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* O cluster do HDInsight versão 3.0 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.0][hdp-2-0-8].
* O cluster do HDInsight versão 2.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.3][hdp-1-3-0].
* O cluster do HDInsight versão 1.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium

O Azure HDInsight oferece ofertas de nuvem de Big Data em duas categorias: _Standard_ e _Premium_. A tabela a seguir lista os recursos que estão disponíveis _somente_ do HDInsight Premium. Recursos não explicitamente descritos na tabela estão disponíveis tanto no HDInsight Standard quanto no Premium.

> [!NOTE]
> A oferta HDInsight Premium está atualmente em versão prévia e está disponível somente para clusters do Linux.

| Recurso do HDInsight Premium | Descrição |
| --- | --- |
| Clusters HDInsight ingressados no domínio |Una clusters HDInsight em domínios do Azure AD (Azure Active Directory) para segurança de nível empresarial. No HDInsight Premium, você pode configurar uma lista de funcionários de sua empresa que podem se autenticar através do Azure AD para fazer logon em um cluster HDInsight. O admin corporativo pode configurar o controle de acesso baseado em função de segurança do Hive utilizando o [Apache Ranger](http://hortonworks.com/apache/ranger/) e restringir o acesso a dados para ser usado apenas tanto quanto necessário. Por fim, o administrador pode auditar os dados acessados por funcionários e as alterações feitas às políticas de controle de acesso, atingindo assim um alto grau de controle de seus recursos corporativos. Para obter mais informações, consulte [Configurar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-configure.md). |

### <a name="cluster-types-supported-in-hdinsight-premium"></a>Tipos de cluster com suporte no HDInsight Premium
A tabela a seguir lista os tipos de cluster com suporte no HDInsight Premium.

| Tipo de cluster | Standard | Premium (Visualização) |
| --- | --- | --- |
| O Hadoop |Sim |Sim (somente HDInsight 3.6) |
| Spark |Sim |Não |
| HBase |Sim |Não |
| Storm |Sim |Não |
| Servidor R |Sim |Não |
| Consulta Interativa |Sim |Não |
| Kafka (Versão prévia) |Sim |Não | 

### <a name="support-for-azure-data-lake-store-in-hdinsight-premium"></a>Suporte para Azure Data Lake Store no HDInsight Premium

Clusters HDInsight Premium não dão suporte ao uso do Azure Data Lake Store como armazenamento primário. No entanto, você pode usar o Azure Data Lake Store como armazenamento de complemento com clusters HDInsight Premium.

### <a name="pricing-and-sla"></a>Preço e SLA
Para obter informações sobre preços e SLA para o HDInsight Premium, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Tamanhos de máquina virtual e configuração de nó de padrão para clusters
As tabelas abaixo listam os tamanhos de VM (máquina virtual) padrão para clusters HDInsight.

> [!IMPORTANT]
> Se você precisa de mais de 32 nós de trabalho em um cluster, você precisa selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
> 
> 

* Todas as regiões com suporte, exceto Sul do Brasil e Oeste do Japão:

  | Tipo de cluster | O Hadoop | HBase | Storm | Spark | Servidor R |
  | --- | --- | --- | --- | --- | --- |
  | Cabeçalho: tamanho padrão da VM |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Cabeçalho: tamanhos de VM recomendados |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Trabalho: tamanho de VM padrão |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabalho: tamanhos de VM recomendados |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper: tamanho de VM padrão | |A3 |A2 | | |
  | Zookeeper: tamanhos de VM recomendados | |A3, A4, A5 |A2, A3, A4 | | |
  | Borda: tamanho padrão da VM | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Borda: tamanho de VM recomendado | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Apenas Sul do Brasil e Oeste do Japão (sem tamanhos v2):

  | Tipo de cluster | O Hadoop | HBase | Storm | Spark | Servidor R |
  | --- | --- | --- | --- | --- | --- |
  | Cabeçalho: tamanho padrão da VM |D3 |D3 |A3 |D12 |D12 |
  | Cabeçalho: tamanhos de VM recomendados |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Trabalho: tamanho de VM padrão |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabalho: tamanhos de VM recomendados |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | Zookeeper: tamanho de VM padrão | |A2 |A2 | | |
  | Zookeeper: tamanhos de VM recomendados | |A2, A3, A4 |A2, A3, A4 | | |
  | Borda: tamanhos padrão da VM | | | | |Windows: D12; Linux: D4 |
  | Borda: tamanhos de VM recomendados | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Supervisor* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Próximas etapas
- [Configuração de cluster para Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhar em Hadoop no HDInsight em um computador com Windows](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
