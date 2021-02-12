---
title: Usar pool dedicado para executar tarefas tarefa
description: Configure um pool de computação dedicado (pool de agentes) no registro para executar uma tarefa de registro de contêiner do Azure.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920300"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Executar uma tarefa ACR em um pool de agentes dedicado

Configure um pool de VM gerenciado pelo Azure (*pool de agentes*) para habilitar a execução de suas [tarefas de registro de contêiner do Azure][acr-tasks] em um ambiente de computação dedicado. Depois de configurar um ou mais pools no registro, você pode escolher um pool para executar uma tarefa no lugar do ambiente de computação padrão do serviço.

Um pool de agentes fornece:

- **Suporte à rede virtual** -atribua um pool de agentes a uma vnet do Azure, fornecendo acesso aos recursos na VNet, como um registro de contêiner, um cofre de chaves ou um armazenamento.
- **Dimensionar conforme necessário** -aumente o número de instâncias em um pool de agentes para tarefas de computação intensiva ou dimensione para zero. A cobrança é baseada na alocação do pool. Para obter detalhes, consulte [preços](https://azure.microsoft.com/pricing/details/container-registry/).
- **Opções flexíveis** – escolha entre diferentes [camadas de pool](#pool-tiers) e opções de escala para atender às suas necessidades de carga de trabalho de tarefa.
- **Gerenciamento do Azure** -os pools de tarefas são corrigidos e mantidos pelo Azure, fornecendo alocação reservada sem a necessidade de manter as VMs individuais.

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre limites e camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure][acr-tiers].

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="preview-limitations"></a>Limitações de visualização

- Os pools de agente de tarefa atualmente dão suporte a nós do Linux. Atualmente, não há suporte para nós do Windows.
- Os pools de agente de tarefa estão disponíveis em versão prévia nas seguintes regiões: oeste dos EUA 2, Sul EUA Central, leste dos EUA 2, leste dos EUA, EUA Central, USGov Arizona, USGov Texas e USGov Virgínia.
- Para cada registro, a cota padrão total de vCPU (núcleo) é 16 para todos os pools de agente padrão e é 0 para pools de agentes isolados. Abra uma [solicitação de suporte][open-support-ticket] para alocação adicional.
- No momento, não é possível cancelar uma tarefa executada em um pool de agentes.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas de CLI do Azure neste artigo, CLI do Azure versão 2.3.1 ou posterior é necessária. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli]. Ou executá-la no [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se você ainda não tiver um registro de contêiner, [crie um][create-reg-cli] (camada Premium necessária) em uma região de visualização.

## <a name="pool-tiers"></a>Camadas de pool

As camadas do pool de agentes fornecem os seguintes recursos por instância no pool.

|Camada    | Tipo  |  CPU  |Memória (GB)  |
|---------|---------|---------|---------|
|S1     |  padrão    | 2       |    3     |
|S2     |  padrão    | 4       |    8     |
|S3     |  padrão    | 8       |   16     |
|I6     |  isolados    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Criar e gerenciar um pool de agente de tarefa

### <a name="set-default-registry-optional"></a>Definir registro padrão (opcional)

Para simplificar CLI do Azure comandos a seguir, defina o registro padrão executando o comando [AZ configure][az-configure] :

```azurecli
az configure --defaults acr=<registryName>
```

Os exemplos a seguir pressupõem que você definiu o registro padrão. Caso contrário, passe um `--registry <registryName>` parâmetro em cada `az acr` comando.

### <a name="create-agent-pool"></a>Criar pool de agentes

Crie um pool de agentes usando o comando [AZ ACR agentpool Create][az-acr-agentpool-create] . O exemplo a seguir cria um pool S2 de camada (4 CPU/instância). Por padrão, o pool contém 1 instância.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> A criação de um pool de agentes e de outras operações de gerenciamento de pool leva vários minutos para ser concluída.

### <a name="scale-pool"></a>Pool de dimensionamento

Dimensione o tamanho do pool para cima ou para baixo com o comando [AZ ACR agentpool Update][az-acr-agentpool-update] . O exemplo a seguir dimensiona o pool para 2 instâncias. Você pode dimensionar para 0 instâncias.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Criar pool em uma rede virtual

### <a name="add-firewall-rules"></a>Adicionar regras de firewall

Os pools de agente de tarefa exigem acesso aos seguintes serviços do Azure. As regras de firewall a seguir devem ser adicionadas a quaisquer grupos de segurança de rede ou rotas definidas pelo usuário existentes.

| Direção | Protocolo | Fonte         | Porta de origem | Destino          | Porta de destino | Usado    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureKeyVault        | 443       | Padrão |
| Saída  | TCP      | VirtualNetwork | Qualquer         | Armazenamento              | 443       | Padrão |
| Saída  | TCP      | VirtualNetwork | Qualquer         | EventHub             | 443       | Padrão |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureActiveDirectory | 443       | Padrão |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureMonitor         | 443       | Padrão |

> [!NOTE]
> Se suas tarefas exigirem recursos adicionais da Internet pública, adicione as regras correspondentes. Por exemplo, regras adicionais são necessárias para executar uma tarefa de Build do Docker que extrai as imagens base do Hub do Docker ou restaura um pacote NuGet.

### <a name="create-pool-in-vnet"></a>Criar pool na VNet

O exemplo a seguir cria um pool de agentes na sub-rede *mysubnet* da rede *myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Executar tarefa no pool de agentes

Os exemplos a seguir mostram como especificar um pool de agentes ao enfileirar uma tarefa.

> [!NOTE]
> Para usar um pool de agentes em uma tarefa ACR, verifique se o pool contém pelo menos 1 instância.
>

### <a name="quick-task"></a>Tarefa rápida

Enfileirar uma tarefa rápida no pool de agentes usando o comando [AZ ACR Build][az-acr-build] e passar o `--agent-pool` parâmetro:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Tarefa disparada automaticamente

Por exemplo, crie uma tarefa agendada no pool de agentes com [AZ ACR tarefa Create][az-acr-task-create], passando o `--agent-pool` parâmetro.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Para verificar a configuração da tarefa, execute a [tarefa AZ ACR Run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Status do pool de consulta

Para localizar o número de execuções atualmente agendadas no pool de agentes, execute [AZ ACR agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de builds de imagem de contêiner e manutenção na nuvem, confira a [série de tutoriais de tarefas de ACR](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
