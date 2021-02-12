---
title: Criar treinamento & implantar computações (estúdio)
titleSuffix: Azure Machine Learning
description: Use o Studio para criar recursos de computação de treinamento e implantação (destinos de computação) para o aprendizado de máquina
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: ab7a74166e85f2ba9fd73e7323cf9cd200cf32e4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031021"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Criar destinos de computação para treinamento e implantação de modelo no Azure Machine Learning Studio

Neste artigo, saiba como criar e gerenciar destinos de computação no Azure Machine Studio.  Você também pode criar e gerenciar destinos de computação com:

* SDK do Azure Machine Learning Learning ou extensão da CLI para Azure Machine Learning
  * [Instância de computação](how-to-create-manage-compute-instance.md)
  * [Cluster de computação](how-to-create-attach-compute-cluster.md)
  * [Cluster do serviço kubernetes do Azure](how-to-create-attach-kubernetes.md)
  * [Outros recursos de computação](how-to-attach-compute-targets.md)
* A [extensão de vs Code](how-to-manage-resources-vscode.md#compute-clusters) para Azure Machine Learning.


## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>O que é um destino de computação? 

Com o Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente denominados [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para a implantação de modelo conforme descrito em ["em que local e como implantar seus modelos"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Exibir destinos de computação

Para ver todos os destinos de computação para seu espaço de trabalho, use as seguintes etapas:

1. Link para o [estúdio do Azure Machine Learning](https://ml.azure.com).
 
1. Em __gerenciar__, selecione __computação__.

1. Selecione as guias na parte superior para mostrar cada tipo de destino de computação.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Exibir a lista de destinos de computação":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Criar destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação:

1. Selecione a guia na parte superior correspondente ao tipo de computação que você criará.

1. Se você não tiver destinos de computação, selecione  **criar** no meio da página.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Criar destino de computação":::

1. Se você vir uma lista de recursos de computação, selecione **+ novo** acima da lista.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Selecionar novo":::


1. Preencha o formulário para o tipo de computação:

  * [Instância de computação](#compute-instance)
  * [Clusters de cálculo](#amlcompute)
  * [Clusters de inferência](#inference-clusters)
  * [Computação anexada](#attached-compute)

1. Selecione __Criar__.

1. Exiba o status da operação de criação, selecionando o destino de computação na lista:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Exibir o status de computação de uma lista":::


### <a name="compute-instance"></a>Instância de computação

Use as [etapas acima](#portal-create) para criar a instância de computação.  Em seguida, preencha o formulário da seguinte maneira:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Criar uma nova instância de computação":::


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é obrigatório e deve ter entre 3 e 24 caracteres.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** caractere.</li><li>O nome deve começar com uma letra</li><li>O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo</li><li>Se o **-**  caractere for usado, ele precisará ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual com suporte podem ser restritos em sua região. Verificar a [lista de disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Habilitar/desabilitar o acesso SSH     |   O acesso SSH é desabilitado por padrão.  O acesso SSH não pode ser. alterado após a criação. Certifique-se de habilitar o acesso se você planeja depurar interativamente com [vs Code remoto](how-to-set-up-vs-code-remote.md)   |
|Configurações avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de recursos**, a **rede virtual** e a **sub-rede** para criar a instância de computação dentro de uma vnet (rede virtual) do Azure. Para obter mais informações, consulte estes [requisitos de rede](./how-to-secure-training-vnet.md) para vnet.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Clusters de computação

Crie um cluster de computação de nó único ou de vários nós para suas cargas de trabalho de treinamento, inferência de lote ou reforço de aprendizado. Use as [etapas acima](#portal-create) para criar o cluster de computação.  Em seguida, preencha o formulário da seguinte maneira:


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é obrigatório e deve ter entre 3 e 24 caracteres.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** caractere.</li><li>O nome deve começar com uma letra</li><li>O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo</li><li>Se o **-**  caractere for usado, ele precisará ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Prioridade da máquina virtual | Escolha prioridade **dedicada** ou **baixa**.  As máquinas virtuais de baixa prioridade são mais baratas, mas não garantem os nós de computação. Seu trabalho pode ser admitido.
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual com suporte podem ser restritos em sua região. Verificar a [lista de disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Número mínimo de nós | Número mínimo de nós que você deseja provisionar. Se você quiser um número dedicado de nós, defina essa contagem aqui. Economize dinheiro definindo o mínimo como 0, portanto, você não pagará por nenhum nó quando o cluster estiver ocioso. |
|Número máximo de nós | Número máximo de nós que você deseja provisionar. A computação fará o dimensionamento automático para um máximo dessa contagem de nós quando um trabalho for enviado. |
|Configurações avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de recursos**, a **rede virtual** e a **sub-rede** para criar a instância de computação dentro de uma vnet (rede virtual) do Azure. Para obter mais informações, consulte estes [requisitos de rede](./how-to-secure-training-vnet.md) para vnet.   Anexar também [identidades gerenciadas](#managed-identity) para conceder acesso a recursos     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Configurar identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Durante a criação do cluster ou ao editar detalhes do cluster de cálculo, nas **Configurações avançadas**, alterne **atribuir uma identidade gerenciada** e especifique uma identidade atribuída pelo sistema ou identidade atribuída pelo usuário.

#### <a name="managed-identity-usage"></a>Uso de identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Clusters de inferência

> [!IMPORTANT]
> O uso do serviço kubernetes do Azure com Azure Machine Learning tem várias opções de configuração. Alguns cenários, como rede, exigem configuração e configuração adicionais. Para obter mais informações sobre como usar o AKS com o Azure ML, consulte [criar e anexar um cluster do serviço kubernetes do Azure](how-to-create-attach-kubernetes.md).

Criar ou anexar um cluster AKS (serviço de kubernetes do Azure) para inferência de grande escala. Use as [etapas acima](#portal-create) para criar o cluster AKs.  Em seguida, preencha o formulário da seguinte maneira:


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é obrigatório. O nome deve ter entre 2 e 16 caracteres. </li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** caractere.</li><li>O nome deve começar com uma letra</li><li>O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo</li><li>Se o **-**  caractere for usado, ele precisará ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Serviço de Kubernetes | Selecione **criar novo** e preencha o restante do formulário.  Ou selecione **usar existente** e, em seguida, selecione um cluster AKs existente da sua assinatura.
|Região |  Selecione a região em que o cluster será criado |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual com suporte podem ser restritos em sua região. Verificar a [lista de disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Finalidade do cluster  | Selecionar **produção** ou **desenvolvimento-teste** |
|Número de nós | O número de nós multiplicado pelo número de núcleos da máquina virtual (vCPUs) deve ser maior ou igual a 12. |
| Configuração de rede | Selecione **avançado** para criar a computação em uma rede virtual existente. Para obter mais informações sobre o AKS em uma rede virtual, consulte [isolamento de rede durante o treinamento e a inferência com pontos de extremidade privados e redes virtuais](./how-to-secure-inferencing-vnet.md). |
| Habilitar configuração de SSL | Use isso para configurar o certificado SSL na computação |

### <a name="attached-compute"></a>Computação anexada

Para usar destinos de computação criados fora do workspace do Azure Machine Learning, é necessário anexá-los. Anexar um destino de computação o disponibiliza para seu workspace.  Use a **computação anexada** para anexar um destino de computação para **treinamento**.  Use **clusters de inferência** para anexar um cluster AKs para **inferência**.

Use as [etapas acima](#portal-create) para anexar uma computação.  Em seguida, preencha o formulário da seguinte maneira:

1. Insira um nome para o destino de computação. 
1. Selecione o tipo de computação a ser anexado. Nem todos os tipos de computação podem ser anexados no estúdio do Azure Machine Learning. Os tipos de computação que podem ser anexados para treinamento são:
    * VM remota
    * Azure Databricks (para uso em pipelines de aprendizado de máquina)
    * Azure Data Lake Analytics (para uso em pipelines de aprendizado de máquina)
    * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que as senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH contam com as assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Criar e usar chaves SSH no Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Selecionar __Anexar__. 


## <a name="next-steps"></a>Próximas etapas

Depois que um destino é criado e anexado ao seu espaço de trabalho, você o utiliza em sua [configuração de execução](how-to-set-up-training-targets.md) com um `ComputeTarget` objeto:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Use o recurso de computação para [Enviar uma execução de treinamento](how-to-set-up-training-targets.md).
* [Tutorial: treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar hiperparâmetros de modo eficiente](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](./how-to-network-security-overview.md)