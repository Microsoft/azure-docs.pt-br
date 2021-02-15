---
title: Configurar identidades gerenciadas em pools do lote
description: Saiba como habilitar identidades gerenciadas atribuídas pelo usuário em pools do lote e como usar identidades gerenciadas dentro dos nós.
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416944"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Configurar identidades gerenciadas em pools do lote

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminam a necessidade de os desenvolvedores precisarem gerenciar credenciais fornecendo uma identidade para o recurso do azure no Azure Active Directory (Azure AD) e usando-o para obter tokens do Azure Active Directory (AD do Azure).

Este tópico explica como habilitar identidades gerenciadas atribuídas pelo usuário em pools do lote e como usar identidades gerenciadas dentro dos nós.

> [!IMPORTANT]
> O suporte para pools do lote do Azure com identidades gerenciadas atribuídas pelo usuário está atualmente em visualização pública para as seguintes regiões: oeste dos EUA 2, Sul EUA Central, leste dos EUA, US Gov Arizona e US Gov-Virgínia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Primeiro, [Crie sua identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) no mesmo locatário como sua conta do lote. Essa identidade gerenciada não precisa estar no mesmo grupo de recursos ou mesmo na mesma assinatura.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Criar um pool do lote com identidades gerenciadas atribuídas pelo usuário

Depois de criar uma ou mais identidades gerenciadas atribuídas pelo usuário, você pode criar um pool do lote com essa identidade gerenciada usando a [biblioteca de gerenciamento .net do lote](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Os pools devem ser configurados usando a [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) para usar identidades gerenciadas.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Atualmente, não há suporte para a criação de pools com identidades gerenciadas com a [biblioteca de cliente .net do lote](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Usar identidades gerenciadas atribuídas pelo usuário em nós do lote

Depois de criar seus pools, suas identidades gerenciadas atribuídas pelo usuário podem acessar os nós de pool via Secure Shell (SSH) ou Área de Trabalho Remota (RDP). Você também pode configurar suas tarefas para que as identidades gerenciadas possam acessar diretamente os [recursos do Azure que dão suporte a identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

Nos nós do lote, você pode obter tokens de identidade gerenciados e usá-los para autenticar por meio da autenticação do Azure AD por meio do [serviço de metadados de instância do Azure](../virtual-machines/windows/instance-metadata-service.md).

Para o Windows, o script do PowerShell para obter um token de acesso para autenticar é:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Para o Linux, o script bash é:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Para obter mais informações, consulte [como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Saiba como usar [chaves gerenciadas pelo cliente com identidades gerenciadas pelo usuário](batch-customer-managed-key.md).
