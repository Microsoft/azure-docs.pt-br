---
title: Criar clusters de cálculo
titleSuffix: Azure Machine Learning
description: Saiba como criar clusters de computação em seu espaço de trabalho do Azure Machine Learning. Use o cluster de computação como um destino de computação para treinamento ou inferência.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: c11176f0c7760e76b755406bda96b72b302f8857
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506932"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de computação do Azure Machine Learning

Saiba como criar e gerenciar um [cluster de cálculo](concept-compute-target.md#azure-machine-learning-compute-managed) em seu espaço de trabalho do Azure Machine Learning.

Você pode usar Azure Machine Learning cluster de computação para distribuir um processo de inferência de treinamento ou de lote em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](../virtual-machines/sizes-gpu.md). 

Neste artigo, aprenda a:

* Criar um cluster de cálculo
* Reduzir o custo do cluster de computação
* Configurar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

* A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>O que é um cluster de computação?

Azure Machine Learning cluster de computação é uma infraestrutura de computação gerenciada que permite criar facilmente uma computação de vários nós ou um único nó. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma Rede Virtual do Azure. A computação é executada em um ambiente em contêineres, empacotando as dependências do modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

Os clusters de computação podem executar trabalhos com segurança em um [ambiente de rede virtual](how-to-secure-training-vnet.md), sem exigir que as empresas abram portas SSH. O trabalho é executado em um ambiente em contêiner e empacota suas dependências de modelo em um contêiner do Docker. 

## <a name="limitations"></a>Limitações

* Não **crie vários anexos simultâneos para a mesma computação** do seu espaço de trabalho. Por exemplo, anexar um cluster de computação a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.

    Se você quiser anexar novamente um destino de computação, por exemplo, para alterar as definições de configuração de cluster, primeiro deverá remover o anexo existente.

* Alguns dos cenários listados neste documento são marcados como __Visualização__. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](how-to-manage-quotas.md).

* O Azure permite que você coloque _bloqueios_ em recursos, para que eles não possam ser excluídos ou sejam somente leitura. __Não aplique bloqueios de recurso ao grupo de recursos que contém seu espaço de trabalho__. Aplicar um bloqueio ao grupo de recursos que contém o espaço de trabalho impedirá operações de dimensionamento para clusters de computação do Azure ML. Para obter mais informações sobre como bloquear recursos, consulte [Bloquear recursos para evitar alterações inesperadas](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Os clusters geralmente podem ser escalados verticalmente para até 100 nós, desde que você tenha cota suficiente para o número de núcleos necessários. Por padrão, os clusters são configurados com a comunicação entre nós habilitada entre os nós do cluster para dar suporte a trabalhos de MPI, por exemplo. No entanto, você pode dimensionar seus clusters para milhares de nós simplesmente [gerando um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)e solicitando que o liste sua assinatura, ou espaço de trabalho, ou um cluster específico para desabilitar a comunicação entre nós. 


## <a name="create"></a>Criar

**Tempo estimado**: aproximadamente 5 minutos.

Uma Computação do Azure Machine Learning pode ser reutilizada entre execuções. A computação pode ser compartilhada com outros usuários no espaço de trabalho e é mantida entre as execuções, escalando ou reduzindo vertical e automaticamente os nós com base no número de execuções enviadas e o max_nodes definido no cluster. A configuração min_nodes controla os nós mínimos disponíveis.

Os núcleos dedicados por região por cota de família de VM e cota regional total, que se aplicam à criação de cluster de computação, são unificados e compartilhados com Azure Machine Learning cota de instância de computação de treinamento. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

A computação é reduzida automaticamente até zero nó quando não é usada.   VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
# <a name="python"></a>[Python](#tab/python)

Para criar um recurso persistente de Computação do Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. 
    
* **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
* **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.  Veja [AmlCompute classe](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) para obter detalhes.


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Para obter mais informações, consulte [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

# <a name="studio"></a>[Estúdio](#tab/azure-studio)

Para obter informações sobre como criar um cluster de computação no estúdio, consulte [criar destinos de computação no Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Reduzir o custo do cluster de computação

Você também pode optar por usar [VMs de baixa prioridade](concept-plan-manage-cost.md#low-pri-vm) para executar algumas ou todas as suas cargas de trabalho. Essas VMs não têm disponibilidade garantida e podem ser substituídas enquanto estiverem em uso. Você precisará reiniciar um trabalho admitido. 

Use qualquer uma dessas maneiras para especificar uma VM de baixa prioridade:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Defina `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Estúdio](#tab/azure-studio)

No estúdio, escolha **baixa prioridade** ao criar uma VM.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurar identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Configure a identidade gerenciada na sua configuração de provisionamento:  

    * Identidade gerenciada atribuída ao sistema:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Identidade gerenciada atribuída ao usuário:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Adicionar identidade gerenciada a um cluster de computação existente 
    
    * Identidade gerenciada atribuída ao sistema:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Identidade gerenciada atribuída ao usuário:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

* Criar um novo cluster de computação gerenciado com identidade gerenciada

  * Identidade gerenciada atribuída pelo usuário

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Identidade gerenciada atribuída pelo sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Adicione uma identidade gerenciada a um cluster existente:

    * Identidade gerenciada atribuída pelo usuário
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Identidade gerenciada atribuída pelo sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Estúdio](#tab/azure-studio)

Consulte [Configurar a identidade gerenciada no estúdio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Uso de identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Solução de problemas

Há uma chance de que alguns usuários que criaram seu espaço de trabalho Azure Machine Learning do portal do Azure antes da versão GA não possam criar AmlCompute nesse espaço de trabalho. Você pode gerar uma solicitação de suporte em relação ao serviço ou criar um novo espaço de trabalho por meio do portal ou do SDK para desbloquear-se imediatamente.

Se o cluster de computação Azure Machine Learning aparecer preso no redimensionamento (0-> 0) para o estado do nó, isso pode ser causado por bloqueios de recursos do Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Próximas etapas

Use o cluster de computação para:

* [Enviar uma execução de treinamento](how-to-set-up-training-targets.md) 
* [Executar inferência de lote](./tutorial-pipeline-batch-scoring-classification.md).
