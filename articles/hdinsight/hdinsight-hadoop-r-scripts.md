---
title: "Usar o R no HDInsight para personalizar clusters – Azure | Microsoft Docs"
description: "Saiba como instalar R usando a Ação de Script e usar R em clusters HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 5b9b793d49217acd9f0c6c518596a7afb5600d69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e usar R em clusters Hadoop do HDInsight (visualização)

Saiba como personalizar o cluster HDInsight baseado em Windows com R usando a Ação de Script, e como usar o R em clusters HDInsight. A [oferta do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) inclui o Servidor R como parte do cluster do HDInsight. Isso permite que os scripts R usem o MapReduce e o Spark para executar cálculos distribuídos. Para obter mais informações, confira [Introdução ao uso do Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md). Para obter informações sobre como usar o R com um cluster baseado no Linux, veja [Instalar e usar o R em clusters Hadoop do HDinsight (Linux)](hdinsight-hadoop-r-scripts-linux.md).

Você pode instalar o R em qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight usando a *Ação de Script*. Um script de exemplo para instalar o R em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Artigos relacionados**

* [Instalar e usar R em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informações gerais sobre como criar clusters HDInsight
* [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script
* [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>O que é R?
O <a href="http://www.r-project.org/" target="_blank">Projeto R para computação de estatísticas</a> é uma linguagem de software livre e um ambiente de computação de estatísticas. O R fornece centenas de funções estatísticas de compilação e uma linguagem própria de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos. O R é o ambiente de programação preferencial para a maioria dos profissionais estatísticos e cientistas em uma ampla variedade de campos.

O R é compatível com o armazenamento de Blob do Azure (WASB) para que os dados armazenados nele possam ser processados usando R no HDInsight.  

## <a name="install-r"></a>Instalar R
Um [exemplo de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar o R em um cluster HDInsight está disponível em um blob somente leitura no armazenamento do Azure. Esta seção oferece instruções sobre como usar o script de exemplo ao criar o cluster usando o Portal do Azure.

> [!NOTE]
> O script de exemplo foi introduzido com o cluster HDInsight versão 3.1. Para obter mais informações sobre versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).
>
>

1. Ao criar um cluster HDInsight no Portal, clique em **Configuração Opcional** e em **Ações de Script**.
2. Na página **Ações de Script** , insira os seguintes valores:

    ![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Usar Ação de Script para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script, por exemplo, <b>Instalar R</b>.</td></tr>
        <tr><td>URI do script</td>
            <td>Especifique o URI para o script que é chamado para personalizar o cluster, por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os Nós</b>, <b>Somente nós do cabeçalho</b> ou <b>Somente nós de trabalho</b>.
        <tr><td>parâmetros</td>
            <td>Especifique os parâmetros, se exigido pelo script. No entanto, o script para instalar o R não requer nenhum parâmetro, por isso você pode deixá-los em branco.</td></tr>
    </table>

    Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar a criação do cluster.

Você também pode usar o script para instalar o R no HDInsight usando o PowerShell do Azure ou o SDK do .NET do HDInsight. Instruções para esses procedimentos são fornecidas posteriormente neste artigo.

## <a name="run-r-scripts"></a>Executar scripts de R
Esta seção descreve como executar um script de R no cluster Hadoop com o HDInsight.

1. **Estabelecer uma conexão da Área de Trabalho Remota para o cluster**: no Portal, habilite a Área de Trabalho Remota para o cluster que você criou com o R instalado e conecte-se ao cluster. Para instruções, consulte [Conectar-se a clusters HDInsight usando RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Abrir o console do R**: a instalação de R coloca um link para o console de R na área de trabalho do nó principal. Clique para abrir o console de R.
3. **Executar o script de R**: o script de R pode ser executado diretamente do console de R colando-o, selecionando-o e pressionando ENTER. Este é um script de exemplo simples que gera os números de 1 a 100 e, em seguida, os multiplica por 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

As duas primeiras linhas chamam as bibliotecas R Hadoop que são instaladas com o R. A linha final imprime os resultados no console. O resultado deve ser assim:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Instalar o R usando o Azure PowerShell
Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  O exemplo demonstra como instalar o Spark usando o Azure PowerShell. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Instalar o R usando o SDK do .NET
Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). O exemplo demonstra como instalar o Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Confira também
* [Instalar e usar R em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informações gerais sobre como criar clusters HDInsight
* [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script
* [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md)
* [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]: exemplo de Ação de Script sobre a instalação do Spark
* [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md): exemplo de Ação de Script sobre a instalação do Giraph
* [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md): exemplo de Ação de Script sobre a instalação do Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
