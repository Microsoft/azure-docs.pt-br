---
title: "Notas de versão arquivadas – Componentes do Hadoop no Azure HDInsight | Microsoft Docs"
description: "Notas de versão arquivadas para versões mais antigas de componentes do Hadoop para Azure HDInsight."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 04278aac85171601b5801b0890d14a9076060444
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-archive-for-hadoop-components-on-azure-hdinsight"></a>Notas de versão (arquivadas) dos componentes do Hadoop no Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **antigas** do Azure HDInsight. Para obter informações sobre versões mais recentes, consulte as [Notas de versão do HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).



## <a name="notes-for-08302016-release-of-hdinsight"></a>Notas da versão de 30/08/2016 do HDInsight
Os números completos da versão para clusters HDInsight baseados em Linux implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP | Build do Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Os números completos da versão para clusters HDInsight baseados em Windows implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.1033.2559206 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |


## <a name="08172016---release-of-r-server-on-hdinsight"></a>17/08/2016 – Versão do R Server no HDInsight
* R Server 8.0.5 – Uma versão basicamente de correção de bug. Consulte as [Notas de Versão do R Server](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) para obter mais informações.
* Pacote do AzureML no nó de borda – [esse pacote R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permite que os modelos R sejam publicados e consumidos como um serviço Web do Azure ML.  Confira a seção ["Operacionalizar um modelo"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) do nosso artigo ["Visão geral do R Server no HDInsight"](hdinsight-hadoop-r-server-overview.md) para obter mais informações.
* Dependências do Linux dos [100 pacotes R mais populares](https://github.com/metacran/cranlogs) – essas dependências de pacote do Linux agora são pré-instalados.
* Opção para usar o repositório CRAN ao adicionar pacotes R aos nós de dados. Para obter mais informações, confira ["Introdução ao uso do R Server no HDInsight"](hdinsight-hadoop-r-server-get-started.md).
* Foi melhorada a confiabilidade de provisionamento do R Server quando os clusters são criados.

## <a name="notes-for-08012016-release-of-hdinsight"></a>Notas da versão de 01/08/2016 do HDInsight
Os números completos da versão para clusters HDInsight baseados em Linux implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP | Build do Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8028416 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8028416 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8053402 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Os números completos da versão para clusters HDInsight baseados em Windows implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.1005.2488842 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1005.2488842 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1005.2488842 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1005.2488842 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1005.2488842 |2.3 |2.3.3.1-25 |

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Spark, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Alterações em clusters de HDInsight 3.4 |Os valores padrão para as configurações de Hive a seguir são alterados para melhorar o desempenho <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul> |O Barramento de |Todos |N/D |
| As correções a seguir estão incluídas nesta versão |HIVE-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933 |O Barramento de |Todos |N/D |

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notas da versão de 14/07/2016 do HDInsight
Os números completos da versão para clusters HDInsight baseados em Linux implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP | Build do Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7932505 |2.2 |2.2.9.1-11 |2.2.1.12-2 |
| 3.3 |3.3.1000.0.7932505 |2.3 |2.3.3.1-18 |2.2.1.12-2 |
| 3.4 |3.4.1000.0.7933003 |2.4 |2.4.2.0 |2.2.1.12-2 |

Os números completos da versão para clusters HDInsight baseados em Windows implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.989.2441725 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.989.2441725 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.989.2441725 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.989.2441725 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.989.2441725 |2.3 |2.3.3.1-21 |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notas da versão de 07/07/2016 do HDInsight
Os números completos da versão para clusters HDInsight baseados em Linux implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 3.2 |3.2.1000.0.7864996 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.1000.0.7864996 |2.3 |2.3.3.1-18 |
| 3.4 |3.4.1000.0.7861906 |2.4 |2.4.2.0 |

Os números completos da versão para clusters HDInsight baseados em Windows implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.977.2413853 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.977.2413853 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.977.2413853 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.977.2413853 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.977.2413853 |2.3 |2.3.3.1-21 |

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Spark, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| [Ferramentas do HDInsight para IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) |O plug-in IntelliJ IDEA para clusters HDInsight Spark agora está integrado ao Kit de ferramentas do Azure para IntelliJ. Ele dá suporte ao SDK do Azure v2.9.1, SDKs mais recentes do Java e inclui todos os recursos do Plug-in autônomo do HDInsight para IntelliJ. |Ferramentas |Spark |N/D |
| [Ferramentas do HDInsight para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) |O Kit de ferramentas do Azure para Eclipse agora dá suporte a clusters HDInsight Spark. Ela habilita os seguintes recursos: <ul><li>Crie e escreva um aplicativo Spark facilmente em Scala e Java com suporte à criação de primeira classe para IntelliSense, autoformatação, verificação de erro etc.</li><li>Teste o aplicativo Spark localmente.</li><li>Envie trabalhos ao cluster HDInsight Spark e recupere os resultados.</li><li>Faça logon no Azure e acesse todos os clusters Spark associados às suas assinaturas do Azure.</li><li>Navegue por todos os recursos de armazenamento associados do seu cluster HDInsight Spark.</li></ul> |Ferramentas |Spark |N/D |

A partir desta versão, alteramos a política de aplicação de patch do SO convidado para clusters HDInsight baseados em Linux. O objetivo da nova política é reduzir significativamente o número de reinicializações devido à aplicação de patch. A nova política corrige VMs (máquinas virtuais) em clusters Linux todas as segundas-feiras ou quintas-feiras, começando às 0:00 UTC de maneira irregular entre nós em qualquer cluster determinado. No entanto, qualquer determinada VM será reinicializada somente, no máximo, uma vez a cada 30 dias devido à aplicação de patch do SO convidado. Além disso, a primeira reinicialização para um cluster recém-criado não ocorrerá antes de 30 dias a contar da data de criação do cluster.

> [!NOTE]
> Essas alterações se aplicarão somente aos clusters recém-criados iguais ou superiores a esta versão de lançamento.
>
>

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notas da versão de 06/06/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

| HDP | Versão do HDI | Versão do Spark | Número de build do Ambari | Número de build do HDP |
| --- | --- | --- | --- | --- |
| 2.3 |3.3.1000.0.7702215 |1.5.2 |2.2.1.8-2 |2.3.3.1-18 |
| 2.4 |3.4.1000.0.7702224 |1.6.1 |2.2.1.8-2 |2.4.2.0 |

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Spark, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Spark no HDInsight está disponível de forma geral |Esta versão traz melhorias de disponibilidade, escalabilidade e produtividade para o Apache Spark de software livre no HDInsight. <ul><li>SLA líder do setor com 99,9% de disponibilidade, o que o torna adequado para cargas de trabalho corporativas exigentes.</li><li>Camada de armazenamento escalonável usando o Azure Data Lake Store.</li><li>Ferramentas de produtividade para todas as fases de desenvolvimento e a exploração de dados. Os notebooks Jupyter com kernel Spark personalizado permitem a exploração de dados interativa, a integração a painéis de BI como Power BI, Tableau e Qlik é boa para o compartilhamento de dados rápido e relatórios contínuos, o plug-in IntelliJ é uma opção confiável para o desenvolvimento e depuração de artefato de código de longo prazo.</li></ul> |O Barramento de |Spark |N/D |
| Ferramentas do HDInsight para IntelliJ |Este é um plug-in IntelliJ IDEA para clusters HDInsight Spark. Ela habilita os seguintes recursos:<ul><li>Crie e escreva um aplicativo Spark facilmente em Scala e Java com suporte à criação de primeira classe para IntelliSense, autoformatação, verificação de erro etc.</li><li>Teste o aplicativo Spark localmente.</li><li>Envie trabalhos ao cluster HDInsight Spark e recupere os resultados.</li><li>Faça logon no Azure e acesse todos os clusters Spark associados às suas assinaturas do Azure.</li><li>Navegue por todos os recursos de armazenamento associados do seu cluster HDInsight Spark.</li><li>Navegue por todos os históricos e informações de trabalhos do seu cluster HDInsight Spark.</li><li>Depure os trabalhos Spark remotamente pelo computador desktop.</li></ul> |Ferramentas |Spark |N/D |

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notas da versão de 13/05/2016 do HDInsight 3.1
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.922.2266903  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.922.2266903  (HDP 2.2.9.1-11)
* HDInsight (Windows)        3.3.0.922.2266903  (HDP 2.3.3.1-18)
* HDInsight    (Linux)            3.2.1000.0.7565644   (HDP    2.2.9.1-11)
* HDInsight (Linux)            3.3.1000.0.7565644   (HDP 2.3.3.1-18)
* HDInsight (Linux)            3.4.1000.0.7548380   (HDP 2.4.2.0)

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Spark, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Atualização de versão do Spark e outras correções de bug |Esta versão atualiza a versão do Spark no cluster HDInsight para 1.6.1 e corrige outros bugs |O Barramento de |Spark |N/D |

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notas da versão de 11/04/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.889.2191206 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.889.2191206 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.889.2191206  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.889.2191206  (HDP 2.2.9.1-10)
* HDInsight (Windows)        3.3.0.889.2191206  (HDP 2.3.3.1-16 -inalterado)
* HDInsight    (Linux)            3.2.1000.0.7339916   (HDP 2.2.9.1-10)
* HDInsight (Linux)            3.3.1000.0.7339916   (HDP 2.3.3.1-16)
* HDInsight (Linux)            3.4.1000.0.7338911   (HDP 2.4.1.1-3)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Problemas de atualização de metastore personalizado do HDI 3.4 |A criação de cluster falhará se você tiver usado um metastore personalizado, que foi usado anteriormente em uma versão inferior de outro cluster HDInsight. Isso ocorreu devido a um erro de script de atualização que agora foi corrigido |Criação do cluster |Todos |N/D |
| Recuperação de pane do Livy |Fornece resiliência de status do trabalho para qualquer trabalho enviado por meio do Livy |Confiabilidade |Spark no Linux |N/D |
| HA de conteúdo do Jupyter |Fornece a capacidade de salvar o conteúdo do Jupyter Notebook na conta de armazenamento associada ao cluster e carregar esse conteúdo da conta. Para saber mais, confira [Kernels disponíveis para notebooks Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). |Blocos de anotações |Spark no Linux |N/D  |
| Remoção de hiveContext de Jupyter Notebooks |Use a mágica `%%sql` no lugar da mágica `%%hive`. SqlContext é equivalente a hiveContext. Para saber mais, confira [Kernels disponíveis para notebooks Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md) |Blocos de anotações |Clusters do Spark no Linux |N/D |
| Substituição de versões mais antigas do Spark |A versão mais antiga Spark 1.3.1 foi removida do serviço em 31/5 |O Barramento de |Clusters do Spark no Windows |N/D |

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notas da versão de 29/03/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.875.2159884  (HDP 2.1.15.0-2374 - unchanged)
* HDInsight (Windows)       3.2.7.875.2159884  (HDP 2.2.9.1-7 – inalterado)
* HDInsight (Windows)        3.3.0.875.2159884  (HDP 2.3.3.1-16)
* HDInsight    (Linux)            3.2.1000.0.7193255   (HDP    2.2.9.1-8 - inalterado)
* HDInsight (Linux)            3.3.1000.0.7193255   (HDP 2.3.3.1-7 - inalterado)
* HDInsight (Linux)            3.4.1000.0.7195842   (HDP 2.4.1.0-327)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versão do HDInsight 3.4 adicionada e versões atualizadas do HDP para todos os clusters do HDInsight |Com esta versão, adicionamos o HDInsight v3.4 (baseado no HDP 2.4) e também atualizamos outras versões do HDP. As notas de versão do HDP 2.4 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) e mais informações sobre versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). |O Barramento de |Todos os clusters do Linux |N/D |
| HDInsight Premium |O HDInsight agora está disponível em duas categorias - Standard e Premium. O HDInsight Premium está atualmente em Preview e disponível apenas para os clusters Hadoop e Spark no Linux. Para saber mais, clique [aqui](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). |O Barramento de |Hadoop e Spark no Linux |N/D |
| Servidor R da Microsoft |O HDInsight Premium fornece o Servidor R da Microsoft, que pode ser incluído nos clusters Hadoop e Spark no Linux. Para obter mais informações, consulte [Visão geral do R Server no HDInsight](hdinsight-hadoop-r-server-overview.md). |O Barramento de |Hadoop e Spark no Linux |N/D |
| Spark 1.6.0 |Os clusters do HDInsight 3.4 agora incluem o Spark 1.6.0 |O Barramento de |Clusters do Spark no Linux |N/D |
| Aprimoramentos do Bloco de notas Jupyter |Os blocos de notas Jupyter disponíveis com os clusters do Spark agora fornecem kernels adicionais do Spark. Eles também incluem aprimoramentos, como o uso de %%magic, a visualização automática e a integração com bibliotecas de visualização do Python (como matplotlib). Para saber mais, confira [Kernels disponíveis para notebooks Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). |O Barramento de |Clusters do Spark no Linux |N/D |

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notas da versão de 22/03/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.875.2159884  (HDP 2.1.15.0-2374 - unchanged)
* HDInsight (Windows)       3.2.7.875.2159884  (HDP 2.2.9.1-7 – inalterado)
* HDInsight (Windows)        3.3.0.875.2159884  (HDP 2.3.3.1-16)
* HDInsight    (Linux)            3.2.1000.0.7193255   (HDP    2.2.9.1-8 - inalterado)
* HDInsight (Linux)            3.3.1000.0.7193255   (HDP 2.3.3.1-7 - inalterado)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters de HDInsight |Com essa versão, atualizamos as versões do HDInsight para todos os clusters HDInsight |O Barramento de |Todos |N/D |

