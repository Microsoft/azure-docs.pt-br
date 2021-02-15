---
title: Habilitar a criação automática de tópico no Apache Kafka - Azure HDInsight
description: Saiba como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente. Você pode configurar o Kafka definindo `auto.create.topics.enable` como true por meio de Ambari. Ou durante a criação do cluster por meio de modelos do PowerShell ou do Resource Manager.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b416623d6637cfe8e2c1cd795dd62553f8c0aed4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933219"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente

Por padrão, Apache Kafka no HDInsight não habilita a criação automática de tópicos. Você pode habilitar a criação automática de tópico para clusters existentes usando o Apache Ambari. Você também pode habilitar a criação automática de tópico ao criar um novo cluster do Kafka usando um modelo do Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interface do usuário do Apache Ambari web

Para habilitar a criação automática de tópico em um cluster existente por meio da interface do usuário da Web do Ambari, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o cluster Kafka.

1. Em **painéis de cluster**, selecione **Ambari página inicial**.

    ![Imagem do portal com o painel do cluster selecionado](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Quando solicitado, autentique-se usando as credenciais de logon (administrador) do cluster. Em vez disso, você pode se conectar ao Amabri diretamente de `https://CLUSTERNAME.azurehdinsight.net/` onde `CLUSTERNAME` é o nome do seu cluster Kafka.

1. Selecione o serviço Kafka na lista à esquerda da página.

    ![Guia lista de serviços do Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Selecione Configurações no meio da página.

    ![Guia Configurações do serviço Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. No campo Filtrar, digite um valor de `auto.create`.

    ![Campo de filtro de pesquisa do Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Essa configuração filtra a lista de propriedades e exibe a `auto.create.topics.enable` configuração.

1. Altere o valor de `auto.create.topics.enable` para e, `true` em seguida, selecione **salvar**. Adicione uma observação e, em seguida, selecione **salvar** novamente.

    ![Imagem da entrada auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Selecione o serviço Kafka, __Reiniciar__ e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __confirmar reiniciar tudo__.

    ![' Apache Ambari reiniciar todos os afetados '](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Você também pode definir valores do Ambari por meio da API REST do Ambari. Isso geralmente é mais difícil, pois você precisa fazer várias chamadas REST para recuperar a configuração atual, modificá-la etc. Para obter mais informações, consulte o documento [gerenciar clusters HDInsight usando a API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Ao criar um cluster do Kafka usando um modelo do Azure Resource Manager, você pode definir diretamente `auto.create.topics.enable` adicionando-o em um `kafka-broker`. O snippet JSON a seguir demonstra como definir esse valor como `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como habilitar a criação automática de tópico para Apache Kafka no HDInsight. Para obter mais informações sobre como trabalhar com o Kafka, consulte os seguintes links:

* [Analisar logs do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Apache Kafka](apache-kafka-mirroring.md)
