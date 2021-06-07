---
title: Configurar o suporte de identidade gerenciada em um cluster existente do Service Fabric
description: Veja como habilitar o suporte a identidades gerenciadas em um cluster existente do Azure Service Fabric
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790032"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Configurar o suporte de identidade gerenciada em um cluster existente do Service Fabric

Para usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em seus aplicativos Service Fabric, primeiro habilite o *serviço de token de identidade gerenciado* no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  
>
> Você pode encontrar a versão Service Fabric de um cluster do portal do Azure abrindo o recurso de cluster e verificando a propriedade **versão do Service Fabric** na seção **Essentials** .
>
> Se o cluster estiver no modo de atualização **manual** , será necessário primeiro atualizá-lo para 6.5.658.9590 ou posterior.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Habilitar o *serviço de token de identidade gerenciado* em um cluster existente

Para habilitar o serviço de token de identidade gerenciado em um cluster existente, você precisará iniciar uma atualização de cluster especificando duas alterações: (1) habilitando o serviço de token de identidade gerenciada e (2) solicitando uma reinicialização de cada nó. Primeiro, adicione o seguinte trecho ao seu modelo de Azure Resource Manager de cluster:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Para que as alterações entrem em vigor, você também precisará alterar a política de atualização para especificar uma reinicialização forçada do tempo de execução de Service Fabric em cada nó à medida que a atualização progride por meio do cluster. Essa reinicialização garante que o serviço de sistema habilitado recentemente seja iniciado e em execução em cada nó. No trecho de código abaixo, `forceRestart` é a configuração essencial para habilitar a reinicialização. Para os parâmetros restantes, use os valores descritos abaixo ou use os valores personalizados existentes já especificados para o recurso de cluster. As configurações personalizadas para a política de atualização do fabric (' upgradeDescription ') podem ser exibidas no portal do Azure selecionando a opção ' malha atualizações ' no recurso de Service Fabric ou resources.azure.com. As opções padrão para a política de atualização (' upgradeDescription ') não são visíveis do PowerShell ou do resources.azure.com. Consulte [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) para obter informações adicionais.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Após a conclusão bem-sucedida da atualização, não se esqueça de reverter a `forceRestart` configuração para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e solução de problemas

Se a implantação falhar com a mensagem a seguir, isso significa que o cluster não está sendo executado em uma versão de Service Fabric alta o suficiente:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Próximas etapas
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
