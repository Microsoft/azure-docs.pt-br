---
title: Erro 404 Jupyter-"bloqueando a API de origem cruzada"-Azure HDInsight
description: Erro do Jupyter Server 404 "não encontrado" devido a "bloqueio da API de origem cruzada" no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 27cd3aff859fd46679679ac12d3acc03fa6da158
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929454"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: erro do Jupyter Server 404 "não encontrado" devido a "bloquear API de origem cruzada" no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao acessar o serviço Jupyter no HDInsight, você verá uma caixa de erro dizendo "não encontrado". Se você verificar os logs do Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Você também pode ver um endereço IP no campo "origem" no log do Jupyter.

## <a name="cause"></a>Causa

Esse erro pode ser causado por algumas coisas:

- Se você tiver configurado regras de NSG (grupo de segurança de rede) para restringir o acesso ao cluster. Restringir o acesso com regras NSG ainda permitirá que você acesse diretamente o Apache Ambari e outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao acessar o Jupyter, você pode ver um erro 404 "não encontrado".

- Se você tiver dado ao seu gateway do HDInsight um nome DNS personalizado diferente do padrão `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Resolução

1. Modifique os arquivos jupyter.py nestes dois locais:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Localize a linha que diz: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` e altere-a para: `NotebookApp.allow_origin='\"*\"'` .

1. Reinicie o serviço Jupyter de Ambari.

1. Digitar `ps aux | grep jupyter` no prompt de comando deve mostrar que ele permite que qualquer URL se conecte a ele.

Isso é menos seguro do que a configuração que já tínhamos em vigor. Mas é presumido que o acesso ao cluster seja restrito e que um de fora tenha permissão para se conectar ao cluster, pois temos NSG em vigor.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]