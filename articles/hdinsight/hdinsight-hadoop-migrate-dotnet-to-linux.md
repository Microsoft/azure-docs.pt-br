---
title: "Use .NET com Hadoop MapReduce no HDInsight baseado em Linux – Azure | Microsoft Docs"
description: Saiba como usar aplicativos .NET para streaming de MapReduce no HDInsight baseado em Linux.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: df931d0f76498506cfc946501e4d385d0dfead80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrar soluções .NET do HDInsight baseado em Windows para o HDInsight baseado em Linux

Clusters do HDInsight baseado em Linux usam [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .NET. Mono permite que você use componentes .NET como aplicativos MapReduce com HDInsight baseado em Linux. Neste documento, saiba como migrar soluções .NET criadas para clusters de HDInsight baseados no Windows para funcionar com Mono no HDInsight baseado em Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidade de Mono com .NET

O Mono versão 4.2.1 está incluído no HDInsight versão 3.5. Para obter mais informações sobre a versão de Mono incluída com o HDInsight, consulte [Versão de componente do HDInsight](hdinsight-component-versioning.md). Para instalar uma versão específica do Mono, consulte o documento [Instalar ou atualizar](hdinsight-hadoop-install-mono.md).

Para obter mais informações sobre a compatibilidade entre Mono e .NET, consulte o documento [Compatibilidade do Mono (http://www.mono-project.com/docs/about-mono/compatibility/)](http://www.mono-project.com/docs/about-mono/compatibility/).

> [!IMPORTANT]
> A estrutura SCP.NET é compatível com Mono. Para obter mais informações sobre como usar SCP.NET com Mono, consulte [Usar o Visual Studio para desenvolver topologias C# para Apache Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análise automatizada de portabilidade

O [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) pode ser usado para gerar um relatório de incompatibilidades entre seu aplicativo e Mono. Use as etapas a seguir para configurar o analisador para verificar seu aplicativo para portabilidade Mono:

1. Instalar o [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante a instalação, selecione a versão do Visual Studio a ser usada.

2. No Visual Studio 2015, selecione __Analisar__ > __Configurações do Portability Analyzer__e verifique se __4.5__ está marcado na seção __Mono__.

    ![4.5 marcado na seção Mono para as configurações do analisador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Clique em __OK__ para salvar a configuração.

3. Selecione __Analisar__ > __Analisar portabilidade do Assembly__. Selecione o assembly que contém sua solução e selecione __Abrir__ para iniciar a análise.

4. Quando a análise for concluída, selecione __Analisar__ > __Exibir relatórios de análise__. Em __Resultados da análise de portabilidade__, selecione __Abrir relatório__ para abrir um relatório.

    ![Caixa de diálogo de resultados do analisador de portabilidade](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> O analisador não pode capturar todos os problemas com sua solução. Por exemplo, um caminho de arquivo de `c:\temp\file.txt` é considerado OK se o Mono está em execução no Windows. O mesmo caminho não é válido em uma plataforma Linux.

## <a name="manual-portability-analysis"></a>Análise de portabilidade manual

Realizar uma auditoria manual do seu código usando as informações no documento [Portabilidade do aplicativo (http://www.mono-project.com/docs/getting-started/application-portability/)](http://www.mono-project.com/docs/getting-started/application-portability/).

## <a name="modify-and-build"></a>Modificar e criar

Você pode continuar a usar o Visual Studio para criar soluções .NET para HDInsight. No entanto, você deve garantir que o projeto esteja configurado para usar o .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implantar e testar

Depois de modificar a solução usando as recomendações do .NET Portability Analyzer ou de uma análise manual, teste-o com o HDInsight. O teste da solução em um cluster HDInsight baseado em Linux pode revelar problemas sutis que precisam ser corrigidos. Recomendamos que você habilite o registro em log adicional no seu aplicativo ao testá-lo.

Para obter mais informações sobre como acessar logs, veja os documentos a seguir:

* [Acessar logs do aplicativo YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Próximas etapas

* [Usar C# com MapReduce no HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Usar funções definidas pelo usuário do C# com Hive e Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desenvolver topologias C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md)