## <a name="notes-for-03102016-release-of-hdinsight"></a>Notas da versão de 10/03/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.859.2123216 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.859.2123216 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.859.2123216  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.859.2123216  (HDP 2.2.9.1-7)
* HDInsight (Windows)        3.3.0.859.2123216  (HDP 2.3.3.1-5 - inalterado)
* HDInsight    (Linux)            3.2.1000.7076817   (HDP    2.2.9.1-8)
* HDInsight (Linux)            3.3.1000.7076817   (HDP 2.3.3.1-7)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters de HDInsight |Com essa versão, atualizamos as versões do HDInsight para todos os clusters HDInsight |O Barramento de |Todos |N/D |

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notas da versão de 27/01/2016 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.817.2028315 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.817.2028315 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.817.2028315  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.817.2028315  (HDP 2.2.9.1-1)
* HDInsight (Windows)        3.3.0.817.2028315  (HDP 2.3.3.1-5 - inalterado)
* HDInsight    (Linux)            3.2.1000.4072335   (HDP    2.2.9.1-1)
* HDInsight (Linux)            3.3.1000.4072335   (HDP 2.3.3.1-1)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters de HDInsight |Com essa versão, atualizamos as versões do HDInsight para todos os clusters HDInsight |O Barramento de |Todos |N/D |

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notas da versão de 02/12/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.763.1931434 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.763.1931434 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.763.1931434  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.763.1931434  (HDP 2.2.7.1-34 - inalterado)
* HDInsight (Windows)        3.3.1000.0           (HDP 2.3.3.1-5)
* HDInsight    (Linux)            3.2.1000.0.6392801 (HDP    2.2.7.1-34 - inalterado)
* HDInsight (Linux)            3.3.1000.0           (HDP 2.3.3.0-3039)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versão do HDInsight 3.3 adicionada e versões atualizadas do HDP para todos os clusters do HDInsight |Com esta versão, adicionamos o HDInsight v3.3 (baseado no HDP 2.3) e também atualizamos outras versões do HDP. As notas de versão do HDP 2.3 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) e mais informações sobre versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). |O Barramento de |Todos |N/D |

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notas da versão de 30/11/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.757.1923908 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.757.1923908  (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.757.1923908  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.757.1923908  (HDP 2.2.7.1-34)
* HDInsight    (Linux)            3.2.1000.0.6392801 (HDP    2.2.7.1-34)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters HDInsight e versões do HDP para clusters HDInsight 3.2 (Windows e Linux) |Com esta versão, as versões do HDInsight e do HDP foram atualizadas |O Barramento de |Todos |N/D |

## <a name="notes-for-10272015-release-of-hdinsight"></a>Notas da versão de 27/10/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight    (Windows)         2.1.10.726.1866228 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight    (Windows)         3.0.6.726.1866228  (HDP 2.0.13.0-2117 - inalterado)
* HDInsight    (Windows)         3.1.4.726.1866228  (HDP 2.1.15.0-2374 - inalterado)
* HDInsight    (Windows)        3.2.7.726.1866228  (HDP 2.2.7.1-33)
* HDInsight    (Linux)            3.2.1000.0.6035701 (HDP    2.2.7.1-33)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters HDInsight (Windows e Linux) |Com esta versão, as versões do HDInsight e do HDP foram atualizadas |O Barramento de |Todos |N/D |
| Jupyter corrigido para clusters Spark do Windows com clusters de letras maiúsculas |Os clusters com nomes DNS especificados em letras maiúsculas tinham problemas com os blocos de anotações Jupyter devido a uma verificação de solicitação de origem. A correção foi alterar o nome DNS da configuração do Jupyter para letras minúsculas. |O Barramento de |HDInsight Spark (Windows) |N/D |

## <a name="notes-for-10202015-release-of-hdinsight"></a>Notas da versão de 20/10/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.716.1846990 (Windows)    (HDP 1.3.12.0-01795 – inalterado)
* HDInsight     3.0.6.716.1846990 (Windows)      (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.4.716.1846990 (Windows)      (HDP 2.1.16.0-2374)
* HDInsight        3.2.7.716.1846990 (Windows)      (HDP 2.2.7.1-0004)
* HDInsight        3.2.1000.0.5930166 (Linux)        (HDP 2.2.7.1-0004)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versão do HDP padrão alterado para HDP 2.2 |A versão padrão para clusters HDInsight do Windows foi alterada para HDP 2.2. O HDInsight versão 3.2 (HDP 2.2) está disponível desde fevereiro de 2015. Essa alteração só inverte a versão padrão do cluster quando uma seleção explícita não é feita durante o provisionamento de cluster usando o portal do Azure, cmdlets do PowerShell ou o SDK. |O Barramento de |Todos |N/D |
| Alterações no formato de nome de VM para a implantação de vários HDInsight em clusters Linux em uma única rede virtual |O suporte para a implantação de vários clusters HDInsight Linux em uma única rede virtual está sendo adicionado nesta versão. Como parte da atualização, o formato de nomes de máquina virtual do cluster foi alterado de headnode\*, workernode\* e zookeepernode\* para hn\*, wn\* e zk\*, respectivamente. Não é uma prática recomendada formar uma dependência direta do formato de nomes de máquina virtual, já que isso está sujeito a alterações. Use "hostname -f" no computador local ou nas APIs Ambari para determinar a lista de hosts e o mapeamento de componentes para hosts. Você pode encontrar mais informações em [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) e [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). |O Barramento de |Clusters HDInsight no Linux |N/D |
| Alterações de configuração |Para clusters HDInsight 3.1, agora as configurações a seguir estão habilitadas:  <ul><li>tez.yarn.ats.enabled e yarn.log.server.url. Isso permite que o Servidor de Linha do Tempo do Aplicativo e o servidor de Log possam servir logs.</li></ul>Para clusters HDInsight 3.2, as seguintes configurações foram modificadas: <ul><li>mapreduce.fileoutputcommitter.algorithm.version foi definida como 2. Isso permite o uso da versão V2 do FileOutputCommitter.</li></ul> |O Barramento de |Todos |N/D |

## <a name="notes-for-09092015-release-of-hdinsight"></a>Notas da versão de 09/09/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.675.1768697 (HDP 1.3.12.0-01795 – inalterado)
* HDInsight     3.0.6.675.1768697  (HDP 2.0.13.0-2117 – inalterado)
* HDInsight     3.1.4.675.1768697  (HDP 2.1.15.0-2334 – inalterado)
* HDInsight        3.2.6.675.1768697  (HDP 2.2.6.1-0012 - inalterado)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters de HDInsight |Com esta versão, as versões do HDInsight foram atualizadas |O Barramento de |Todos |N/D |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas da versão de 31/07/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 – inalterado)
* HDInsight     3.0.6.640.1695824  (HDP 2.0.13.0-2117 – inalterado)
* HDInsight     3.1.4.640.1695824  (HDP 2.1.15.0-2334 – inalterado)
* HDInsight        3.2.6.640.1695824  (HDP 2.2.6.1-0012 - inalterado)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Corrigir fluxo de trabalho de recriação de imagens de nó de cluster do Spark |Correção de um bug que estava fazendo com que os nós de cluster do Spark não se recuperassem depois de refazer a imagem |O Barramento de |Spark |N/D |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas da versão de 31/07/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.635.1684502 (HDP 1.3.12.0-01795 – inalterado)
* HDInsight     3.0.6.635.1684502  (HDP 2.0.13.0-2117 – inalterado)
* HDInsight     3.1.4.635.1684502  (HDP 2.1.15.0-2334 – inalterado)
* HDInsight        3.2.6.635.1684502  (HDP 2.2.6.1-0012 - inalterado)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Versões atualizadas do HDInsight para todos os clusters de HDInsight |Com esta versão, as versões do HDInsight foram atualizadas |O Barramento de |Todos |N/D |

## <a name="notes-for-07072015-release-of-hdinsight"></a>Notas da versão de 07/07/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.610.1630216    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.610.1630216    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.4.610.1630216    (HDP 2.1.15.0-2334 - inalterado)
* HDInsight        3.2.4.610.1630216    (HDP 2.2.6.1-0012)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

| Title | Descrição | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo, Hadoop, HBase ou Storm) | JIRA (se aplicável) |
| --- | --- | --- | --- | --- |
| Atualizadas versões do HDP para clusters do HDInsight 3.2 |Com esta versão, o HDInsight 3.2 implanta o HDP 2.2.6.1-0012 |O Barramento de |Todos |N/D |

