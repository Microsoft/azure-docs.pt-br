---
title: Gerenciar clusters Hadoop baseados no Windows no HDInsight usando o Portal do Azure | Microsoft Docs
description: "Saiba como administrar o serviço do HDInsight. Crie um cluster HDInsight, abra o console interativo do JavaScript e abra o console de comando Hadoop."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 3f1c7bb1795828899148a8ba8a0461a06947d40d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerenciar clusters Hadoop baseados no Windows no HDInsight usando o Portal do Azure

Ao usar o [Portal do Azure][azure-portal], você pode criar clusters Hadoop baseados no Windows no Azure HDInsight, alterar a senha de usuário do Hadoop e habilitar o RDP (Protocolo de Área de Trabalho Remota) para que você possa acessar o console de comando do Hadoop no cluster.

As informações neste artigo aplicam-se apenas aos clusters HDInsight baseados no Windows. Para obter informações sobre como gerenciar clusters baseados no Linux, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Conta de armazenamento do Azure** - um cluster do HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para obter mais informações sobre como o Armazenamento de Blob do Azure fornece uma experiência perfeita com os clusters HDInsight, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Abra o Portal
1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Depois de abrir o portal, você poderá:

   * Clique em **Novo** no menu esquerdo para criar um novo cluster:

       ![novo botão do cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Clique em **Clusters HDInsight** no menu esquerdo.

       ![Botão do cluster HDInsight do Portal do Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Se **HDInsight** não aparecer no menu esquerdo, clique em **Procurar**.

     ![Botão Procurar cluster do Portal do Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Criar clusters
Para obter as instruções de criação usando o Portal, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e com suporte, consulte [Qual versão do Hadoop está no HDInsight do Azure](hdinsight-component-versioning.md). Você pode personalizar o HDInsight usando uma das seguintes opções:

* Use a ação de Script para executar scripts personalizados que possam personalizar um cluster para alterar a configuração de cluster ou instalar componentes personalizados como Giraph ou Solr. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).
* Use os parâmetros de personalização do cluster no SDK do .NET do HDInsight ou no PowerShell do Azure durante a criação do cluster. Essas alterações de configuração são preservadas durante o tempo de vida do cluster e não são afetadas pelas novas imagens do nó do cluster que a plataforma do Azure refaz periodicamente para manutenção. Para mais informações sobre como usar os parâmetros de personalização do cluster, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Alguns componentes nativos do Java, como Mahout e Cascading, podem ser executados no cluster como arquivos JAR. Esses arquivos JAR podem ser distribuídos para o armazenamento de Blob do Azure e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).
  >
  > O Cascading não tem suporte do HDInsight e não é qualificado para o Suporte da Microsoft. Para obter as listas dos componentes suportados, confira [Novidades nas versões de cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).
  >
  >

Não há suporte para a instalação de software personalizado no cluster usando uma conexão de área de trabalho remota. Evite armazenar arquivos nas unidades de nó de cabeçalho, pois eles serão perdidos se você precisar recriar os clusters. É recomendável armazenar os arquivos no armazenamento de Blob do Azure. O armazenamento de Blob é persistente.

## <a name="list-and-show-clusters"></a>Listar e mostrar clusters
1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo.
3. Clique no nome do cluster. Se a lista de clusters for longa, você poderá usar o filtro na parte superior da página.
4. Clique duas vezes em um cluster da lista para exibir os detalhes.

    **Menu e fundamentos**:

    ![Fundamentos do cluster HDInsight do portal do Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Para personalizar o menu, clique com o botão direito do mouse em qualquer parte do menu e, então, clique em **Personalizar**.
   * **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.
   * **Painel**, **Painel do Cluster** e **URL: são maneiras de acessar o painel do cluster, que é o Ambari Web para clusters baseados em Linux. -**Secure Shell**: mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH).
   * **Cluster em Escala**: permite alterar o número de nós de trabalho deste cluster.
   * **Excluir**: exclui o cluster.
   * **Início Rápido**: exibe informações que o ajudarão a começar a usar o HDInsight.
   * **Usuários: permite definir permissões para o *gerenciamento do portal* deste cluster para outros usuários em sua assinatura do Azure.

     > [!IMPORTANT]
     > Isso afeta *apenas* o acesso e as permissões para esse cluster no Portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.
     >
     >
   * **Marcas**: as marcas permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto**e usar um valor comum para todos os serviços associados a um projeto específico.
   * **Ambari Views**: links para o Ambari Web.

     > [!IMPORTANT]
     > Para gerenciar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Uso**:

     ![Uso do cluster HDInsight do Portal do Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Clique em **Configurações**.

    ![Uso do cluster HDInsight do Portal do Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Propriedades**: exiba as propriedades do cluster.
   * **Identidade do AAD do Cluster**:
   * **Chaves de Armazenamento do Azure**: exiba a conta de armazenamento padrão e a chave dela. A conta de armazenamento é configurada durante o processo de criação do cluster.
   * **Logon do Cluster**: altere o nome de usuário e a senha do HTTP de cluster.
   * **Metastores Externos**: exiba os Hive e Oozie metastores. Os metastores só podem ser configurados durante o processo de criação do cluster.
   * **Dimensionar o Cluster**: aumente e diminua o número de nós de trabalho do cluster.
   * **Área de Trabalho Remota**: habilite e desabilite o acesso da área de trabalho remota (RDP) e configure o nome de usuário da RDP.  O nome de usuário da RDP deve ser diferente do nome de usuário de HTTP.
   * **Parceiro de Registro**:

     > [!NOTE]
     > Esta é uma lista genérica de configurações disponíveis; nem todas elas estarão presentes para todos os tipos de cluster.
     >
     >
6. Clique em **Propriedades**:

    A seção das propriedades lista o seguinte:

   * **Nome do host**: nome do cluster.
   * **URL do Cluster**.
   * **Status**: incluindo Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Região**: local do Azure. Para ter acesso à lista de locais do Azure com suporte, consulte a caixa de listagem suspensa **Região** em [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Dados criados**.
   * **Sistema operacional**: **Windows** ou **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versão**. Consulte [versões do HDInsight](hdinsight-component-versioning.md)
   * **Assinatura**: nome da assinatura.
   * **ID da assinatura**.
   * **Fonte de dados primária**. A conta de armazenamento de Blob do Azure usada como o sistema de arquivos Hadoop padrão.
   * **Faixa de preço dos nós de trabalho**.
   * **Faixa de preço do nó de cabeça**.

## <a name="delete-clusters"></a>Excluir clusters
Excluir um cluster não excluirá a conta de armazenamento padrão, nem nenhuma conta de armazenamento vinculada. Você pode recriar o cluster usando as mesmas contas de armazenamento e as mesmas metastores.

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Excluir** no menu superior e siga as instruções.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

> [!NOTE]
> Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades.  Confira [Listar e mostrar clusters](#list-and-show-clusters).
>
>

O impacto da alteração do número de nós de dados em cada tipo de cluster com suporte do HDInsight:

* O Hadoop

    Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* HBase

    Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Para obter mais informações sobre como usar o shell do HBase, confira []
* Storm

    Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.

    A redistribuição pode ser feita de duas maneiras:

  * Interface da Web Storm
  * Ferramenta CLI (interface de linha de comando)

    Consulte a [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da Web do Storm está disponível no cluster HDInsight:

    ![HDInsight storm dimensionar novo balanceamento](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Para dimensionar clusters**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Dimensionar o Cluster**.
4. Insira o **Número de Nós de Trabalho**. O limite no número de nós do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança para aumentar o limite.  As informações de custo refletirão as alterações feitas no número de nós.

    ![HDInsight Hadoop HBase Storm Spark scale](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausar/desligar clusters
A maioria dos trabalhos do Hadoop é composta por trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso.
Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Há várias maneiras de programar o processo:

* Use o Azure Data Factory. Consulte [Serviços Vinculados ao Azure HDInsight](../data-factory/compute-linked-services.md) e [Transformar e analisar usando o Azure Data Factory](../data-factory/transform-data.md) para saber mais sobre os serviços sob demanda e autodefinidos vinculados ao HDInsight.
* Use o Azure PowerShell.  Consulte [Analisar dados de atraso de voo](hdinsight-analyze-flight-delay-data.md).
* Use a CLI do Azure. Consulte [Gerenciar clusters HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* Use o SDK .NET do HDInsight. Consulte [Enviar trabalhos do Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para saber mais sobre preços, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do Portal, veja [Excluir clusters](#delete-clusters)

## <a name="change-cluster-username"></a>Alterar nome de usuário do cluster
Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight é criada durante o processo de criação. Você também pode criar uma conta de usuário RDP para acessar o cluster via RDP. Consulte [Habilitar área de trabalho remota](#connect-to-hdinsight-clusters-by-using-rdp).

**Para alterar o nome de usuário e a senha do cluster HDInsight**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Logon do Cluster**.
4. Se **Logon do cluster** tiver sido habilitado, você deverá clicar em **Desabilitar** e clicar em **Habilitar** antes de poder alterar o nome de usuário e a senha.
5. Altere o **Nome de Logon do Cluster** e/ou a **Senha de Logon do Cluster** e clique em **Salvar**.

    ![HDInsight change cluster user username password http user](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso no portal do Azure.

> [!NOTE]
> Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.
>
>

**Para conceder/revogar acesso aos serviços Web HTTP**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Logon do Cluster**.
4. Se **Logon do cluster** tiver sido habilitado, você deverá clicar em **Desabilitar** e clicar em **Habilitar** antes de poder alterar o nome de usuário e a senha.
5. Para **Nome de Usuário de Logon no Cluster** e **Senha de Logon no Cluster**, insira o novo nome de usuário e a senha (respectivamente) para o cluster.
6. Clique em **SALVAR**.

    ![HDInsight grand remove http web service access](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Encontrar a conta de armazenamento padrão
Cada cluster HDInsight tem uma conta de armazenamento padrão. A conta de armazenamento padrão e as chaves para um cluster são exibidos em **Configurações**/**Propriedades**/**Chaves de Armazenamento do Azure**. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo de recursos do Azure. O grupo de recursos do Azure ao qual um cluster pertence aparece em:

* A lista de clusters tem uma coluna **Grupo de Recursos** .
* Bloco **Fundamentos** do cluster.  

Consulte [Listar e mostrar clusters](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Abrir o console de Consulta do HDInsight
O console de Consulta do HDInsight inclui os seguintes recursos:

* **Editor Hive**: uma interface GUI da Web para enviar trabalhos do Hive.  Consulte [Executar consultas do Hive usando o Console de Consulta](hdinsight-hadoop-use-hive-query-console.md).

    ![Editor de hive do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Histórico de trabalhos**: monitore trabalhos do Hadoop.  

    ![histórico de trabalhos do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Clique em **Nome da Consulta** para mostrar os detalhes, incluindo propriedades do trabalho, **Consulta de Trabalho** e **Saída do Trabalho. Você também pode baixar a consulta e a saída para sua estação de trabalho.
* **Navegador de Arquivos**: procure a conta de armazenamento padrão e as contas de armazenamento vinculadas.

    ![Pesquisa do navegador de arquivos do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na captura de tela, o tipo **<Account>** indica que o item é uma conta de armazenamento do Azure.  Clique no nome da conta para procurar arquivos.
* **IU do Hadoop**.

    ![Interface do usuário do Hadoop do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Na **IU do Hadoop*, você pode procurar arquivos e verificar logs.
* **IU do Yarn**.

    ![Interface do usuário do YARN do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Execute consultas Hive
Para executar trabalhos do Hive no Portal, clique em **Editor do Hive** no console da Consulta do HDInsight. Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorar trabalhos
Para monitorar trabalhos no Portal, clique em **Histórico de Trabalhos** no console da Consulta do HDInsight. Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Procurar arquivos
Para procurar arquivos armazenados na conta de armazenamento padrão e as contas de armazenamento vinculadas, clique em **Navegador de Arquivos** no console de Consulta do HDInsight. Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

Você também pode usar o utilitário **Procurar no sistema de arquivos** da **interface do usuário do Hadoop** no console do HDInsight.  Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorar o uso do cluster
A seção **Uso** da folha do cluster do HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

> [!IMPORTANT]
> Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Abrir a IU do Hadoop
Para monitorar o cluster, procure no sistema de arquivos e verifique os logs, clique em **IU do Hadoop** no console de Consulta do HDInsight. Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Abrir a IU do Yarn
Para usar a interface do usuário do Yarn, clique em **Interface do usuário do Yarn** no console de Consulta do HDInsight. Consulte [Abrir o console de consulta do HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Conectar-se aos clusters usando o RDP
As credenciais do cluster que você forneceu em sua criação concedem acesso aos serviços do cluster, mas não ao próprio cluster através da área de trabalho remota. Você pode ativar o acesso de Área de Trabalho Remota ao provisionar um cluster ou depois que um cluster for provisionado. Para obter instruções sobre como habilitar a Área de Trabalho Remota na criação, consulte [Criar cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

**Para habilitar a área de trabalho remota**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Área de Trabalho Remota**.
4. Insira **Expira em**, **Nome de Usuário de Área de Trabalho Remota** e **Senha de Área de Trabalho Remota** e clique em **Habilitar**.

    ![Habilitar/desabilitar configuração de área de trabalho remota do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Os valores padrão para Expira em é uma semana.

   > [!NOTE]
   > Você também pode usar o SDK do .NET HDInsight para habilitar a área de trabalho remota em um cluster. Use o **EnableRdp** método no objeto de cliente HDInsight da seguinte maneira: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. Da mesma forma, para desabilitar a área de trabalho remota no cluster, você pode usar **client.DisableRdp(clustername, location)**. Para obter mais informações sobre esses métodos, consulte [Referência do SDK do .NET HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Isso é aplicável apenas a clusters do HDInsight em execução no Windows.
   >
   >

**Para conectar-se ao cluster usando o RDP**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Procurar Tudo** no menu esquerdo, clique em **Clusters HDInsight**, clique no nome do cluster.
3. Clique em **Configurações** no menu superior e clique em **Área de Trabalho Remota**.
4. Clique em **Conectar** e siga as instruções. Se Conectar estiver desabilitado, você deverá habilitá-lo primeiro. Use o nome do usuário e a senha da Área de Trabalho Remota.  Não é possível usar as credenciais de usuário do Cluster.

## <a name="open-hadoop-command-line"></a>Abrir a linha de comando do Hadoop
Para conectar-se ao cluster usando a área de trabalho remota e usar a linha de comando do Hadoop, você deve primeiro habilitar o acesso à área de trabalho remota para o cluster conforme descrito na seção anterior.

**Para abrir a linha de comando do Hadoop**

1. Conecte-se ao cluster usando a Área de Trabalho Remota.
2. Na área de trabalho, clique duas vezes em **Linha de Comando do Hadoop**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Para obter mais informações sobre os comandos Hadoop, consulte [Referência de comandos Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Na captura de tela anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número da versão pode ser alterado com base na versão dos componentes do Hadoop instalados no cluster. Você pode usar variáveis de ambiente do Hadoop para referir-se a essas pastas. Por exemplo:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Enviar trabalhos Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Linha de comando do Hadoop"
