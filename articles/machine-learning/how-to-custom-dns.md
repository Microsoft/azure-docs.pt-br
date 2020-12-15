---
title: Usar servidor DNS personalizado
titleSuffix: Azure Machine Learning
description: Como configurar um servidor DNS personalizado para trabalhar com um espaço de trabalho Azure Machine Learning e um ponto de extremidade privado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 07ff656c5eacbbcdc16c6c7cf098478ca6baf745
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509284"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Como usar seu workspace com um servidor DNS personalizado

Ao usar um espaço de trabalho Azure Machine Learning com um ponto de extremidade privado, há [várias maneiras de lidar com a resolução de nomes DNS](../private-link/private-endpoint-dns.md). Por padrão, o Azure manipula automaticamente a resolução de nomes para seu espaço de trabalho e o ponto de extremidade privado. Se, em vez disso, você _usar seu próprio servidor DNS personalizado_ _, deverá criar manualmente entradas DNS para o espaço de trabalho.

> [!IMPORTANT]
> Este artigo aborda apenas como localizar o FQDN (nome de domínio totalmente qualificado) e os endereços IP para essas entradas, ele não fornece informações sobre como configurar os registros DNS para esses itens. Consulte a documentação do software DNS para obter informações sobre como adicionar registros.

## <a name="prerequisites"></a>Pré-requisitos

- Uma rede virtual do Azure que usa [seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Um espaço de trabalho Azure Machine Learning com um ponto de extremidade privado. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Familiaridade com o uso do [isolamento de rede durante o treinamento & inferência](./how-to-network-security-overview.md).

- Familiaridade com a [configuração da zona DNS do ponto de extremidade privado do Azure](../private-link/private-endpoint-dns.md)

- Opcionalmente, [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Localizar os endereços IP

A lista a seguir contém os nomes de domínio totalmente qualificados (FQDN) usados pelo seu espaço de trabalho e pelo ponto de extremidade privado:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Se você criar uma instância de computação, também deverá adicionar uma entrada para `<instance-name>.<region>.instances.azureml.ms` com o IP privado do ponto de extremidade privado do espaço de trabalho.

    > [!NOTE]
    > As instâncias de computação podem ser acessadas somente de dentro da rede virtual.

Para localizar os endereços IP internos para os FQDNs na VNet, use um dos seguintes métodos:

> [!NOTE]
> Os nomes de domínio totalmente qualificados e os endereços IP serão diferentes com base na sua configuração. Por exemplo, o valor de GUID no nome de domínio será específico para seu espaço de trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Na [portal do Azure](https://portal.azure.com), selecione seu espaço de __trabalho__ do Azure Machine Learning.
1. Na seção __configurações__ , selecione __conexões de ponto de extremidade privado__.
1. Selecione o link na coluna __ponto de extremidade privado__ que é exibida.
1. Uma lista de nomes de domínio totalmente qualificados (FQDN) e endereços IP para o ponto de extremidade privado do espaço de trabalho estão na parte inferior da página.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista de FQDNs no portal":::

---

As informações retornadas de todos os métodos são as mesmas; uma lista de FQDN e endereço IP privado para os recursos.

| FQDN | Endereço IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Alguns FQDNs não são mostrados em listados pelo ponto de extremidade privado, mas são exigidos pelo espaço de trabalho. Esses FQDNs são listados na tabela a seguir e também devem ser adicionados ao seu servidor DNS:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Se você tiver uma instância de computação, use `<instance-name>.<region>.instances.azureml.ms` , em que `<instance-name>` é o nome da sua instância de computação. Use o endereço IP privado do ponto de extremidade privado do espaço de trabalho. Observe que a instância de computação pode ser acessada somente de dentro da rede virtual.
>
> Para todos esses endereços IP, use o mesmo endereço que as `*.api.azureml.ms` entradas retornadas das etapas anteriores.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar Azure Machine Learning com uma rede virtual, consulte a [visão geral da rede virtual](how-to-network-security-overview.md).
