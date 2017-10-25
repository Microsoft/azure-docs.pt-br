---
title: "Gerenciar clusters Hadoop usando a CLI do Azure – Azure HDInsight | Microsoft Docs"
description: Saiba como usar a Interface de linha de comando do Azure para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure funciona no Windows, Mac e Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: cc1b78e430ab46f367fafcc16e1240aa76a9ba64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gerenciar clusters Hadoop no HDInsight usando a CLI do Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar a [Interface de Linha de Comando do Azure](../cli-install-nodejs.md) para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux. Atualmente, o HDInsight não dá suporte à [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

Este artigo aborda apenas o uso da CLI do Azure com o HDInsight. Para obter um guia geral sobre como usar a CLI do Azure, confira [Instalar e configurar a CLI do Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) para obter informações de instalação e configuração.
* **Conecte-se ao Azure**usando o seguinte comando:

    ```cli
    azure login
    ```
  
    Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).
* **Alterne para o modo Gerenciador de Recursos do Azure**usando o seguinte comando:
  
    ```cli
    azure config mode arm
    ```

Para obter ajuda, use a opção **-h** .  Por exemplo:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Criar clusters com a CLI
Consulte [Criar clusters no HDInsight usando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Modo de exibição de linha de comando de lista de clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Excluir clusters
Use o seguinte comando para excluir um cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
Para alterar o tamanho do cluster Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Habilitar/desabilitar o acesso HTTP para um cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Habilitar/desabilitar o acesso RDP para um cluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar clusters HDInsight usando o Azure PowerShell][hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Como usar a CLI do Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listar e mostrar clusters"
