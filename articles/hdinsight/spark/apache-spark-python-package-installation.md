---
title: Ação de script para pacotes do Python com o Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar a ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters do HDInsight Spark para usar pacotes python externos.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: c3f912b4f4c2e78c44425f489927cee185b3d312
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868708"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gerenciar com segurança o ambiente do Python no Azure HDInsight usando a Ação de Script

O HDInsight tem duas instalações internas do Python no cluster do Spark, Anaconda Python 2.7 e Python 3.5. Os clientes podem precisar personalizar o ambiente do Python, como a instalação de pacotes python externos. Aqui, mostramos a melhor prática de gerenciamento seguro de ambientes do Python para clusters Apache Spark no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se você ainda não tiver um cluster Spark no HDInsight, poderá executar ações de script durante a criação do cluster. Acesse a documentação em [como usar ações de script personalizadas](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight usa um ambiente de tecnologias de software livre construído em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, confira o [site de perguntas frequentes do Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

|Componente |Descrição |
|---|---|
|Interno|Esses componentes estão pré-instalados em clusters do HDInsight e fornecem os recursos básicos do cluster. Por exemplo, o Apache Hadoop YARN Resource Manager, a linguagem de consulta do Apache Hive (HiveQL) e a biblioteca do Mahout pertencem a essa categoria. Uma lista completa de componentes de cluster está disponível em [O que há de novo nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).|
|Personalizado|Você, como usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.|

> [!IMPORTANT]
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode estar apto a resolver o problema OU pode solicitar que você contate canais disponíveis para as tecnologias de software livre em que é encontrado conhecimento profundo sobre essa tecnologia. Por exemplo, há muitos sites da comunidade que podem ser usados, como: [Página de perguntas e respostas da Microsoft sobre o HDInsight](/answers/topics/azure-hdinsight.html), `https://stackoverflow.com`. Além disso, os projetos Apache têm sites de projeto em `https://apache.org`.

## <a name="understand-default-python-installation"></a>Entender a instalação padrão do Python

O cluster do HDInsight Spark é criado com a instalação do Anaconda. Há duas instalações do Python no cluster: Anaconda Python 2.7 e Python 3.5. A tabela a seguir mostra as configurações padrão do Python para Spark, Livy e Jupyter.

|Configuração |Python 2,7|Python 3.5|
|----|----|----|
|Caminho|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Versão do Spark|Padrão definido como 2.7|Pode alterar a configuração para 3,5|
|Versão do Livy|Padrão definido como 2.7|Pode alterar a configuração para 3,5|
|Jupyter|Kernel do PySpark|Kernel do PySpark3|

## <a name="safely-install-external-python-packages"></a>Instalar com segurança pacotes externos do Python

O cluster do HDInsight depende do ambiente interno do Python, tanto Python 2.7 quanto Python 3.5. A instalação direta de pacotes personalizados nesses ambientes internos padrão pode causar alterações inesperadas na versão da biblioteca. Além disso, pode dividir ainda mais o cluster. Para instalar com segurança pacotes externos do Python personalizados para seus aplicativos Spark, siga as etapas abaixo.

1. Crie um ambiente virtual do Python usando o Conda. Um ambiente virtual fornece um espaço isolado para seus projetos sem dividir outros. Ao criar o ambiente virtual do Python, você pode especificar a versão do Python que deseja usar. Você ainda precisa criar um ambiente virtual, mesmo que queira usar Python 2.7 e 3.5. Esse requisito é para garantir que o ambiente padrão do cluster não está sendo dividido. Execute ações de script no cluster para todos os nós com o script abaixo para criar um ambiente virtual do Python.

    -   `--prefix` especifica um caminho em que um ambiente virtual Conda reside. Há várias configurações que precisam ser alteradas com base no caminho especificado aqui. Neste exemplo, usamos o py35new, pois o cluster já tem um ambiente virtual existente chamado py35.
    -   `python=` especifica a versão do Python para o ambiente virtual. Neste exemplo, usamos a versão 3.5, a mesma versão do cluster interno. Você também pode usar outras versões do Python para criar o ambiente virtual.
    -   `anaconda` especifica o package_spec como Anaconda para instalar os pacotes do Anaconda no ambiente virtual.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Instale pacotes externos do Python no ambiente virtual criado, se necessário. Execute ações de script no cluster para todos os nós com o script abaixo para instalar pacotes externos do Python. Aqui, você precisa ter privilégio de acesso no sudo para gravar arquivos na pasta do ambiente virtual.

    Pesquise o [índice do pacote](https://pypi.python.org/pypi) para obter uma lista completa de pacotes disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [conda-forge](https://conda-forge.org/feedstocks/).

    Use o comando abaixo caso queira instalar uma biblioteca com sua versão mais recente:

    - Use o canal do conda:

        -   `seaborn` é o nome do pacote que você deseja instalar.
        -   `-n py35new` especifica o nome do ambiente virtual que foi criado. Não se esqueça de alterar o nome de forma correspondente, com base na criação do ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ou use o repositório PyPi e altere `seaborn` e `py35new` de forma correspondente:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Use o comando abaixo caso queira instalar uma biblioteca com uma versão específica:

    - Use o canal do conda:

        -   `numpy=1.16.1` é o nome do pacote e a versão que você deseja instalar.
        -   `-n py35new` especifica o nome do ambiente virtual que foi criado. Não se esqueça de alterar o nome de forma correspondente, com base na criação do ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ou use o repositório PyPi e altere `numpy==1.16.1` e `py35new` de forma correspondente:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    se você não souber o nome do ambiente virtual, poderá usar SSH no nó principal do cluster e executar `/usr/bin/anaconda/bin/conda info -e` para mostrar todos os ambientes virtuais.

3. Altere as configurações do Spark e do Livy e aponte para o ambiente virtual criado.

    1. Abra a interface do usuário do Ambari, acesse a página Spark2, guia Configurações.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png" alt-text="Alterar a configuração do Spark e do Livy por meio do Ambari" border="true":::

    2. Expanda Livy2-env avançado e adicione as instruções abaixo na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, altere o caminho analogamente.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-livy-config.png" alt-text="Alterar a configuração do Livy por meio do Ambari" border="true":::

    3. Expanda Spark2-env avançado, substitua a instrução exportação PYSPARK_PYTHON existente, na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, altere o caminho analogamente.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-config.png" alt-text="Alterar a configuração do Spark por meio do Ambari" border="true":::

    4. Salve as alterações e reinicie os serviços afetados. Essas alterações precisam de uma reinicialização do serviço Spark2. A interface do usuário do Ambari exibirá um lembrete de reinicialização necessária. Clique em Reiniciar para reiniciar todos os serviços afetados.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-restart-services.png" alt-text="Reiniciar serviços" border="true":::

    5. Defina duas propriedades para a sessão do Spark para garantir que o trabalho aponte para a configuração do Spark atualizada: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` e `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        Usando o terminal ou um notebook, use a `spark.conf.set` função.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Se você estiver usando Livy, adicione as seguintes propriedades ao corpo da solicitação:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Se você quiser usar o novo ambiente virtual criado no Jupyter. Altere as configurações do Jupyter e reinicie-o. Execute ações de script em todos os nós de cabeçalho com a instrução abaixo para apontar o Jupyter para o novo ambiente virtual criado. Não se esqueça de modificar o caminho para o prefixo que você especificou para o seu ambiente virtual. Depois de executar essa ação de script, reinicie o serviço Jupyter por meio da interface do usuário do Ambari para disponibilizar essa alteração.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Você pode confirmar duas vezes o ambiente do Python no Jupyter Notebook executando o código abaixo:

    :::image type="content" source="./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png" alt-text="Verificar a versão do Python no Jupyter Notebook" border="true":::

## <a name="known-issue"></a>Problema conhecido

Há um bug conhecido nas versões `4.7.11`, `4.7.12` e `4.8.0` do Anaconda. Se você vir que suas ações de script param de responder `"Collecting package metadata (repodata.json): ...working..."` e falharem com o `"Python script has been killed due to timeout after waiting 3600 secs"` . Baixe [este script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) e execute-o como ações de script em todos os nós para corrigir o problema.

Para verificar sua versão do Anaconda, você pode usar SSH no nó de cabeçalho do cluster e executar `/usr/bin/anaconda/bin/conda --v`.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Pacotes externos com blocos de anotações do Jupyter no Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
