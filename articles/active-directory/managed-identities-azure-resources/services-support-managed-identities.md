---
title: Serviços do Azure compatíveis com identidades gerenciadas – Azure AD
description: Lista de serviços que dão suporte a identidades gerenciadas para recursos do Azure e autenticação do Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 058873df989c444ebe06fc20a2f8a40fd2d3c594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594539"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Serviços que dão suporte a identidades gerenciadas para recursos do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Usando uma identidade gerenciada, você pode autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, sem ter as credenciais no seu código. Estamos no processo de integração de identidades gerenciadas para recursos do Azure e autenticação do Azure AD no Azure. Verifique regularmente as atualizações.

> [!NOTE]
> Identidades gerenciadas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como MSI (Identidade de Serviço Gerenciada).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Serviços do Azure que dão suporte às identidades gerenciadas dos recursos do Azure

Os seguintes serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure:


### <a name="azure-api-management"></a>Gerenciamento de API do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Visualização | Visualização | Não disponível | Visualização |

Veja a lista a seguir para configurar a identidade gerenciada para Gerenciamento de API do Azure (em regiões em que estiver disponível):

- [Modelo do Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Configuração de Aplicativo do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check]  | Não disponível  | ![Disponível][check] |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure App configuração (em regiões onde estiver disponível):

- [CLI do Azure](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check] |