## <a name="notes-for-06262015-release-of-hdinsight"></a>Notas da versão de 26/06/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.601.1610731    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.601.1610731    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.4.601.1610731    (HDP 2.1.15.0-2334 - inalterado)
* HDInsight        3.2.4.601.1610731    (HDP 2.2.6.1-0011)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Atualizadas versões do HDP para clusters do HDInsight 3.2</td>
<td>Com esta versão, o HDInsight 3.2 implanta o HDP 2.2.6.1</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notas da versão de 18/6/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.596.1601657    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.596.1601657    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.4.596.1601657    (HDP 2.1.15.0-2334)
* HDInsight        3.2.4.596.1601657    (HDP 2.2.6.1-0002)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Portas HTTPS adicionais abertas</td>
<td>O serviço de nuvem agora abre cinco portas, de 8001 a 8005, no cluster. Por exemplo, em https://<clustername>.azurehdinsight.net:8001/. Solicitações para essas URLs são autenticadas usando o mesmo mecanismo básico de senha de autenticação que a porta 443. Essas portas são vinculadas à mesma porta no headnode ativo. Ações de script podem ser usadas para fazer serviços de cliente escutarem nessas portas no headnode e serem roteados para fora do cluster.</td>
<td>Serviço de Nuvem</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Problema intermitente de ordem aleatória do MapReduce para HDInsight 3.2</td>
<td>Correção de uma condição de corrida intermitente rara na ordem aleatória do MapReduce em clusters grandes, resultando em ocasionais falhas de tarefas. Confira <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a> para obter mais informações.</td>
<td>Núcleo do Hadoop</td>
<td>Todos</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>
<tr>
<td>Migração para a última versão do SDK do Azure para Java 2.2 para HDInsight 3.2</td>
<td>Realizada migração para a versão mais recente do SDK do Azure para Java usado pelo driver do WASB. O SDK mais recente tem algumas correções e suas notas de versão estão disponíveis em https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Núcleo do Hadoop</td>
<td>Todos</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>
<tr>
<td>Migração para o HDP 2.1.15 para clusters do HDInsight 3.1</td>
<td>As notas da Hortonworks para a versão estão disponíveis <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">aqui</a>.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notas da versão de 04/06/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.583.1575584    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.583.1575584    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.583.1575584    (HDP 2.1.12.1-0003 - inalterado)
* HDInsight        3.2.4.583.1575584    (HDP 2.2.6.1-1)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correção para o erro 502 de gateway incorreto para clusters Storm</td>
<td>Esta versão corrige um bug que afeta a API de envio do trabalho que fazia com que o site ficasse inativo após uma reinicialização.</td>
<td>Serviço</td>
<td>Storm</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notas da versão de 01/06/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.577.1563827    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.577.1563827    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.577.1563827    (HDP 2.1.12.1-0003 - inalterado))
* HDInsight        3.2.4.577.1563827    (HDP 2.2.6.0-2800 - inalterado)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correções de vários bugs</td>
<td>Esta versão corrige bugs relacionados ao provisionamento de cluster.</td>
<td>O Barramento de</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notas da versão de 27/05/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight        3.2.4.570.1554102    (HDP 2.2.6.0-2800)
* Outras versões do cluster e o SDK não são implantados como parte desta versão.

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Atualização do HDP 2.2</td>
<td>Esta versão do HDInsight 3.2 contém o HDP 2.2.6 e apresenta várias correções de bugs importantes para o HDInsight. As notas de versão completas estão disponíveis em <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">Notas de versão do HDP 2.2.6</a>.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>
<tr>
<td>Alteração da configuração padrão de memória de contêiner Yarn</td>
<td>Nesta atualização, a memória disponível padrão para contêineres YARN (yarn.nodemanager.resource.memory-mb e yarn.scheduler.maximum-allocation-mb), iniciada pelo Gerenciador de Nó, é aumentada para 5.632 MB. Anteriormente, isso foi reduzido para 4.608 MB, mas, com base em várias execuções de trabalho, o novo valor deve oferecer melhor confiabilidade e desempenho para a maioria dos trabalhos, portanto, é um melhor padrão. Como de costume, se você tiver uma dependência crítica dessa configuração de memória, defina-a explicitamente durante a criação do cluster.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>
<tr>
<td>Paridade de configuração padrão para clusters HBase e Storm</td>
<td>Esta atualização restaura clusters Hbase e Storm para usar os mesmos valores de configurações YARN que os clusters do Hadoop. Isso é feito para fins de paridade entre todos os tipos de cluster.</td>
<td>HDP</td>
<td>HBase, Storm</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notas da versão de 20/05/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.564.1542093    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.564.1542093    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.564.1542093    (HDP 2.1.12.1-0003)
* HDInsight        3.2.4.564.1542093    (HDP 2.2.4.6-2)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Suporte a EventHub do SCP.NET</td>
<td>Os pacotes de cluster atualizados para HDInsight Storm trazem novos recursos para SCP.NET. Agora, você terá acesso a novas APIs no construtor de topologias que facilitam o uso de EventHubSpout ou Spouts Java. Você deve atualizar o SDK de cliente SCP.NET para funcionar com os novos clusters, pois os contratos foram atualizados. Para obter detalhes sobre as novas APIs, uso e notas de versão (incluindo correções de bugs), veja o arquivo Leiame incluído no pacote NuGet do SCP.NET.</td>
<td>Ferramentas do VS</td>
<td>Clusters do Storm HDInsight 3.2</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualização de driver JDBC</td>
<td>Foi atualizado o driver para a versão com suporte do SQL Server no sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notas da versão de 27/04/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.537.1486660    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.537.1486660    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.537.1486660    (HDP 2.1.12.0-2329 - inalterado)
* HDInsight        3.2.3.537.1486660    (HDP 2.2.2.2-4)
* SDK            1.5.8

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Corrigir a dependência de DLL</td>
<td>Remove a dependência de HDInsight na estrutura de teste de unidade.</td>
<td>.</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Correção de bug para condição de corrida</td>
<td>Uma solicitação Criar cluster agora aguarda que a solicitação PUT seja aceita antes de realizar a sondagem do status</td>
<td>.</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notas da versão de 14/04/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.521.1453250    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.521.1453250    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.521.1453250    (HDP 2.1.12.0-2329 - inalterado)
* HDInsight        3.2.3.525.1459730    (HDP 2.2.2.2-2)
* SDK            1.5.6

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correções de bug Tez</td>
<td>Correções para Apache TEZ 2214 e TEZ 1923 estão incluídas nesta versão do HDI 3.2. Estas são necessárias para determinadas consultas Hive em Tez que requerem a movimentação de uma quantidade significativa de dados.
</td>
<td>HDP</td>
<td>O Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notas da versão de 06/04/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.521.1453250    (HDP 1.3.12.0-01795 - inalterado)
* HDInsight     3.0.6.521.1453250    (HDP 2.0.13.0-2117 - inalterado)
* HDInsight     3.1.3.521.1453250    (HDP 2.1.12.0-2329 - inalterado)
* HDInsight        3.2.3.521.1453250    (HDP 2.2.2.2-1)
* SDK            1.5.6

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>SDK 1.5.6 do .NET do HDInsight</td>
<td>Atualizações para remover algumas classes internas para HDInsight no Linux.</td>
<td>.</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Biblioteca de Avro 1.5.6</td>
<td>Adição de <b>KnownTypeAttribute</b> para o método <b>GetAllKnownTypes</b>. Corrigida NullReferenceException quando um tipo é null para o método GetAllKnownTypes.</td>
<td>.</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Correções de bug</td>
<td>Correções de bug diversas para o serviço</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notas da versão de 01/04/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.513.1431705    (HDP 1.3.12.0-01795)
* HDInsight     3.0.6.513.1431705    (HDP 2.0.13.0-2117)
* HDInsight     3.1.3.513.1431705    (HDP 2.1.12.0-2329)
* HDInsight        3.2.3.513.1431705    (HDP 2.2.2.1-2600)
* SDK            1.5.5

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Capacidade de habilitar/desabilitar as credenciais da área de trabalho remota em clusters do Windows via SDK do .NET</td>
<td>Suporte programático para habilitar ou desabilitar as credenciais RDP em clusters do Windows.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Capacidade de habilitar credenciais de área de trabalho remota em clusters enquanto eles estão sendo provisionados</td>
<td>Suporte programático para habilitar as credenciais de área de trabalho remota enquanto o cluster está sendo criado. Isso remove o processo de duas etapas para provisionar primeiro o cluster e, em seguida, habilitar a área de trabalho remota.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Python atualizado para 2.7.8</td>
<td>Python atualizado em Clusters HDInsight para Python 2.7.8, que contém algumas correções importantes de segurança para versões 2.1, 3.0, 3.1 e 3.2 do HDInsight</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Alteração de configuração do YARN</td>
<td>Configuração YARN yarn.resourcemanager.max-completed-applications alterada para 1.000 para todos os tipos de clusterpar HDInsight versões 3.1 e 3.2. Esse valor controla apenas a lista de aplicativos concluídos na interface de usuário YARN. Para obter informações sobre os aplicativos que foram enviados antes da lista de aplicativos mostrada na interface do usuário, você pode ir diretamente para o Servidor de Histórico.</td>
<td>YARN</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Redimensionamento de nós em um cluster HBase</td>
<td>Clusters HBase permitem redimensionamento de nós (aumento e redução) para as versões 3.1 e 3.2 do HDInsight</td>
<td>O Barramento de</td>
<td>hbase</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualização do JDBC</td>
<td>O driver JDBC do SQL é atualizado para a versão sqljdbc_4.0.2206.100 para o HDInsight versão 3.2. Essa versão contém aprimoramentos de segurança importantes.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualização da configuração da JVM</td>
<td>Configuração networkaddress.cache.ttl da JVM atualizada para 300 segundos do valor padrão de -1 para HDInsight versões 3.1 e 3.2. Esse valor de configuração controla a política de caching para pesquisas de nome bem-sucedidas por meio do serviço de nome. Isso corrige um bug relacionado ao aumento e redução de clusters HBase.</td>
<td>O Barramento de</td>
<td>hbase</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualização para SDK de Armazenamento do Azure para Java 2.0</td>
<td>O HDInsight versão 3.2 é atualizado para usar a versão mais recente do SDK do armazenamento do Azure para Java. Ela contém várias correções de bug importantes em relação à versão 0.6.0 atual.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualizado para o código-fonte WASB do Apache</td>
<td>O HDInsight versão 3.2 agora usa a versão mais recente de código para o driver de sistema de arquivos WASB do Apache Hadoop. Com essa alteração, o driver WASB agora é empacotado como um jar separado. Isso é apenas uma alteração de empacotamento e não contém quaisquer alterações ao comportamento do driver WASB. O nome deste arquivo JAR é hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualizações de nome de arquivo JAR no HDInsight 3.2</td>
<td>Esta atualização para HDInsight versão 3.2 contém várias correções de bugs e alguns jars internos, empacotados como parte do HDP, foram atualizados. Esses arquivos JAR são internos ao pacote HDP e não para uso direto por aplicativos cliente. Aplicativos devem empacotar sua própria versão dos JARs para que uma atualização para os JARs internos HDP não interrompa os aplicativos cliente.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notas da versão de 03/03/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.488.1375841    (HDP 1.3.9.0-01351 - inalterado)
* HDInsight     3.0.6.488.1375841    (HDP 2.0.9.0-2097 -  inalterado)
* HDInsight     3.1.3.488.1375841    (HDP 2.1.10.0-2290 - inalterado)
* HDInsight        3.2.3.488.1375841    (HDP-2.2.10.0-2340 - inalterado)
* SDK            1.5.0                (inalterado)

