---
title: Hue com Hadoop em clusters HDInsight baseados em Linux – Azure
description: Saiba como instalar o Hue em clusters do HDInsight e usar o túnel para rotear as solicitações para a Matiz. Use o Hue para procurar armazenamento e executar o Hive ou o Pig.
keywords: hue hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: b0354803a117e8e2c2382ae888bde94a502f24c6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760623"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e usar o Hue em clusters de Hadoop do HDInsight

Saiba como instalar o Hue em clusters do HDInsight e usar o túnel para rotear as solicitações para a Matiz.

> [!IMPORTANT]  
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>O que é o Hue?
Matiz é um conjunto de aplicativos Web usados para interagir com um cluster Apache Hadoop. Você pode usar o Hue para procurar o armazenamento associado a um cluster de Hadoop (WASB, no caso de clusters HDInsight), executar trabalhos de Hive e scripts do Pig, etc. Os componentes a seguir são disponibilizados com as instalações do Hue em um cluster Hadoop do HDInsight.

* Editor de Hive Beeswax
* Apache Pig
* Gerenciador do Metastore
* Apache Oozie
* Navegador de Arquivos (que dialoga com o contêiner padrão WASB)
* Navegador de Trabalhos

> [!WARNING]  
> Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites da comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalar o Hue usando Ações de Script

O script para instalar o Hue em um cluster HDInsight baseado em Linux está disponível em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Você pode usar esse script para instalar o Hue em clusters com Blobs de armazenamento do Azure (WASB) ou Azure Data Lake Storage como armazenamento padrão.

Esta seção fornece instruções sobre como usar o script ao provisionar o cluster usando o Portal do Azure.

> [!NOTE]  
> O Azure PowerShell, CLI clássico do Azure, o SDK do .NET do HDInsight ou modelos do Azure Resource Manager também podem ser usados para aplicar ações de script. Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Inicie provisionando um cluster com as etapas em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento.

   > [!NOTE]  
   > Para instalar o Hue em clusters HDInsight, o tamanho do nó de cabeçalho recomendado é de, pelo menos, A4 (8 núcleos, memória de 14 GB).
   >
   >
2. Na folha **Configuração Opcional**, selecione **Ações do Script** e forneça as informações como mostrado abaixo:

    ![Fornecer parâmetros de ação de script para Matiz](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "Fornecer parâmetros de ação de script para Matiz")

   * **NAME**: Insira um nome amigável para a ação de script.
   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Marque essa opção.
   * **WORKER**: Deixe em branco.
   * **ZOOKEEPER**: Deixe em branco.
   * **PARAMETERS**: Deixe em branco.
3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração opcional** para salvar as informações de configuração opcional.
4. Continue a provisionar o cluster como descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Usar o Hue com clusters do HDInsight

O túnel SSH é a única maneira de acessar o Hue no cluster a partir do momento em que ele está em execução. O túnel via SSH permite que o tráfego vá diretamente para o nó de cabeçalho do cluster no qual o Hue está sendo executado. Após a conclusão do provisionamento do cluster, use as etapas a seguir para usar o Hue em um cluster Linux do HDInsight.

> [!NOTE]  
> É recomendável usar o navegador Web Firefox para seguir as instruções abaixo.
>
>

