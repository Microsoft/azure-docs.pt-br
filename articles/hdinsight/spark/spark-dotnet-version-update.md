---
title: Atualizando o .NET para Apache Spark para a versão v 1.0 no HDI
description: Saiba mais sobre como atualizar o .NET para Apache Spark versão para 1,0 no HDI e como isso afeta seus códigos e clusters existentes.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788122"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Atualizando o .NET para Apache Spark para a versão v 1.0 no HDInsight

Este documento fala sobre a primeira versão principal do [.net para Apache Spark](https://github.com/dotnet/spark)e como ele pode afetar seus pipelines de produção atuais em clusters HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>Sobre o .NET para Apache Spark versão 1.0.0

Esta é a primeira [versão oficial principal](https://github.com/dotnet/spark/releases/tag/v1.0.0) do .net para Apache Spark e fornece a integridade do dataframe para o Spark 2.4. x, bem como o Spark 3.0. x, juntamente com outros recursos. Para obter uma lista completa de todos os recursos, melhorias e correções de bugs, consulte as [notas de versão do v 1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)oficial.
Outra coisa importante a observar é que essa versão **não** é compatível com as versões anteriores do `Microsoft.Spark` e do `Microsoft.Spark.Worker` . Confira o [Guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) se você estiver planejando atualizar seu .net para Apache Spark aplicativo para ser compatível com o v 1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Usando o .NET para Apache Spark v 1.0 no HDInsight

Embora os clusters HDI atuais não sejam afetados (ou seja, eles ainda terão a mesma versão que antes), os clusters HDI recém-criados terão essa versão mais recente do v 1.0.0 do .NET para Apache Spark. O que isso significa se:

- **Você tem um cluster HDI mais antigo**: se quiser atualizar seu aplicativo .net do Spark para v 1.0.0 (recomendado), você precisará atualizar a `Microsoft.Spark.Worker` versão no cluster do HDI. Para obter mais informações, consulte a [seção alterando versões do .net para Apache Spark no cluster HDI](#changing-net-for-apache-spark-version-on-hdinsight).
Se você não quiser atualizar a versão atual do .NET para Apache Spark em seu aplicativo, nenhuma etapa adicional será necessária.  

- **Você tem um novo cluster HDI**: se desejar atualizar seu aplicativo .net do Spark para o v 1.0.0 (recomendado), nenhuma etapa será necessária para alterar o trabalho no HDI, no entanto, você precisará consultar o [Guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) para entender as etapas necessárias para atualizar seu código e pipelines.
Se você não quiser alterar a versão atual do .NET para Apache Spark em seu aplicativo, precisaria alterar a versão no cluster HDI de v 1.0 (padrão em novos clusters) para qualquer versão que estiver usando. Para obter mais informações, consulte a [seção alterando versões do .net para Apache Spark no cluster HDI](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Alterando o .NET para a versão Apache Spark no HDInsight

### <a name="deploy-microsoftsparkworker"></a>Implantar o Microsoft.Spark.Worker

`Microsoft.Spark.Worker` é um componente de back-end que reside nos nós de trabalho individuais do seu cluster do Spark. Quando você deseja executar uma UDF em C# (função definida pelo usuário), o Spark precisa entender como iniciar o .NET CLR para executar esse UDF. `Microsoft.Spark.Worker` fornece uma coleção de classes para Spark que habilitam essa funcionalidade. Selecione a versão de trabalho dependendo da versão do .NET para Apache Spark que você deseja implantar no cluster HDI.

1. Baixe a versão Linux do Microsoft. Spark. Worker de sua versão específica. Por exemplo, se desejar `.NET for Apache Spark v1.0.0` , você baixará [Microsoft. Spark. Worker. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Baixe o script [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) para instalar os binários de trabalho baixados na etapa 1 para todos os nós de trabalho do seu cluster HDI.  

3. Carregue os arquivos mencionados acima na conta de armazenamento do Azure à qual seu cluster tem acesso. Você pode consultar [o artigo sobre a implantação do .net para Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) para obter mais detalhes.

4. Execute o `install-worker.sh` script em todos os nós de trabalho do cluster, usando as ações de script. Consulte [o artigo sobre a implantação do .net para Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) para obter mais informações.

### <a name="update-your-application-to-use-specific-version"></a>Atualizar seu aplicativo para usar uma versão específica

Você pode atualizar seu aplicativo .NET para Apache Spark para usar uma versão específica escolhendo a versão necessária do [pacote NuGet do Microsoft. Spark](https://www.nuget.org/packages/Microsoft.Spark/) em seu projeto. Certifique-se de conferir as notas de versão da versão específica e o [Guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , conforme mencionado acima, se optar por atualizar seu aplicativo para o v 1.0.0.

## <a name="faqs"></a>Perguntas frequentes

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Meu cluster HDI existente com a versão < 1.0.0 começará a falhar com a nova versão?

Os clusters HDI existentes continuarão a ter a mesma versão anterior para .NET para Apache Spark e seu aplicativo existente (com a versão anterior do Spark .NET) não será afetado.

## <a name="next-steps"></a>Próximas etapas

[Implantar seu aplicativo .NET para Apache Spark no HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)