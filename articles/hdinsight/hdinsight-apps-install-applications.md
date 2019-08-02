---
title: Instalar aplicativos de terceiros no HDInsight do Azure
description: Saiba como instalar aplicativos de terceiros do Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9d25996db9c13a12781d369327f2b43a41fb4115
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786528"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar um aplicativo do [Apache Hadoop](https://hadoop.apache.org/) de terceiros no Azure HDInsight. Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster do HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria.  

A lista a seguir mostra os aplicativos publicados:

|Aplicativo |Tipo de cluster (s) | DESCRIÇÃO |
|---|---|---|
|[Plataforma de inteligência AtScale](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |O Hadoop |AtScale transforma seu cluster do HDInsight em um servidor OLAP de expansão, permitindo que você consultar bilhões de linhas de dados interativamente usando as ferramentas de BI, você já sabe, o proprietário e adora – do Microsoft Excel, Power BI, Tableau Software para QlikView. |
|[CDAP para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |A CDAP é a primeira plataforma de integração unificada para big data que acelera a implantação para o Hadoop e permite que a TI fornecer dados de autoatendimento. Código-fonte aberto e extensível, a CDAP remove as barreiras à inovação. Requisitos: 4 nós região, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |O Hadoop |Autoatendimento plataforma escalonável do Datameer de preparação, exploração e que regem seus dados para análise aceleram transformar dados de várias origens complexos em informações valiosas de pronto para os negócios, fornecendo insights mais rápidas e inteligentes em uma escala empresarial. |
|[DSS do Dataiku no HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |O DSS do Dataiku em uma plataforma de ciência de dados da empresa que permite aos cientistas de dados e analistas de dados colaborar para criar e executar serviços e novos produtos de dados com mais eficiência, transformar dados brutos em previsões de impacto. |
|[Aplicativo de HDI do WANdisco Fusion](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark,HBase,Storm,Kafka |Mantendo os dados consistentes em um ambiente distribuído é um desafio de operações de dados em massa. O WANdisco Fusion, uma plataforma de software corporativo, resolve esse problema, permitindo que a consistência de dados não estruturados em qualquer ambiente. |
|[H2O SparklingWater para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water dá suporte aos seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, máquina de aumento de gradiente, redes neurais profundas, de aprendizado profundo, K-means, o PCA, modelos de classificação baixa generalizada, detecção de anomalias, Autoencoders. |
|[Striim para integração de dados em tempo real para HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Storm,Spark,Kafka |Striim (pronuncia-se "fluxo") é um-ponta a plataforma de inteligência + de integração de dados de streaming, permitindo contínua ingestão, processamento e análise de fluxos de dados díspares. |
|[Enterprise-acelerando Jumbune BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Em um alto nível, Jumbune auxilia as empresas em 1. Acelerando o Tez, o mecanismo MapReduce e o Spark com base em Hive, Java, Scala desempenho de carga de trabalho. 2. Cluster de Hadoop proativo de monitoramento, 3. Estabelecendo o gerenciamento de qualidade de dados no sistema de arquivos distribuído. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Kyligence Enterprise alimentado por tecnologia Apache Kylin, permite que o BI sobre Big Data. Como um mecanismo OLAP do enterprise no Hadoop, Kyligence Enterprise capacita o analista de negócios arquitetar BI no Hadoop com o depósito de dados padrão do setor e a metodologia de BI. |
|[Spark Job Server for KNIME Spark Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |Spark Job Server for KNIME Spark Executor é usado para conectar-se a plataforma de análise KNIME aos clusters do HDInsight. |
|[Explosão Presto para HDInsight do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |O Hadoop |Presto é um rápido e escalonável distribuído mecanismo de consulta SQL. Arquitetado para a separação de armazenamento e computação, o Presto é perfeito para consultar dados no Azure Data Lake Storage, no Armazenamento de Blobs do Azure, em bancos de dados SQL e NoSQL e em outras fontes de dados. |
|[Coletor de dados de conjuntos de fluxos para a nuvem do HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector é um mecanismo leve e poderoso que transmite dados em tempo real. Use o coletor de dados a rota e processar dados em seus fluxos de dados. Ele vem com uma licença de avaliação de 30 dias. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise para HDInsight dá suporte a disputa para qualquer escala dos dados de dados de toda a empresa. O custo da execução Trifacta no Azure é uma combinação de Trifacta os custos de assinatura com os custos de infraestrutura do Azure para as máquinas virtuais. |
|[Plataforma de dados do Unifi](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-catalog) |Hadoop,HBase,Storm,Spark |A plataforma de dados do Unifi é um pacote perfeitamente integrado de ferramentas de dados de autoatendimento projetados para capacitar o usuário para lidar com desafios de negócios que aumentar a receita, reduzir custos ou complexidade operacional. |
|[APM Unraveldata](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Desvendar os dados de aplicativo para o cluster HDInsight Spark. |
|[Catálogo de dados controlados por IA waterline](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline cataloga, organiza e controla os dados usando o AI para dados de marca automática com os termos de negócios. O catálogo didático de negócios da Waterline é um componente crítico e de sucesso para iniciativas de análise, conformidade e governança de autoatendimento e gerenciamento de TI. |

As instruções fornecidas neste artigo usam o portal do Azure. Também é possível exportar o modelo do Azure Resource Manager do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e usar o Azure PowerShell e a CLI clássica do Azure para implantar o modelo.  Consulte [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicativos em clusters existentes
O procedimento a seguir mostra como instalar aplicativos do HDInsight em um cluster HDInsight existente.

**Instalar um aplicativo do HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **Analytics** > **clusters de HDInsight**.
3. Selecione um cluster HDInsight na lista.  Se não tiver um, você deverá criá-lo primeiro.  Confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Sob o **as configurações** categoria, selecione **aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![Menu do portal de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecione **+ adicionar** no menu. Você pode ver uma lista de aplicativos disponíveis.  Se **+ adicionar** estiver acinzentada, que significa que há não é nenhum aplicativo para esta versão do cluster HDInsight.
   
    ![Aplicativos disponíveis de aplicativos HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Selecione um dos aplicativos disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Você pode ver o status da instalação do portal notificações (selecione o ícone de sino na parte superior do portal). Depois que o aplicativo é instalado, ele é exibido na folha Aplicativos Instalados.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicativos durante a criação do cluster
Você tem a opção de instalar aplicativos do HDInsight ao criar um cluster. Durante o processo, os aplicativos do HDInsight são instalados depois que o cluster é criado e está no estado de execução. Para instalar aplicativos durante a criação de cluster usando o portal do Azure, você deve usar o **personalizado** opção em vez do padrão **criação rápida** opção.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Listar os aplicativos do HDInsight instalados e as propriedades
O portal mostra uma lista dos aplicativos do HDInsight instalados para um cluster e as propriedades de cada aplicativo instalado.

**Listar o aplicativo do HDInsight e exibir as propriedades**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, navegue até **todos os serviços** > **Analytics** > **clusters de HDInsight**.
3. Selecione um cluster HDInsight na lista.
4. Sob o **as configurações** categoria, selecione **aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![Aplicativos instalados de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecione um dos aplicativos instalados para mostrar a propriedade. A propriedades lista:

    |Propriedade | DESCRIÇÃO |
    |---|---|
    |Nome do aplicativo |Nome do aplicativo. |
    |Status |Status do aplicativo. |
    |Página da web |A URL do aplicativo Web que você implantou no nó de borda. A credencial é igual às credenciais de usuário HTTP que você configurou para o cluster. |
    |Ponto de extremidade SSH |Você pode usar o SSH para conectar-se ao nó de borda. As credenciais SSH são iguais às credenciais de usuário SSH que você configurou para o cluster. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |DESCRIÇÃO | Descrição do aplicativo. |

6. Para excluir um aplicativo, clique nele com o botão direito do mouse e clique em **Excluir** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Conectar-se ao nó de borda
Você pode se conectar ao nó de borda usando HTTP e SSH. As informações de ponto de extremidade podem ser encontradas no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais de ponto de extremidade HTTP são as credenciais de usuário HTTP que você configurou para o cluster HDInsight. As credenciais de ponto de extremidade do SSH são as credenciais SSH que você configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Solução de problemas
Confira [Solucionar problemas de instalação](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Próximas etapas
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: Instalar um aplicativo HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar o cluster HDInsight, testar e hospedar aplicativos HDInsight.