1. Use as informações em [Usar túnel SSH para acessar a interface do usuário da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH a partir do sistema de cliente para o cluster HDInsight, e em seguida, configurar seu navegador da Web para usar o túnel como um proxy.

2. Depois de criar um túnel SSH e configurar seu navegador para tráfego de proxy através dele, encontre o nome de host do nó do cabeçalho primário. Você pode fazer isso conectando-se ao cluster usando SSH na porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` onde **USERNAME** é o nome de usuário SSH e **CLUSTERNAME** é o nome do cluster.

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Após a conexão, use o seguinte comando para obter o nome de domínio totalmente qualificado do nó de cabeçalho primário:

        hostname -f

    Isso retornará um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Esse é o nome do host do nó de cabeçalho primário onde o site da Hue está localizado.
4. Use o navegador para abrir o portal de matiz em http:\//HOSTNAME:8888. Substitua HOSTNAME pelo nome obtido na etapa anterior.

   > [!NOTE]  
   > Ao fazer logon pela primeira vez, será solicitado que você crie uma conta para poder fazer logon no portal do Hue. As credenciais que você especificar aqui serão limitadas ao portal e não serão relacionadas às credenciais de usuário SSH ou de administrador que você especificou durante o provisionamento do cluster.
   >
   >

    ![Faça logon no portal de Matiz](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Especificar credenciais para o portal de Matiz")

### <a name="run-a-hive-query"></a>Executar um trabalho do Hive
1. No portal do Hue, clique em **Editores de Consulta** e, em seguida, clique em **Hive** para abrir o editor do Hive.

    ![Usar o Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Usar o Hive")
2. Na guia **Ajuda**, em **Banco de dados**, você deverá ver **hivesampletable**. Essa é uma tabela de exemplo que é enviada juntamente com todos os clusters de Hadoop no HDInsight. Insira uma consulta de exemplo no painel direito e veja a saída na guia **Resultados** no painel abaixo, como mostrado na captura de tela.

    ![Executar consulta de Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Executar consulta de Hive")

    Você também pode usar a guia **Gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procurar no armazenamento de cluster
1. No portal do Hue, clique em **Navegador de Arquivos** no canto superior direito da barra de menus.
2. Por padrão, o navegador de arquivos é aberto no diretório **/user/myuser** . Clique na barra invertida imediatamente antes do diretório “user” no caminho até a raiz do contêiner de armazenamento do Azure associado ao cluster.

    ![Use o navegador de arquivos](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Use o navegador de arquivos")
3. Clique com o botão direito do mouse em um arquivo ou pasta para ver as operações disponíveis. Use o botão **Carregar** no canto superior direito para carregar arquivos no diretório atual. Use o botão **Novo** para criar novos arquivos ou diretórios.

> [!NOTE]  
> O navegador de arquivos do Hue só pode mostrar o conteúdo do contêiner padrão associado ao cluster do HDInsight. Quaisquer contêineres/contas de armazenamento adicionais associados ao cluster não poderão ser acessados usando o navegador de arquivos. No entanto, os contêineres adicionais associados ao cluster sempre estarão acessíveis para os trabalhos do Hive. Por exemplo, ao digitar o comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` no editor do Hive, você poderá ver também o conteúdo de contêineres adicionais. Neste comando, **newcontainer** não é o contêiner padrão associado a um cluster.
>
>

## <a name="important-considerations"></a>Considerações importantes
1. O script usado para instalar o Hue instala-o apenas no nó de cabeçalho primário do cluster.

2. Durante a instalação, vários serviços do Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois que o script termina de instalar o Hue, pode levar algum tempo para que outros serviços do Hadoop sejam iniciados. Isso pode, inicialmente, afetar o desempenho do Hue. Depois que todos os serviços tiverem sido iniciados, o Hue estará totalmente funcional.
3. A matiz não reconhece os trabalhos do Apache Tez, que é o padrão atual do Hive. Se você quiser usar o MapReduce como o mecanismo de execução do Hive, atualize o script para usar o comando a seguir em seu script:

        set hive.execution.engine=mr;

4. Com clusters do Linux, você pode ter um cenário no qual os serviços estão em execução no nó de cabeçalho primário enquanto o Gerenciador de Recursos pode estar em execução no secundário. Um cenário como esse pode resultar em erros (mostrados abaixo) ao usar o Hue para exibir detalhes de trabalhos EM EXECUÇÃO no cluster. No entanto, você pode exibir os detalhes do trabalho após ele ser concluído.

   ![Erro no portal de matiz](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Erro no portal de matiz")

   Isso ocorre devido a um problema conhecido. Como solução alternativa, modifique o Ambari para que o Gerenciador de Recursos ativo também seja executado no nó de cabeçalho primário.
5. O Hue entende o WebHDFS, enquanto os clusters HDInsight utilizam o Armazenamento do Azure com o `wasb://`. Portanto, o script personalizado utilizado com a ação de script instala WebWasb, que é um serviço compatível com WebHDFS para conversar com o WASB. Portanto, embora em alguns lugares o portal do Hue esteja marcado como HDFS (como quando você move o mouse sobre o **Navegador de Arquivos**), ele deve ser interpretado como WASB.

## <a name="next-steps"></a>Próximas etapas
* [Instalar o Apache Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de grafos usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.
* [Instalar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de grafos.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