Esta versão contém a atualização a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA</th>
</tr>
<tr>
<td>Aprimoramentos de confiabilidade</td>
<td>Fizemos correções que permitem melhor escalabilidade do serviço com o aumento de carga relacionado à criação de cluster.</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notas da versão de 18/02/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.471.1342507    (HDP 1.3.9.0-01351 - inalterado)
* HDInsight     3.0.6.471.1342507    (HDP 2.0.9.0-2097 -  inalterado)
* HDInsight     3.1.3.471.1342507    (HDP 2.1.10.0-2290 - inalterado)
* HDInsight        3.2.3.471.1342507    (HDP-2.2.10.0-2340)
* SDK            1.5.0

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Clusters do HDInsight 3.2</td>
<td>O Hadoop 2.6/HDP2.2 está disponível com clusters do HDInsight 3.2. Ele contém atualizações importantes para todos os componentes de código-fonte aberto. Para obter mais informações, veja Novidades no HDInsight e as <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">Notas de versão do HDP 2.2.0.0</a>.</td>
<td>Software livre</td>
<td>Todos</td>
<td>N/D </td>
</tr>
<tr>
<td>HDInsight no Linux (Visualização)</td>
<td>Clusters podem ser implantados em execução no Ubuntu Linux. Para saber mais, confira Introdução ao HDInsight no Linux.</td>
<td>O Barramento de</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Disponibilidade geral do Storm</td>
<td>Clusters do Apache tempestade estão geralmente disponíveis. Para saber mais, veja Introdução ao uso do Storm no HDInsight.</td>
<td>O Barramento de</td>
<td>Storm</td>
<td>N/D</td>
</tr>
<tr>
<td>Tamanhos de máquina virtual</td>
<td>HDInsight do Azure está disponível em mais tipos e tamanhos de máquina virtual. Os clusters do HDInsight podem utilizar tamanhos de A2 a A7 criados para fins gerais; os nós da Série D têm SSDs (unidades de estado sólido) e processadores 60% mais rápidos; nos tamanhos A8 e A9, há suporte para InfiniBand para rede rápida.</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Dimensionamento de cluster</td>
<td>Você pode alterar o número de nós de dados para um cluster HDInsight em execução sem precisar excluí-lo ou recriá-lo. Atualmente, somente os tipos de cluster Hadoop Query e Apache Storm têm essa capacidade, mas o Apache HBase logo deverá ter também. Para saber mais, veja Gerenciar clusters HDInsight.</td>
<td>O Barramento de</td>
<td>Hadoop, Storm</td>
<td>N/D</td>
</tr>
<tr>
<td>Ferramentas do Visual Studio</td>
<td>Além de ferramentas completas para o Apache Storm, as ferramentas para Apache Hive no Visual Studio foram atualizadas para incluir o preenchimento de declaração, validação local e suporte aprimorado de depuração. Para saber mais, veja Introdução às ferramentas do Hadoop HDInsight para o Visual Studio.</td>
<td>Ferramentas</td>
<td>O Hadoop</td>
<td>N/D </td>
</tr>
<td>Conector de Hadoop para BD Cosmos do Azure</td>
<td>Com o conector de Hadoop para BD Cosmos do Azure, você pode executar agregações complexas, análise e manipulações de seus documentos JSON sem esquema armazenados nas coleções de BD Cosmos do Azure ou entre contas de banco de dados. Para saber mais e obter um tutorial, veja Executar trabalhos do Hadoop usando o BD Cosmos do Azure e o HDInsight.</td>
<td>O Barramento de</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Correções de bug</td>
<td>Fizemos várias correções de bug secundárias para serviços HDInsight. Não se espera nenhuma alteração no comportamento voltado ao cliente.</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notas da versão de 06/02/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.463.1325367    (HDP 1.3.9.0-01351 - inalterado)
* HDInsight     3.0.6.463.1325367    (HDP 2.0.9.0-2097 -  inalterado)
* HDInsight     3.1.2.463.1325367    (HDP 2.1.10.0-2290)
* SDK            N/D

