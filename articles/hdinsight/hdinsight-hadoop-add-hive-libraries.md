---
title: "Adicionar bibliotecas Hive durante a criação do cluster HDInsight – Azure | Microsoft Docs"
description: "Saiba como adicionar bibliotecas Hive (arquivos jar) a um cluster HDInsight durante a criação do cluster."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 90a1ea99cbba82b49a0ff6712bcaaa5dc814810e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas Hive personalizadas ao criar seu cluster HDInsight

Saiba como pré-carregar bibliotecas do Hive no HDInsight. Este documento contém informações sobre como usar uma Ação de script para pré-carregar bibliotecas durante a criação do cluster. As bibliotecas adicionadas usando as etapas deste documento estão disponíveis globalmente no Hive — não há necessidade de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como ele funciona

Ao criar um cluster, você pode usar uma Ação de script para modificar os nós do cluster à medida que eles são criados. O script neste documento aceita um único parâmetro, que é a localização das bibliotecas. Essa localização deve estar em uma conta de Armazenamento do Azure e as bibliotecas devem ser armazenadas como arquivos jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` nos nós de cabeçalho e de trabalho e adiciona-os à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo `hive-env.sh` com o local dos arquivos.

> [!NOTE]
> Usar as ações de script neste artigo disponibiliza as bibliotecas nos seguintes cenários:
>
> * **HDInsight baseado em Linux** — ao usar um cliente Hive, **WebHCat** e **HiveServer2**.
> * **HDInsight baseado em Windows** — ao usar cliente Hive e **WebHCat**.

## <a name="the-script"></a>O script

**Local do script**

Para os **clusters baseados no Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para os **clusters baseados no Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Requisitos**

* Os scripts devem ser aplicados nos**Nós do cabeçalho** e nos **Nós de trabalho**.

* Os jars que você deseja instalar devem estar armazenados no Armazenamento de Blobs do Azure em um **único contêiner**.

* A conta de armazenamento contendo a biblioteca de arquivos jar **deve** ser vinculada ao cluster HDInsight durante a criação. Esta deve ser a conta de armazenamento padrão ou uma conta adicionada por meio da __configuração opcional__.

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a Ação de Script. Por exemplo, se os jars estivessem armazenados em um contêiner denominado **libs** em uma conta de armazenamento denominada **mystorage**, o parâmetro seria **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Este documento supõe que você já criou uma conta de armazenamento, um contêiner de blobs e carregou os arquivos nele.
  >
  > Se você ainda não criou uma conta de armazenamento, faça isso usando o [portal do Azure](https://portal.azure.com). Lá, você pode usar um utilitário como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar um contêiner na conta e carregar arquivo nele.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

> [!NOTE]
> As etapas a seguir criam um cluster HDInsight baseado no Linux. Para criar um cluster baseado no Windows, escolha **Windows** como o SO do cluster ao criar o cluster e use o script do Windows (PowerShell) em vez do script bash.
>
> Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie provisionando um cluster com as etapas em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento.

2. Na seção **Configuração Opcional**, escolha **Ações de Script** e forneça as seguintes informações:

   * **NOME**: insira um nome amigável para a ação de script.

   * **URI do SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **CABEÇALHO**: marque esta opção.

   * **TRABALHO**: marque esta opção.

   * **ZOOKEEPER**: deixe essa opção em branco.

   * **PARÂMETROS**: insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, **wasb://libs@mystorage.blob.core.windows.net/**.

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração.

4. Na seção **Configuração Opcional**, selecione **Contas de Armazenamento Vinculadas** e selecione o link **Adicionar uma chave de armazenamento**. Selecione a conta de armazenamento que contém os jars. Em seguida, use os botões **selecionar** para salvar as configurações e retornar para **Configuração Opcional**.

5. Para salvar a configuração opcional, use o botão **Selecionar** na parte inferior da seção **Configuração Opcional**.

6. Continue a provisionar o cluster como descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Assim que a criação do cluster for concluída, você poderá usar os jars adicionados do Hive por meio desse script sem precisar usar a instrução `ADD JAR`.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como trabalhar com o Hive, confira [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
