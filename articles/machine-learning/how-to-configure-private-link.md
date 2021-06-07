---
title: Configurar um ponto de extremidade privado
titleSuffix: Azure Machine Learning
description: Use o link privado do Azure para acessar com segurança seu espaço de trabalho do Azure Machine Learning de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 6fd497e0bc0fd282d57779c483f1e39e8f5ab60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505470"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configurar o link privado do Azure para um espaço de trabalho Azure Machine Learning

Neste documento, você aprenderá a usar o link privado do Azure com seu espaço de trabalho Azure Machine Learning. Para obter informações sobre como criar uma rede virtual para Azure Machine Learning, consulte [visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

O link privado do Azure permite que você se conecte ao seu espaço de trabalho usando um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados na sua rede virtual. Você pode, então, limitar o acesso ao seu espaço de trabalho para ocorrer somente nos endereços IP privados. O link privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [Link Privado do Azure](../private-link/private-link-overview.md).

> [!IMPORTANT]
> O link privado do Azure não afeta o plano de controle do Azure (operações de gerenciamento), como excluir o espaço de trabalho ou gerenciar recursos de computação. Por exemplo, criando, atualizando ou excluindo um destino de computação. Essas operações são executadas pela Internet pública normalmente. Operações de plano de dados, como usar o Azure Machine Learning Studio, APIs (incluindo pipelines publicados) ou o SDK usam o ponto de extremidade privado.
>
> Você pode encontrar problemas ao tentar acessar o ponto de extremidade privado para seu espaço de trabalho se você estiver usando o Mozilla Firefox. Esse problema pode estar relacionado ao DNS sobre HTTPS no Mozilla. É recomendável usar o Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="prerequisites"></a>Pré-requisitos

* Se você planeja usar um espaço de trabalho habilitado para link privado com uma chave gerenciada pelo cliente, você deve solicitar esse recurso usando um tíquete de suporte. Para obter mais informações, consulte [gerenciar e aumentar cotas](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

* Você deve ter uma rede virtual existente para criar o ponto de extremidade privado no. Você também deve [desabilitar as políticas de rede para pontos de extremidade privados](../private-link/disable-private-endpoint-network-policy.md) antes de adicionar o ponto final privado.
## <a name="limitations"></a>Limitações

* O uso de um espaço de trabalho Azure Machine Learning com link privado não está disponível nas regiões do Azure governamental.
* Se você habilitar o acesso público para um espaço de trabalho protegido com o link privado e usar o Azure Machine Learning Studio pela Internet pública, alguns recursos, como o designer, poderão falhar ao acessar seus dados. Esse problema ocorre quando os dados são armazenados em um serviço protegido por trás da VNet. Por exemplo, uma conta de armazenamento do Azure.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que usa um ponto de extremidade privado

Use um dos métodos a seguir para criar um espaço de trabalho com um ponto de extremidade privado. Cada um desses métodos __requer uma rede virtual existente__:

> [!TIP]
> Se você quiser criar um espaço de trabalho, um ponto de extremidade privado e uma rede virtual ao mesmo tempo, consulte [usar um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

O SDK do Azure Machine Learning Python fornece a classe [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , que pode ser usada com [Workspace. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para criar um espaço de trabalho com um ponto de extremidade privado. Essa classe requer uma rede virtual existente.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão de CLI do Azure para Machine Learning](reference-azure-machine-learning-cli.md) fornece o comando [AZ ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_create) . Os seguintes parâmetros para este comando podem ser usados para criar um espaço de trabalho com uma rede privada, mas requer uma rede virtual existente:

* `--pe-name`: O nome do ponto de extremidade privado que é criado.
* `--pe-auto-approval`: Se as conexões de ponto de extremidade privado com o espaço de trabalho devem ser aprovadas automaticamente.
* `--pe-resource-group`: O grupo de recursos para o qual criar o ponto de extremidade privado. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente na qual criar o ponto de extremidade privado.
* `--pe-subnet-name`: O nome da sub-rede para a qual criar o ponto de extremidade privado. O valor padrão é `default`.

Esses parâmetros são além de outros parâmetros necessários para o comando Create. Por exemplo, o comando a seguir cria um novo espaço de trabalho na região oeste dos EUA, usando um grupo de recursos existente e VNet:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

A guia __rede__ no Azure Machine Learning Studio permite que você configure um ponto de extremidade privado. No entanto, ele requer uma rede virtual existente. Para obter mais informações, consulte [criar espaços de trabalho no portal](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Adicionar um ponto de extremidade privado a um espaço de trabalho

Use um dos métodos a seguir para adicionar um ponto de extremidade privado a um espaço de trabalho existente:

> [!WARNING]
>
> Se você tiver destinos de computação existentes associados a este espaço de trabalho e eles não estiverem atrás da mesma rede virtual, o ponto de extremidade privado será criado no, eles não funcionarão.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Para obter mais informações sobre as classes e os métodos usados neste exemplo, consulte [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) e [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão de CLI do Azure para Machine Learning](reference-azure-machine-learning-cli.md) fornece o comando [AZ ml Workspace Private-Endpoint Add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) .

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

No espaço de trabalho Azure Machine Learning no portal, selecione __conexões de ponto de extremidade privadas__ e, em seguida, selecione __+ ponto de extremidade privado__. Use os campos para criar um novo ponto de extremidade privado.

* Ao selecionar a __região__, selecione a mesma região que sua rede virtual. 
* Ao selecionar __tipo de recurso__, use __Microsoft. MachineLearningServices/Workspaces__. 
* Defina o __recurso__ para o nome do espaço de trabalho.

Por fim, selecione __criar__ para criar o ponto de extremidade privado.

---

## <a name="remove-a-private-endpoint"></a>Remover um ponto de extremidade privado

Use um dos seguintes métodos para remover um ponto de extremidade privado de um espaço de trabalho:

# <a name="python"></a>[Python](#tab/python)

Use [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) para remover um ponto de extremidade privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão de CLI do Azure para Machine Learning](reference-azure-machine-learning-cli.md) fornece o comando [AZ ml Workspace Private-Endpoint Delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

No espaço de trabalho Azure Machine Learning no portal, selecione __conexões de ponto de extremidade privadas__ e, em seguida, selecione o ponto de extremidade que você deseja remover. Por fim, selecione __remover__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho em um ponto de extremidade privado

Como a comunicação com o espaço de trabalho só é permitida a partir da rede virtual, todos os ambientes de desenvolvimento que usam o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar a interrupção temporária da conectividade, a Microsoft recomenda liberar o cache DNS em computadores que se conectam ao espaço de trabalho depois de habilitar o link privado. 

Para obter informações sobre máquinas virtuais do Azure, consulte a [documentação sobre máquinas virtuais](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Habilitar acesso público

Em algumas situações, talvez você queira permitir que alguém se conecte ao seu espaço de trabalho protegido por um ponto de extremidade público, em vez de através da VNet. Depois de configurar um espaço de trabalho com um ponto de extremidade privado, você pode opcionalmente habilitar o acesso público ao espaço de trabalho. Isso não remove o ponto de extremidade privado. Todas as comunicações entre componentes por trás da VNet ainda são protegidas. Ele permite acesso público somente ao espaço de trabalho, além do acesso privado por meio da VNet.

> [!WARNING]
> Ao se conectar por meio do ponto de extremidade público, alguns recursos do estúdio falharão ao acessar seus dados. Esse problema ocorre quando os dados são armazenados em um serviço protegido por trás da VNet. Por exemplo, uma conta de armazenamento do Azure.

Para habilitar o acesso público a um espaço de trabalho habilitado para vínculo privado, use as seguintes etapas:

# <a name="python"></a>[Python](#tab/python)

Use [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) para remover um ponto de extremidade privado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão de CLI do Azure para Machine Learning](reference-azure-machine-learning-cli.md) fornece o comando [AZ ml Workspace Update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_update) . Para habilitar o acesso público ao espaço de trabalho, adicione o parâmetro `--allow-public-access true` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Atualmente, não há nenhuma maneira de habilitar essa funcionalidade usando o Portal.

---


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como proteger seu espaço de trabalho do Azure Machine Learning, consulte o artigo [visão geral de isolamento e privacidade da rede virtual](how-to-network-security-overview.md) .

* Se você planeja usar uma solução DNS personalizada em sua rede virtual, consulte [como usar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).
