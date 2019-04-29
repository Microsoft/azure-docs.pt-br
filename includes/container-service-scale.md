---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 2ed74a4ba19af3a441bcf26a48890f033e6c365f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509829"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Depois de [implantar um cluster do Serviço de Contêiner do Azure](../articles/container-service/dcos-swarm/container-service-deployment.md), talvez você precise alterar o número de nós do agente. Por exemplo, mais agentes podem ser necessários para que você possa executar mais aplicativos ou instâncias de contêiner. 

É possível alterar o número de nós de agente em um cluster DC/OS, Docker Swarm ou Kubernetes usando o Portal do Azure ou a CLI do Azure. 

## <a name="scale-with-the-azure-portal"></a>Dimensionar com o Portal do Azure

1. No [Portal do Azure](https://portal.azure.com), procure **Serviços de contêiner** e clique no serviço de contêiner que você quer modificar.
2. Na folha **Serviço de contêiner**, clique em **Agentes**.
3. Em **Contagem de VM**, insira o número desejado de nós de agentes.

    ![Dimensionar um pool no portal](./media/container-service-scale/container-service-scale-portal.png)

4. Para salvar a configuração, clique em **Salvar**.

## <a name="scale-with-the-azure-cli"></a>Dimensionar com a CLI do Azure

Verifique se você [instalou](/cli/azure/install-az-cli2) a CLI do Azure mais recente e conectou-se a uma conta do Azure (`az login`).

### <a name="see-the-current-agent-count"></a>Conferir a conta do agente atual
Para ver o número de agentes atualmente no cluster, execute o comando `az acs show`. Isso mostra a configuração do cluster. Por exemplo, o seguinte comando mostra a configuração do serviço de contêiner chamado `containerservice-myACSName` no grupo de recursos `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

O comando retorna o número de agentes no valor `Count` em `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Usar o comando az acs scale
Para alterar o número de nós de agente, execute o comando `az acs scale` e forneça o **grupo de recursos**, o **nome do serviço de contêiner** e a **nova contagem de agente** desejada. Ao usar um número menor ou maior, você pode reduzir e escalar verticalmente, respectivamente.

Por exemplo, para alterar o número de agentes no cluster anterior para 10, digite o seguinte comando:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

A CLI do Azure retorna uma cadeia de caracteres JSON que representa a nova configuração do serviço de contêiner, incluindo a nova contagem de agentes.

Para obter mais opções de comando, execute `az acs scale --help`.

## <a name="scaling-considerations"></a>Considerações de dimensionamento

* O número de nós de agente deve estar entre 1 e 100, inclusive. 

* A cota de núcleos pode limitar o número de nós de agente em um cluster.

* As operações de dimensionamento do nó de agente são aplicadas a um conjunto de dimensionamento de máquinas virtuais do Azure que contém o pool de agentes. Em um cluster DC/OS, somente nós de agente no pool privado são dimensionados pelas operações mostradas neste artigo.

* Dependendo do orquestrador que você implanta no cluster, é possível dimensionar separadamente o número de instâncias de um contêiner em execução no cluster. Por exemplo, em um cluster DC/OS, use o [Marathon UI](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) para alterar o número de instâncias de um aplicativo contêiner.


## <a name="next-steps"></a>Próximos passos
* Consulte [mais exemplos](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) de como usar os comandos da CLI do Azure com o Serviço de Contêiner do Azure.
* Saiba mais sobre os [pools de agentes DC/OS](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) no Serviço de Contêiner do Azure.