Esta versão contém as atualizações a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correções de bug</td>
<td>Fizemos várias correções de bug secundárias para serviços HDInsight. Não se espera nenhuma alteração no comportamento voltado ao cliente.</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualização de manutenção HDP 2.1</td>
<td>O HDInsight 3.1 é atualizado para implantar o HDP 2.1.10.0. Para obter mais informações, confira <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">Notas de versão do HDP-2.1.10</a>. </td>
<td>Software livre</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Atualizações binárias HDP</td>
<td>Existem alguns arquivos JAR no HBase para os quais os nomes de arquivo foram atualizados. Esses arquivos JAR são usados internamente pelo HBase, portanto, não se espera que os clientes tenham uma dependência nos nomes desses arquivos JAR. Estão incluídos:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Software livre</td>
<td>HBase</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notas da versão de 29/01/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.455.1309616    (HDP 1.3.9.0-01351 - inalterado)
* HDInsight     3.0.6.455.1309616    (HDP 2.0.9.0-2097 -  inalterado)
* HDInsight     3.1.2.455.1309616    (HDP 2.1.9.0-2196 -  inalterado)
* SDK            N/D

Esta versão contém a atualização a seguir:

<table border="1">

<tr>
<th>Title</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo de cluster (por exemplo, Hadoop, HBase ou Storm)</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correções de bug</td>
<td>Fizemos algumas correções de bugs importantes que melhoram a confiabilidade dos Clusters HDInsight durante as atualizações do Azure.</td>
<td>O Barramento de</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notas da versão de 05/01/2015 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.420.1246118    (HDP 1.3.9.0-01351 - inalterado)
* HDInsight     3.0.6.420.1246118    (HDP 2.0.9.0-2097 - inalterado)
* HDInsight     3.1.2.420.1246118    (HDP 2.1.9.0-2196 - inalterado)

Esta versão contém as atualizações a seguir:

<table border="1">

<tr>
<th>Title</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Exemplos para análise de tendência do Twitter e recomendações de vídeos baseadas no Mahout</td>
<td><p>Nesta versão, o console de consulta do HDInsight tem dois exemplos adicionais:</p>

<p><b>Análise de Tendências do Twitter</b><br>
APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares. Neste tutorial, Aprenda a usar o Hive para obter uma lista dos usuários do Twitter que enviaram a maioria dos tweets que contêm uma palavra específica. </p>

<p><b>Recomendação de filme do Mahout</b><br>
O Apache Mahout é uma biblioteca de machine learning de Apache Hadoop. O Mahout contém algoritmos para processamento de dados (como filtragem, classificação e clustering). Neste tutorial, você utilizará um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.</p></td>
<td>Console de consulta</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Mude para o valor padrão para configuração de Hive: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Essa configuração de tamanho aplica-se a associações de mapa convertidas automaticamente. O valor representa a soma dos tamanhos das tabelas que podem ser convertidas em hashmaps que cabem na memória. Em uma versão anterior, esse valor aumentava do valor padrão de 10 MB para 128 MB. No entanto, o novo valor de 128 MB estava causando falhas nos trabalhos devido à falta de memória. Esta versão reverte o valor padrão para 10 MB. Os clientes ainda podem optar por substituir esse valor durante a criação do cluster, considerando suas consultas e tamanhos de tabela. Para obter mais informações sobre essa configuração e como substituí-la, confira <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimize Auto Join Conversion</a> (Otimizar conversão de junção automática) na documentação da Hortonworks. </p></td>
<td>Hive</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notas da versão de 23/12/2014 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.10.420.1246783    (HDP version inalterado)
* HDInsight     3.0.6.420.1246783    (HDP version inalterado)
* HDInsight     3.1.1.420.1246783    (HDP version inalterado)

