---
title: Hue com Hadoop em clusters HDInsight baseados em Linux – Azure
description: Saiba como instalar o Hue em clusters do HDInsight e usar o túnel para rotear as solicitações para a Matiz. Use o Hue para procurar armazenamento e executar o Hive ou o Pig.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 97a8dd2476642e693b589b4046f612c5569b9c0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865121"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e usar o Hue em clusters de Hadoop do HDInsight

Saiba como instalar o Hue em clusters do HDInsight e usar o túnel para rotear as solicitações para a Matiz.

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
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites da comunidade que podem ser usados, como: [Página de perguntas e respostas da Microsoft sobre o HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalar o Hue usando Ações de Script

Use as informações na tabela abaixo para a Ação de Script. Consulte [Personalizar clusters do HDInsight usando Ações de Script](hdinsight-hadoop-customize-cluster-linux.md) para obter instruções específicas sobre como usar as Ações de Script.

> [!NOTE]  
> Para instalar o Hue em clusters HDInsight, o tamanho do nó de cabeçalho recomendado é de, pelo menos, A4 (8 núcleos, memória de 14 GB).

|Propriedade |Valor |
|---|---|
|Tipo de script:|- Personalizado|
|Nome|Instalar o Hue|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Tipos de nó:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Usar o Hue com clusters do HDInsight

Você só pode ter uma conta de usuário com o Hue em clusters regulares. Para acesso de vários usuários, habilite o [Enterprise Security Package](./domain-joined/hdinsight-security-overview.md) no cluster. O túnel SSH é a única maneira de acessar o Hue no cluster a partir do momento em que ele está em execução. O túnel via SSH permite que o tráfego vá diretamente para o nó de cabeçalho do cluster no qual o Hue está sendo executado. Após a conclusão do provisionamento do cluster, use as etapas a seguir para usar o Hue em um cluster do HDInsight.

> [!NOTE]  
> É recomendável usar o navegador Web Firefox para seguir as instruções abaixo.

1. Use as informações em [Usar túnel SSH para acessar a interface do usuário da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH a partir do sistema de cliente para o cluster HDInsight, e em seguida, configurar seu navegador da Web para usar o túnel como um proxy.

1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Após a conexão, use o seguinte comando para obter o nome de domínio totalmente qualificado do nó de cabeçalho primário:

    ```bash
    hostname -f
    ```

    Isso retornará um nome semelhante ao seguinte:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Esse é o nome do host do nó de cabeçalho primário onde o site da Hue está localizado.

1. Use o navegador para abrir o Portal do Hue em `http://HOSTNAME:8888`. Substitua HOSTNAME pelo nome obtido na etapa anterior.

   > [!NOTE]  
   > Ao fazer logon pela primeira vez, será solicitado que você crie uma conta para poder fazer logon no portal do Hue. As credenciais que você especificar aqui serão limitadas ao portal e não serão relacionadas às credenciais de usuário SSH ou de administrador que você especificou durante o provisionamento do cluster.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="Janela de logon do portal do Hue do HDInsight":::

### <a name="run-a-hive-query"></a>Executar um trabalho do Hive

1. No portal do Hue, selecione **Editores de Consulta** e, em seguida, selecione **Hive** para abrir o editor do Hive.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="Uso do editor do Hive no portal do Hue do HDInsight":::

2. Na guia **Ajuda**, em **Banco de dados**, você deverá ver **hivesampletable**. Essa é uma tabela de exemplo que é enviada juntamente com todos os clusters de Hadoop no HDInsight. Insira uma consulta de exemplo no painel direito e veja a saída na guia **Resultados** no painel abaixo, como mostrado na captura de tela.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="Consulta do Hive no portal do Hue do HDInsight":::

    Você também pode usar a guia **Gráfico** para ver uma representação visual do resultado.

### <a name="browse-the-cluster-storage"></a>Procurar no armazenamento de cluster

1. No portal do Hue, selecione **Navegador de Arquivos** no canto superior direito da barra de menus.
2. Por padrão, o navegador de arquivos é aberto no diretório **/user/myuser** . Selecione a barra invertida imediatamente antes do diretório “user” no caminho até a raiz do contêiner de armazenamento do Azure associado ao cluster.

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="Navegador de arquivos do portal do Hue do HDInsight":::

3. Clique com o botão direito do mouse em um arquivo ou pasta para ver as operações disponíveis. Use o botão **Carregar** no canto superior direito para carregar arquivos no diretório atual. Use o botão **Novo** para criar novos arquivos ou diretórios.

> [!NOTE]  
> O navegador de arquivos do Hue só pode mostrar o conteúdo do contêiner padrão associado ao cluster do HDInsight. Quaisquer contêineres/contas de armazenamento adicionais associados ao cluster não poderão ser acessados usando o navegador de arquivos. No entanto, os contêineres adicionais associados ao cluster sempre estarão acessíveis para os trabalhos do Hive. Por exemplo, ao digitar o comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` no editor do Hive, você poderá ver também o conteúdo de contêineres adicionais. Neste comando, **newcontainer** não é o contêiner padrão associado a um cluster.

## <a name="important-considerations"></a>Considerações importantes

1. O script usado para instalar o Hue instala-o apenas no nó de cabeçalho primário do cluster.

1. Durante a instalação, vários serviços do Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois que o script termina de instalar o Hue, pode levar algum tempo para que outros serviços do Hadoop sejam iniciados. Isso pode, inicialmente, afetar o desempenho do Hue. Depois que todos os serviços tiverem sido iniciados, o Hue estará totalmente funcional.

1. O Hue não reconhece os trabalhos do Apache Tez, que é o padrão atual do Hive. Se você quiser usar o MapReduce como o mecanismo de execução do Hive, atualize o script para usar o comando a seguir em seu script:

   `set hive.execution.engine=mr;`

1. Com clusters do Linux, você pode ter um cenário no qual os serviços estão em execução no nó de cabeçalho primário enquanto o Gerenciador de Recursos pode estar em execução no secundário. Um cenário como esse pode resultar em erros (mostrados abaixo) ao usar o Hue para exibir detalhes de trabalhos EM EXECUÇÃO no cluster. No entanto, você pode exibir os detalhes do trabalho após ele ser concluído.

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Mensagem de exemplo de erro do portal do Hue":::

   Isso ocorre devido a um problema conhecido. Como solução alternativa, modifique o Ambari para que o Gerenciador de Recursos ativo também seja executado no nó de cabeçalho primário.

1. O Hue entende o WebHDFS, enquanto os clusters HDInsight utilizam o Armazenamento do Azure com o `wasbs://`. Portanto, o script personalizado utilizado com a ação de script instala WebWasb, que é um serviço compatível com WebHDFS para conversar com o WASB. Portanto, embora em alguns lugares o portal do Hue esteja marcado como HDFS (como quando você move o mouse sobre o **Navegador de Arquivos**), ele deve ser interpretado como WASB.

## <a name="next-steps"></a>Próximas etapas

[Instalar o R em clusters HDInsight](./r-server/r-server-overview.md). Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de grafos.