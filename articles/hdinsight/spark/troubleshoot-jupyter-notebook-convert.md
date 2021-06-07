---
title: Não é possível criar Jupyter Notebook no Azure HDInsight
description: Saiba mais sobre as etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938666"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Não é possível criar Jupyter Notebook no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao iniciar um Jupyter Notebook, você receberá uma mensagem de erro que contém:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Causa

Incompatibilidade de versão.

## <a name="resolution"></a>Resolução

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra `_version.py` executando o seguinte comando:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Altere **5** para **4** para que a linha modificada seja exibida da seguinte maneira:

    ```python
    version_info = (4, 0, 3)
    ```

    Salve as alterações digitando **Ctrl + X**, **Y**, **Enter**.

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, em que `CLUSTERNAME` é o nome do cluster.

1. Reinicie o serviço Jupyter.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