Esta versão contém a atualização a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Falhas intermitentes de criação de Cluster causadas por excesso de carga</td>
<td><p>Algoritmo aprimorado para baixar os pacotes HDP durante a criação do cluster permite a manipulação mais robusta de falhas causadas por excesso de carga.</p></td>
<td>O Barramento de</td>
<td>Hadoop, Hbase, Storm</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notas da versão de 18/12/2014 do HDInsight
Esta versão contém a atualização de componentes a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Disponibilidade geral de personalização de cluster</a></td>
<td><p>A personalização oferece a capacidade de personalizar os clusters de HDInsight do Azure com projetos disponíveis por meio do ecossistema do Apache Hadoop. Com esse novo recurso, agora você pode testar e implantar projetos de Hadoop no HDInsight do Azure. Isso é habilitado por meio do recurso **Ação de Script**, que pode modificar clusters Hadoop de modos arbitrários usando scripts personalizados. Esse recurso de personalização está disponível em todos os tipos de clusters do HDInsight, incluindo Hadoop, HBase e Storm. Para demonstrar o poder dessa funcionalidade, documentamos o processo para instalar os populares módulos <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a> e <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a>. Esta versão também adiciona a funcionalidade para os clientes especificarem suas ações de script personalizadas por meio do Portal do Azure, fornece diretrizes e práticas recomendadas de como criar ações de script personalizadas usando métodos auxiliares e fornece diretrizes sobre como testar a ação de script. </p></td>
<td>Disponibilidade geral dos recursos</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notas da versão de 21/11/2014 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight     2.1.9.406.1221105    (HDP 1.3.9.0-01351)
* HDInsight     3.0.5.406.1221105    (HDP 2.0.9.0-2097)
* HDInsight     3.1.1.406.1221105    (HDP 2.1.9.0-2196)
* SDK do HDInsight N/D

Esta versão contém as atualizações de componentes a seguir:

<table border="1">
<tr>
<th>Title</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>
<tr>
<td>Correção de bug: erro intermitente ao adicionar grande número de partições a uma tabela na DDL do Hive </td>
<td><p>Se existir um erro de conexão intermitente com o banco de dados do metastore do Hive ao adicionar diversas partições a uma tabela Hive, a DDL do Hive poderá falhar. A instrução a seguir será vista no log de erros do Hive se essa falha ocorrer: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, HBase</td>
<td>HIVE-482 (Este é um JIRA interno de modo que não é possível fazer um orçamento externamente. Mencionado aqui para referência.)</td>
</tr>
<tr>
<td>Correção de bug: interrupção de funcionamento ocasional no console de consulta do HDInsight</td>
<td>Quando isso acontece, a seguinte instrução pode ser vista no log do WebHCat para o trabalho do inicializador do WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Não foi possível carregar arquivo de histórico {url wasb para o arquivo de histórico}"</p></td>
<td>WebHCat</td>
<td>O Hadoop</td>
<td>HIVE-482 (Este é um JIRA interno de modo que não é possível fazer um orçamento externamente. Mencionado aqui para referência.)</td>
</tr>
<tr>
<td>Correção de bug: pico ocasional na latência das consultas do HBase</td>
<td>Se isso acontecer, os usuários notarão um pico ocasional de 3 segundos na latências das consultas do HBase. </td>
<td>Gateway do cluster HDInsight</td>
<td>HBase</td>
<td>N/D</td>
</tr>
<tr>
<td>Mudanças de nomes dos arquivos JAR HDP</td>
<td>Para a versão em cluster do HDI 3.0, há algumas mudanças nos arquivos JAR internos instalados pelo HDP. jetty-6.1.26.jar foi substituído por jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar foi substituído pelo jetty-util-6.1.26.hwx.jar. Essas mudanças se aplicam aos projetos do Hadoop, Mahout, WebHCat e Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-11212014-release-of-hdinsight"></a>Notas da versão de 21/11/2014 do HDInsight
Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1.9.382.1169709 (sem alteração desde 14/11/2014)
* HDInsight 3.0.5.382.1169709 (sem alteração desde 14/11/2014)
* HDInsight 3.1.1.382.1169709 (sem alteração desde 14/11/2014)
* SDK do HDInsight 1.4.0

Esta versão contém as atualizações de componentes a seguir:

<table border="1">
<tr><th>Title</th><th>Descrição</th><th>Componente</th><th>Tipo de cluster</th><th>JIRA (se aplicável)</th></tr>
<tr>
<td>Acesso a logs de aplicativos</td>
<td>Capacidade para enumerar de modo programático aplicativos que foram executados em seus clusters e para baixar logs relevantes de aplicativos ou de contêineres específicos para ajudar a depurar aplicativos problemáticos.</td>
<td>.</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Capacidade para especificar o nome da região no IHdInsightClient.DeleteCluster </td>
<td>O SDK do Azure HDInsight fornece a capacidade de especificar um nome de região ao usar o **DeleteCluster**. Isso ajuda a desbloquear clientes com dois recursos de mesmo nome em regiões diferentes que não conseguiam excluir nenhum deles.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>ClusterDetails.DeploymentId</td>
<td>O objeto **ClusterDetails** agora retorna um campo **DeploymentID** que representa um identificador exclusivo para o cluster. É garantida a exclusividade entre tentativas de criação de clusters com os mesmos nomes.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notas da versão de 14/11/2014 do HDInsight

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Esta versão contém os novos recursos, atualizações de componentes e correções de bug a seguir.

<table border="1">
<tr><th>Title</th><th>Descrição</th><th>Componente</th><th>Tipo de cluster</th><th>JIRA (se aplicável)</th></tr>
<tr>
<td>Ação de script (visualização)</td>
<td>Visualização do recurso de personalização de cluster, que habilita a modificação dos clusters Hadoop de maneiras arbitrárias pelo uso de scripts personalizados. Com esse recurso, os usuários podem experimentar e implantar projetos disponíveis no ecossistema do Apache Hadoop nos clusters do Azure HDInsight. O recurso de personalização está disponível em todos os tipos de clusters do HDInsight, incluindo Hadoop, HBase e Storm.</td>
<td>New recurso</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Trabalhos pré-compilados para sites do Azure e análise de logs de armazenamento</td>
<td>O Console de Consulta do HDInsight tem uma galeria de introdução que dá suporte a soluções que funcionam com seus dados ou com dados de exemplo.
<p>**Soluções que funcionam com seus dados**:<br>
Criamos trabalhos para alguns dos cenários de análise de dados mais comuns para fornecer um ponto de partida para você criar as suas próprias soluções. Você pode usar seus próprios dados com o trabalho para ver como funciona. Em seguida, quando estiver pronto, use o que aprendeu para criar uma solução modelada segundo o trabalho pré-compilado.</p>
<p>**Soluções que funcionam com dados de exemplo**:<br>
Aprenda como trabalhar com o HDInsight ao analisar alguns cenários básicos (como analisar los da Web e dados de sensores). Além de aprender como usar o HDInsight para analisar tais dados, você também verá como é possível conectar outros aplicativos e serviços a esses dados. Visualizar dados ao conectar ao Microsoft Excel fornecerá um exemplo da eficiência dessa abordagem.</p></td>
<td>Console de consulta</td>
<td>O Hadoop</td>
<td>N/D</td>
</tr>
<tr>
<td>Correção de perda de memória no Templeton</td>
<td>Foi tratada a perda de memória no Templeton que afetava os clientes que tinham um cluster em execução há tempos ou que estavam enviando 100s de solicitações de trabalhos por segundo. O problema se manifestava como erros 5xx do Templeton e a solução alternativa era reiniciar o serviço. A solução alternativa não é mais necessária.</td>
<td>Templeton</td>
<td>Todos</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>

> [!NOTE]
> Para demonstrar os novos recursos disponíveis pela personalização de clusters, os procedimentos que usam as Ações de Script para instalar ps módulos Spark e R em um cluster foram documentados. Para obter mais informações, confira:

* [Instalar e usar o Spark 1.0 em clusters HDInsight](hdinsight-hadoop-spark-install.md)
* [Instalar e usar R em clusters Hadoop do HDInsight](hdinsight-hadoop-r-scripts.md)

## <a name="notes-for-11072014-release-of-hdinsight"></a>Notas da versão de 07/11/2014 do HDInsight

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1    2.1.9.374.1153876
* HDInsight 3.0    3.0.5.374.1153876
* HDInsight 3.1    3.1.1.374.1153876