Veja a lista a seguir para configurar a identidade gerenciada para Serviço de Aplicativo do Azure (em regiões em que estiver disponível):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [CLI do Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [PowerShell do Azure](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modelo do Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes habilitado para Azure Arc

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Visualização | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

O kubernetes habilitado para Arc do Azure atualmente [dá suporte à identidade atribuída pelo sistema](../../azure-arc/kubernetes/quickstart-connect-cluster.md). O certificado de identidade de serviço gerenciado é usado por todos os agentes kubernetes habilitados para o Azure ARC para comunicação com o Azure.

### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Todos os servidores habilitados para Arc do Azure têm uma identidade atribuída pelo sistema. Não é possível desabilitar ou alterar a identidade atribuída do sistema em um servidor habilitado para Arc do Azure. Consulte os seguintes recursos para saber mais sobre como consumir identidades gerenciadas em servidores habilitados para Arc do Azure:

- [Autenticar em recursos do Azure com servidores habilitados para Arc](../../azure-arc/servers/managed-identity-authentication.md)
- [Usando uma identidade gerenciada com servidores habilitados para Arc](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Autogerenciamento do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Visualização | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte o documento a seguir para reconfigurar uma identidade gerenciada se você moveu sua assinatura para um novo locatário:
* [Reparar uma conta de autogerenciamento quebrada](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |

Veja a lista a seguir para usar uma identidade gerenciada com o [Azure Blueprints](../../governance/blueprints/overview.md):

- [Portal do Azure – atribuição de blueprint](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST – atribuição de blueprint](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |


### <a name="azure-communication-services"></a>Serviços de Comunicação do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | ![Disponível][check] | Não disponível | Não disponível | Não disponível |


### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Linux: Visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Linux: Visualização<br>Windows: Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Instâncias de Contêiner do Azure (em regiões em que estiverem disponíveis):

- [CLI do Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modelo do Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Tarefas do Registro de Contêiner do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Tarefas do Registro de Contêiner do Azure (em regiões em que estiverem disponíveis):

- [CLI do Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Data Factory V2 (em regiões em que estiver disponível):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Gêmeos Digitais do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o gêmeos digital do Azure (em regiões onde disponível):

- [Portal do Azure](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Grade de Eventos do Azure

Tipo de identidade gerenciada |Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Visualização | Visualização | Não disponível | Visualização |
| Atribuído pelo usuário | Não disponível | Não disponível  | Não disponível  | Não disponível |

### <a name="azure-firewall-policy"></a>Política de firewall do Azure

Tipo de identidade gerenciada |Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível  | Não disponível  | Não disponível |

### <a name="azure-functions"></a>Funções do Azure

Tipo de identidade gerenciada |Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check]  | ![Disponível][check]  | ![Disponível][check]  |

Veja a lista a seguir para configurar a identidade gerenciada para Azure Functions (em regiões em que estiver disponível):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [CLI do Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [PowerShell do Azure](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modelo do Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Hub IoT do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para o Hub IoT do Azure (em regiões onde disponível):

- [Portal do Azure](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Importação/Exportação do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível na região em que o serviço de exportação de importação do Azure está disponível | Versão Prévia | Disponível | Disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-kubernetes-service-aks"></a>AKS (Serviço de Kubernetes do Azure)

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |


Para obter mais informações, confira [Usar identidades gerenciadas no Serviço de Kubernetes do Azure](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Cluster de Log Analytics do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |

Para obter mais informações, consulte [como a identidade funciona no Azure monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Aplicativos Lógicos do Azure

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | Não disponível | ![Disponível][check] |


Veja a lista a seguir para configurar a identidade gerenciada para Aplicativos Lógicos do Azure (em regiões em que estiverem disponíveis):

- [Azure portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Modelo do Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Versão Prévia | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Visualização | Não disponível | Não disponível | Não disponível |

Para obter mais informações, consulte [usar identidades gerenciadas com Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte a lista a seguir para configurar a identidade gerenciada para Azure Policy (em regiões onde estiver disponível):

- [Azure portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [CLI do Azure](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Modelos do Gerenciador de Recursos do Azure](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[A identidade gerenciada para aplicativos Service Fabric](../../service-fabric/concepts-managed-identity.md) está disponível em todas as regiões.

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | não disponível |
| Atribuído pelo usuário | ![Disponível][check] | Não disponível | Não disponível |Não disponível |

Veja a lista a seguir para configurar a identidade gerenciada para aplicativos do Serviço do Azure em todas as regiões:

- [Modelo do Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | Não disponível | Não disponível | ![Disponível][check] |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |


Para obter mais informações, consulte [como habilitar a identidade gerenciada atribuída pelo sistema para o aplicativo Azure Spring Cloud](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Atribuído pelo sistema | Disponível na região onde o serviço de borda do Azure Stack está disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual do Azure

|Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |

Veja a lista a seguir para configurar a identidade gerenciada para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |
| Atribuído pelo usuário | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] | ![Disponível][check] |

Veja a lista a seguir para configurar a identidade gerenciada para Máquinas Virtuais do Azure (em regiões em que estiverem disponíveis):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI do Azure](qs-configure-cli-windows-vm.md)
- [Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [SDKs do Azure](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Construtor de imagem de VM do Azure

| Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Não disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | [Disponível em regiões com suporte](../../virtual-machines/image-builder-overview.md#regions) | Não disponível | Não disponível | Não disponível |

Para saber como configurar a identidade gerenciada para o construtor de imagem de VM do Azure (em regiões onde disponível), consulte a [visão geral do Image Builder](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Serviço Azure SignalR

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Visualização | Visualização | Não disponível | Visualização |
| Atribuído pelo usuário | Visualização | Visualização | Não disponível | Visualização |

Consulte a lista a seguir para configurar a identidade gerenciada para o serviço de Signaler do Azure (em regiões onde estiver disponível):

- [Modelo do Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Tipo de identidade gerenciada | Todos Disponíveis ao Público Geral<br>Regiões Globais do Azure | Azure Government | Azure Alemanha | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Atribuído pelo sistema | Disponível nas regiões em que o serviço do Azure Resource mover está disponível | Não disponível | Não disponível | Não disponível |
| Atribuído pelo usuário | Não disponível | Não disponível | Não disponível | Não disponível |

Consulte o documento a seguir para usar o Azure Resource mover:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os serviços a seguir dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam identidades gerenciadas para recursos do Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Veja a lista a seguir para configurar o acesso ao Azure Resource Manager:

- [Atribuir acesso por meio do portal do Azure](howto-assign-access-portal.md)
- [Atribuir acesso por meio do PowerShell](howto-assign-access-powershell.md)
- [Atribuir acesso por meio da CLI do Azure](howto-assign-access-CLI.md)
- [Atribuir acesso por meio do modelo do Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponível][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://management.microsoftazure.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-key-vault"></a>Cofre de Chave do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponível][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha |  `https://vault.microsoftazure.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Disponível][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponível][check] |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

### <a name="azure-sql"></a>SQL do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponível][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponível][check] |
| Azure Alemanha | `https://database.cloudapi.de/` | ![Disponível][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Disponível][check] |

### <a name="azure-data-explorer"></a>Azure Data Explorer

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Disponível][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://<account>.<region>.kusto.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponível][check] |
| Azure Government |  | Não disponível |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |

### <a name="azure-service-bus"></a>Barramento de Serviço do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponível][check] |
| Azure Government |  | ![Disponível][check] |
| Azure Alemanha |   | Não disponível |
| Azure China 21Vianet |  | Não disponível |









### <a name="azure-storage-blobs-and-queues"></a>Blobs e filas do Armazenamento do Azure

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponível][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponível][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nuvem | ID de Recurso | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponível][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponível][check] |
| Azure Alemanha | `https://*.asazure.cloudapi.de` | ![Disponível][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Disponível][check] |

> [!Note]
> O Microsoft Power BI também [é compatível com identidades gerenciadas](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Disponível"
