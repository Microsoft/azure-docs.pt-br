---
title: "Instalar ou atualizar o Mono no HDInsight – Azure | Microsoft Docs"
description: "Aprenda a usar uma versão específica do Mono com o cluster HDInsight. O Mono é usado para executar aplicativos do .NET em clusters HDInsight baseados no Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: fd284542e1de65f323f1e3a092689f847e025be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-or-update-mono-on-hdinsight"></a>Instalar ou atualizar Mono no HDInsight

Saiba como instalar uma versão específica do [Mono](https://www.mono-project.com) no HDInsight 3.4 ou superior.

O Mono é instalado no HDInsight 3.4 e superior e é usado para executar aplicativos do .NET. Para obter informações sobre a versão do Mono incluída com cada HDInsight, consulte [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md). Para instalar uma versão diferente no cluster, use a ação de script neste documento. 

## <a name="how-it-works"></a>Como ele funciona

Este script aceita o seguinte parâmetro:

* __Número da versão do Mono__: a versão do Mono a ser instalada. A versão deve estar disponível em [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

O script instala os seguintes pacotes do Mono:

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>O script

__Local do script__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Requisitos__:

* O script deve ser aplicado nos __nós de cabeçalho__ e nos __nós de trabalho__.

## <a name="to-use-the-script"></a>Para usar o script

Para obter informações sobre como usar esse script com o HDInsight, consulte o documento [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Você pode usar o script por meio do Portal do Azure, do Azure PowerShell ou da CLI do Azure.

Ao seguir o documento de ação do script, use o seguinte URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

> [!NOTE]
> Ao configurar o HDInsight com esse script, marque o script como __Persistente__. Essa configuração permite que o HDInsight aplique o script a nós de trabalho adicionados por meio de operações de colocação em escala.


## <a name="next-steps"></a>Próximas etapas

Você aprendeu como atualizar ou instalar uma versão específica do Mono no HDInsight. Para saber mais sobre como usar os aplicativos do .NET com o Mono no HDInsight, veja os seguintes documentos:

* [Usar o .NET para transmitir MapReduce no HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Usar o .NET com o Hive e o Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Desenvolver soluções do C# com o Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Migrar soluções .NET para o HDInsight baseado no Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Para saber mais sobre como usar as ações de script, confira [Personalizar clusters HDInsight com base em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)