Esta versão contém as atualizações de componentes a seguir:

<table border="1">
<tr><th>Title</th><th>Descrição</th><th>Componente</th><th>Tipo de cluster</th><th>JIRA (se aplicável)</th></tr>
<tr>
<td>HDP 2.1.7</td>
<td>Esta versão é baseada no Hortonworks Data Platform (HDP) 2.1.7. Para obter mais informações, confira <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notas de versão do HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>
<tr>
<td>Servidor de linha do tempo do YARN</td>
<td>O YARN Timeline Server (também conhecido como Generic Application History Server) foi habilitado por padrão. O Servidor de linha do tempo fornece informações genéricas sobre aplicativos concluídos, como ID do aplicativo, nome do aplicativo, status do aplicativo, hora de envio do aplicativo e hora de conclusão do aplicativo.

Essas informações do aplicativo podem ser recuperadas do headnode tanto ao acessar o URI http://headnodehost:8188 quanto pela execução do comando YARN: yarn application -list -appStates ALL.

Essas informações também podem ser recuperadas remotamente por uma API REST em https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.

Para obter mais informações, confira <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> (Servidor de linha do tempo do YARN).</td>
<td>Serviço, YARN</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>
<tr>
<td>ID de Implantação de Cluster</td>
<td>Iniciando pela versão 1.3.3.1.5426.29232 do SDK, os usuários podem acessar uma ID exclusiva para cada cluster, emitida pelo HDInsight. Isso permite que os clientes tomem conhecimento das instâncias exclusivas de clusters quando um dnsname está sendo reutilizado entre cenários de criação/cancelamento.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>

> [!NOTE]
> O bug que evitava que o número completo de versão fosse mostrado no portal ou retornado pelo SDK ou pelo Windows PowerShell foi corrigido nesta versão.

## <a name="notes-for-10152014-release"></a>Notas para a versão de 15/10/2014

A liberação desse hotfix corrige um vazamento de memória no Templeton que afetou os usuários mais ativos do Templeton. Em alguns casos, os usuários que haviam usado o Templeton com frequência viam erros manifestados como códigos de erro 500, pois as solicitações não tinham memória suficiente para serem executadas. A solução alternativa para esse problema era reiniciar o serviço do Templeton. Esse problema foi corrigido.

## <a name="notes-for-1072014-release"></a>Notas para a versão de 07/10/2014

* Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* retorna o FQDN (nome de domínio totalmente qualificado) do nó em vez de somente o nome do host. Por exemplo, em vez de retornar "**headnode0**", você obtém o FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implementados em uma rede virtual. Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

* Fornecemos novas configurações de memória para a implantação padrão do cluster do HDInsight. As configurações de memória padrão anteriores não atentavam adequadamente às diretrizes referente ao número de núcleos de CPU implantados. Essas novas configurações de memória devem fornecer melhores padrões, de acordo com as recomendações da Hortonworks. Para alterá-las, consulte a documentação de referência do SDK sobre alteração da configuração de clusters. As novas configurações de memória usadas pelo cluster do HDInsight padrão com 4 núcleos de CPU (8 contêineres) são detalhadas na tabela a seguir. (Os valores usados anteriormente a essa versão também são fornecidos entre parênteses).

