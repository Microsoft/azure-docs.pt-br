---
title: Atualizar a configuração de um cluster de Service Fabric do Azure
description: Saiba como atualizar a configuração que executa um cluster do Service Fabric no Azure usando um modelo do Resource Manager.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842709"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Atualizar a configuração de um cluster no Azure 

Este artigo descreve como personalizar as várias configurações de malha e a política de atualização para o cluster do Service Fabric. Para clusters hospedados no Azure, você pode personalizá-los através do [portal do Azure](https://portal.azure.com) ou utilizando um modelo do Azure Resource Manager.

> [!NOTE]
> Nem todas as configurações estão disponíveis no portal e a [melhor prática é personalizá-lo usando um modelo do Azure Resource Manager](./service-fabric-best-practices-infrastructure-as-code.md); o Portal é para o cenário de Desenvolvimento\Teste do Service Fabric apenas.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as configurações do cluster, utilizando os modelos do Resource Manager
Clusters do Azure podem ser configurados por meio do modelo do Resource Manager do JSON. Para saber mais sobre as diferentes configurações, consulte [Definições de configuração para clusters](service-fabric-cluster-fabric-settings.md). Por exemplo, as etapas a seguir mostram como adicionar uma nova configuração *MaxDiskQuotaInMB* à seção *diagnóstico* usando o Gerenciador de recursos do Azure.

1. Acesse https://resources.azure.com
2. Navegue até sua assinatura expandindo **assinaturas**  ->  **\<Your Subscription>**  ->  **resourceGroups**  ->  **\<Your Resource Group>**  ->  **provedores**  ->  **Microsoft. perfabric**  ->  **clusters** -> **\<Your Cluster Name>**
3. No canto superior direito, selecione **leitura/gravação.**
4. Selecione **Editar** e atualize o `fabricSettings` elemento JSON e adicione um novo elemento:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Você também pode personalizar as configurações de cluster em uma das maneiras a seguir com o Azure Resource Manager:

- Use o [portal do Azure](../azure-resource-manager/templates/export-template-portal.md) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use o [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use a [CLI do Azure](../azure-resource-manager/management/manage-resources-cli.md) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use os comandos Azure PowerShell [set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) e [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) para modificar a configuração diretamente.
- Usar os comandos de configuração do cluster[az sf da CLI do Azure](/cli/azure/sf/cluster/setting) para modificar a configuração diretamente.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [configurações de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-in-out.md).