<table border="1">
<tr><th>Componente</th><th>Alocação de memória</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144 MB (inalterado)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144 MB (inalterado)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (anteriormente -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536 MB (anteriormente 1024 MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (anteriormente -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 MB (anteriormente 1024 MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (anteriormente -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (anteriormente 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 MB (inalterado)</td></tr>
</table>

Para obter mais informações sobre as configurações de memória usadas pelo YARN e pelo MapReduce na plataforma de dados Hortonworks usada pelo HDInsight, consulte [Determinar as configurações de memória de HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). A Hortonworks também oferece uma ferramenta para calcular as configurações adequadas de memória.

Sobre a mensagem de erro do Azure PowerShell e do SDK do HDInsight: “*O cluster não foi configurado para acessar os serviços HTTP*”:

* Esse erro é um [problema de compatibilidade](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conhecido que pode ocorrer devido a uma diferença entre a versão do SDK do HDInsight ou Azure PowerShell e a versão do cluster. Clusters criados a partir de 15/08 dão suporte à nova funcionalidade de provisionamento em Redes Virtuais. Mas essa funcionalidade não é interpretada corretamente por versões mais antigas do SDK do HDInsight ou Azure PowerShell. O resultado é uma falha em algumas operações de envio de trabalho. Se você usar as APIs do SDK do HDInsight ou os cmdlets do Azure PowerShell (**Use-AzureRmHDInsightCluster** ou **Invoke-AzureRmHDInsightHiveJob**) para enviar trabalhos, essas operações podem falhar com a mensagem de erro "*O cluster <clustername> não está configurado para acessar os serviços HTTP*". Ou, dependendo da operação, você pode receber outras mensagens de erro, como “*Não é possível se conectar ao cluster*”.
* Esses problemas de compatibilidade são resolvidos nas versões mais recentes do SDK do HDInsight e do PowerShell do Azure. Recomendamos atualizar o SDK do HDInsight para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao HDInsight SDK mais recente no [NugGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e ao Azure PowerShell Tools em [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notas da versão de 12/09/2014 do HDInsight 3.1
* Esta versão é baseada na HDP (plataforma de dados Hortonworks) 2.1.5. Para obter uma lista de bugs corrigidos nesta versão, consulte a página [Corrigido nesta versão](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) no site da Hortonworks.
* Na pasta de bibliotecas do Pig, o arquivo “avro-mapred-1.7.4.jar” foi alterado para “avro-mapred-1.7.4-hadoop2.jar”. Esse arquivo contém uma pequena correção de bug que é não separável. Recomenda-se que os clientes não assumam uma dependência direta no nome do arquivo JAR para evitar separações quando os arquivos forem renomeados.

## <a name="notes-for-8212014-release"></a>Notas para a versão de 21/08/2014
* Estamos adicionando a configuração WebHCat a seguir (HIVE-7155), que define o limite de memória padrão para um trabalho de controlador Templeton como 1 GB. (O valor padrão anterior era 512 MB.)

     templeton.mapper.memory.mb (=1024)

  * Essa mudança trata do erro a seguir com determinadas consultas de Hive devido a limites mais baixos de memória: “O contêiner está sendo executado além dos limites de memória física”.
  * Para voltar aos padrões antigos, você pode definir o valor dessa configuração para 512 por meio do PowerShell do Azure no momento da criação do cluster, usando o comando a seguir:

      Add-AzureRmHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}
* O nome de host da função zookeeper foi alterado para *zookeeper*. Isso afeta o modo como o nome é resolvido no cluster, mas não afeta as APIs REST externas. Se você tem componentes que utilizam nome de host de *nó zookeeper* , você precisará atualizá-los com o novo nome. Os novos nomes para os três nós zookeeper são:

  * zookeeper0
  * zookeeper1
  * zookeeper2
* A matriz de suporte de versão do HBase é atualizada. Somente a versão HDInsight 3.1 (HBase versão 0.98) tem suporte para cargas de trabalho de produção no HBase. A versão 3.0, que estava disponível para visualização, não terá suporte se você prosseguir.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Observações sobre os clusters criados antes de 15/8/2014
Uma mensagem de erro do Azure PowerShell ou do SDK do HDInsight, "Cluster <clustername> não está configurado para acessar serviços HTTP" (ou dependendo da operação, outras mensagens de erro, como: "Não é possível conectar ao cluster") podem ser encontradas devido à diferença entre a versão do PowerShell do Azure ou do SDK do HDInsight e aquela de um cluster. Clusters criados a partir de 15/08 dão suporte à nova funcionalidade de provisionamento em Redes Virtuais. Essa funcionalidade não é interpretada corretamente por versões mais antigas do Azure PowerShell ou do SDK do HDInsight, o que resulta em falhas nas operações de envio de trabalhos. Se você usar as APIs do SDK do HDInsight ou os cmdlets do Azure PowerShell (como Use-AzureRmHDInsightCluster ou Invoke-AzureRmHDInsightHiveJob) para enviar trabalhos, essas operações podem falhar com uma das mensagens de erro descritas.

Esses problemas de compatibilidade são resolvidos nas versões mais recentes do SDK do HDInsight e do PowerShell do Azure. Recomendamos atualizar o SDK do HDInsight para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao SDK do HDInsight mais recente no [NuGet][nuget-link]. Você pode acessar as ferramentas do Azure PowerShell usando o [Microsoft Web Platform Installer][webpi-link].

## <a name="notes-for-7282014-release"></a>Notas para a versão de 28/07/2014
* **O HDInsight está disponível em novas regiões**: expandimos a presença geográfica do HDInsight para três regiões. Os clientes que usam o HDInsight agora podem criar clusters nessas regiões:
  * Ásia Oriental
  * Centro-Norte dos EUA
  * Centro-Sul dos Estados Unidos
* O HDInsight versão 1.6 (HDP 1.1 e Hadoop 1.0.3) e HDInsight versão 2.1 (HDP 1.3 e Hadoop 1.2) estão sendo removidos do portal do Azure. Você pode continuar criando clusters Hadoop para essas versões usando o cmdlet do Azure PowerShell [New-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) ou usando o [SDK do HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Para obter mais informações, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md).
* Alterações ao Hortonworks Data Platform (HDP) nesta versão:

<table border="1">
<tr><th>HDP</th><th>Alterações</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sem alterações</td></tr>
<tr><td>HDP 2,1 / HDI 3,1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>
</table>

## <a name="notes-for-6242014-release"></a>Notas para a versão de 24/06/2014
Essa versão contém aprimoramentos para o serviço HDInsight:

* **Disponibilidade do HDP 2.1**: O HDInsight 3.1 (que contém o HDP 2.1) agora está disponível de modo geral e é a versão padrão para novos clusters.
* **HBase – melhorias do portal do Azure**: estamos tornando os clusters HBase disponíveis na Visualização. Você pode criar clusters HBase por meio do portal com apenas alguns cliques.

Com o HBase, você pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, por meio de sites interativos que funcionem com conjuntos de dados grandes para dados para o sensor de armazenamento de serviços e dados de telemetria de milhões de pontos de extremidade. A próxima etapa seria analisar os dados nessas cargas de trabalho com trabalhos do Hadoop e isso é possível no HDInsight, por meio do PowerShell do Azure e do painel de cluster do Hive.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Apache Mahout pré-instalado no HDInsight 3.1
 [Mahout](http://hortonworks.com/hadoop/mahout/) é pré-instalado em clusters Hadoop do HDInsight 3.1, de modo que você pode executar trabalhos Mahout sem a necessidade de configuração de cluster adicionais. Por exemplo, você pode acessar remotamente um cluster do Hadoop usando o protocolo RDP e, sem etapas adicionais, executar o comando Olá Mundo do Mahout a seguir:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa sobre este procedimento, consulte a documentação do [Exemplo Breiman](https://mahout.apache.org/users/classification/breiman-example.html) no site do Apache Mahout.

### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Consultas de Hive podem usar o Tez no HDInsight 3.1
O Hive 0.13 está disponível no HDInsight 3.1 e é capaz de executar consultas usando Tez, que pode ser utilizado para melhorias de desempenho significativas.
O Tez não está habilitado por padrão para consultas de Hive. Para usá-lo, você deve escolher a opção de fazê-lo. Você pode habilitar o Tez executando o trecho de código a seguir:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

A Hortonworks publicou uma demonstração detalhada das melhorias nas consultas do Hive com o Tez, conforme fornecido em medições de desempenho padrão. Para obter mais informações, consulte [Medindo o desempenho do Hive 13 Apache para Hadoop Enterprise](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Para saber mais, consulte o artigo [Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

### <a name="global-availability"></a>Disponibilidade global
Com a versão do HDInsight no Hadoop 2.2, a Microsoft disponibilizou o HDInsight em todas as principais regiões geográficas onde o Azure está disponível. Especificamente, os datacenters da Europa Ocidental e do Sudeste Asiático foram colocados online. Isso permite que os clientes localizem clusters em um datacenter próximo e possivelmente em uma zona com requisitos de conformidade semelhantes.

### <a name="dos--donts-between-cluster-versions"></a>Prós e contras entre as versões de clusters
**Os metastores do Oozie usados com um cluster do HDInsight 3.1 não são compatíveis com as versões anteriores dos clusters do HDInsight 2.1 e não podem ser usados com elas**.

Um banco de dados do metastore do Oozie personalizado implantado com um cluster do HDInsight 3.1 não pode ser reutilizado com um cluster do HDInsight 2.1. Isso acontece mesmo se o metastore tiver sido originado com um cluster HDInsight 2.1. Esse cenário não tem suporte, já que o esquema do metastore é atualizado quando usado com um cluster HDInsight 3.1 e, portanto, não é mais compatível com o metastore exigido pelos clusters HDInsight 2.1. Qualquer tentativa de reutilizar um metastore do Oozie que tenha sido usado com um cluster do HDInsight 3.1 fará com que o cluster HDInsight 2.1 seja inutilizado.

**Os metastores do Oozie não podem ser compartilhados entre clusters diferentes.**

Metastores do Oozie estão associados a clusters específicos e não podem ser compartilhados entre clusters.

### <a name="breaking-changes"></a>Alterações de última hora
**Sintaxe de prefixo**: apenas a sintaxe "wasb://" tem suporte em clusters HDInsight 3.0 e 3.1. A antiga sintaxe "asv://" tem suporte em clusters HDInsight 1.6 e 2.1, mas não tem suporte em clusters HDInsight 3.0 ou 3.1. Isso significa que qualquer trabalho enviado para um cluster HDInsight 3.0 ou 3.1 que use explicitamente a sintaxe "asv://" falhará. Em vez disso, a sintaxe “wasb://” deve ser usada. Os trabalhos enviados para qualquer cluster HDInsight 3.0 ou 3.1, criados com um metastore existente e contendo referências explícitas a recursos usando a sintaxe “asv://” também falharão. Esses metastores precisam ser recriados usando a sintaxe “wasb://” para endereçar recursos.

**Portas:**as portas usadas pelo serviço HDInsight foram alteradas. Os números de porta que estavam sendo usados estavam dentro do intervalo de portas efêmero do SO Windows. As portas são alocadas automaticamente por meio de um intervalo efêmero, para comunicações baseadas em protocolo de Internet de curta duração. O novo conjunto de números de porta de serviço do HDP (plataforma de dados Hortonworks) permitido está agora fora desse intervalo, para evitar conflitos que poderiam surgir com as portas usadas pelos serviços que estão sendo executados no headnode. Os novos números de porta não devem causar nenhuma mudança de última hora. Os números usados são conforme descrito a seguir:

 **HDInsight 1.6 (HDP 1.1)**

<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

 **HDInsight 3.1 e 3.0 (HDP 2.1 e 2.0)**

<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table>

### <a name="dependencies"></a>Dependências
As seguintes dependências foram acrescentadas ao HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

As seguintes dependências não existem mais no HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

### <a name="version-changes"></a>Mudanças de versão
As mudanças de versão a seguir foram feitas entre o HDInsight 2.x (HDP1.x) e o HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']

### <a name="drivers"></a>Drivers
O driver JDBC (conectividade de banco de dados Java) do SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se você desejar se conectar ao HDInsight usando o ODBC (conectividade aberta de banco de dados), use o driver ODBC do Microsoft Hive. Para obter mais informações, consulte [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

### <a name="bug-fixes"></a>Correções de bug
Com essa versão, atualizamos as versões do HDInsight a seguir com diversas correções de bug:

* HDInsight 2,1 (HDP 1,3)
* HDInsight 3,0 (HDP 2,0)
* HDInsight 3.1 (HDP 2.1)

## <a name="hortonworks-release-notes"></a>Notas de versão Hortonworks
As notas de versão para os HDPs (plataformas de dados Hortonworks) que são usados pelos clusters de versão do HDInsight estão disponíveis nos locais listados a seguir:

* O HDInsight versão 3.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Esse é o cluster do Hadoop padrão criado ao usar o portal do Azure após 07/11/2014. Os clusters do HDInsight 3.1 criados antes de 07/11/2014 eram baseados no [Hortonworks Data Platform 2.1.1][hdp-2-1-1]
* O HDInsight versão 3.0 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.0][hdp-2-0-8].
* O HDInsight versão 2.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.3][hdp-1-3-0].
* O HDInsight versão 1.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
