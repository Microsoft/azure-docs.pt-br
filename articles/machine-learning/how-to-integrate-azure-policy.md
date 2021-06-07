---
title: Auditar e gerenciar a conformidade da política
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Policy para usar políticas internas para Azure Machine Learning para garantir que seus espaços de trabalho estejam em conformidade com seus requisitos.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544359"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditar e gerenciar Azure Machine Learning usando Azure Policy

[Azure Policy](../governance/policy/index.yml) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Com Azure Machine Learning, você pode atribuir as seguintes políticas:

| Política | Descrição |
| ----- | ----- |
| **Chave gerenciada pelo cliente** | Auditar ou impor se os espaços de trabalho devem usar uma chave gerenciada pelo cliente. |
| **Link privado** | Auditar ou impor se os espaços de trabalho usam um ponto de extremidade privado para se comunicar com uma rede virtual. |
| **Ponto de extremidade privado** | Configure a sub-rede da rede virtual do Azure em que o ponto de extremidade privado deve ser criado. |
| **Zona de DNS privado** | Configure a zona DNS privada a ser usada para o link privado. |
| **Identidade gerenciada atribuída pelo usuário** | Auditar ou impor se os espaços de trabalho usam uma identidade gerenciada atribuída pelo usuário. |

As políticas podem ser definidas em escopos diferentes, como no nível de assinatura ou grupo de recursos. Para obter mais informações, consulte a [documentação do Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Políticas internas

Azure Machine Learning fornece um conjunto de políticas que você pode usar para cenários comuns com Azure Machine Learning. Você pode atribuir essas definições de política à sua assinatura existente ou usá-las como base para criar suas próprias definições personalizadas. Para obter uma lista completa das políticas internas para Azure Machine Learning, consulte [políticas internas para Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Para exibir as definições de política internas relacionadas a Azure Machine Learning, use as seguintes etapas:

1. Vá para __Azure Policy__ na [portal do Azure](https://portal.azure.com).
1. Selecione __Definições__.
1. Para __tipo__, selecione _interno_ e para __categoria__, selecione __Machine Learning__.

A partir daqui, você pode selecionar as definições de política para exibi-las. Ao exibir uma definição, você pode usar o link __atribuir__ para atribuir a política a um escopo específico e configurar os parâmetros para a política. Para obter mais informações, consulte [atribuir uma política-portal](../governance/policy/assign-policy-portal.md).

Você também pode atribuir políticas usando [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md)e [modelos](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Criptografia de espaço de trabalho com chave gerenciada pelo cliente

Controla se um espaço de trabalho deve ser criptografado com uma chave gerenciada pelo cliente ou usando uma chave gerenciada pela Microsoft para criptografar métricas e metadados. Para obter mais informações sobre como usar a chave gerenciada pelo cliente, consulte a seção [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) do artigo criptografia de dados.

Para configurar essa política, defina o parâmetro de efeito como __auditoria__ ou __negar__. Se definido como __auditoria__, você poderá criar um espaço de trabalho sem uma chave gerenciada pelo cliente e um evento de aviso será criado no log de atividades.

Se a política estiver definida como __negar__, você não poderá criar um espaço de trabalho a menos que ele especifique uma chave gerenciada pelo cliente. A tentativa de criar um espaço de trabalho sem uma chave gerenciada pelo cliente resulta em um erro semelhante a `Resource 'clustername' was disallowed by policy` e cria um erro no log de atividades. O identificador de política também é retornado como parte desse erro.

## <a name="workspace-should-use-private-link"></a>O espaço de trabalho deve usar o link privado

Controla se um espaço de trabalho deve usar o link privado do Azure para se comunicar com a rede virtual do Azure. Para obter mais informações sobre como usar o link privado, consulte [Configurar link privado para um espaço de trabalho](how-to-configure-private-link.md).

Para configurar essa política, defina o parâmetro de efeito como __auditoria__ ou __negar__. Se definido como __auditoria__, você poderá criar um espaço de trabalho sem usar o link privado e um evento de aviso será criado no log de atividades.

Se a política estiver definida como __negar__, você não poderá criar um espaço de trabalho a menos que ele use um link privado. A tentativa de criar um espaço de trabalho sem um link privado resulta em um erro. O erro também é registrado no log de atividades. O identificador de política é retornado como parte desse erro.

## <a name="workspace-should-use-private-endpoint"></a>O espaço de trabalho deve usar o ponto de extremidade privado

Configura um espaço de trabalho para criar um ponto de extremidade privado dentro da sub-rede especificada de uma rede virtual do Azure.

Para configurar essa política, defina o parâmetro Effect como __DeployIfNotExists__. Defina o __privateEndpointSubnetID__ para a ID de Azure Resource Manager da sub-rede.
## <a name="workspace-should-use-private-dns-zones"></a>O espaço de trabalho deve usar zonas DNS privadas

Configura um espaço de trabalho para usar uma zona DNS privada, substituindo a resolução DNS padrão por um ponto de extremidade privado.

Para configurar essa política, defina o parâmetro Effect como __DeployIfNotExists__. Defina __privateDnsZoneId__ para a ID de Azure Resource Manager da zona DNS privada a ser usada. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>O espaço de trabalho deve usar identidade gerenciada atribuída pelo usuário

Controla se um espaço de trabalho é criado usando uma identidade gerenciada atribuída pelo sistema (padrão) ou uma identidade gerenciada atribuída pelo usuário. A identidade gerenciada para o espaço de trabalho é usada para acessar recursos associados, como o armazenamento do Azure, o registro de contêiner do Azure, Azure Key Vault e informações de Aplicativo Azure. Para obter mais informações, consulte [usar identidades gerenciadas com Azure Machine Learning](how-to-use-managed-identities.md).

Para configurar essa política, defina o parâmetro de efeito como __auditoria__, __negação__ ou __desabilitado__. Se definido como __auditoria__, você poderá criar um espaço de trabalho sem especificar uma identidade gerenciada atribuída pelo usuário. Uma identidade atribuída pelo sistema é usada e um evento de aviso é criado no log de atividades.

Se a política estiver definida como __negar__, você não poderá criar um espaço de trabalho a menos que forneça uma identidade atribuída pelo usuário durante o processo de criação. A tentativa de criar um espaço de trabalho sem fornecer uma identidade atribuída pelo usuário resulta em um erro. O erro também é registrado no log de atividades. O identificador de política é retornado como parte desse erro.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure Policy](../governance/policy/overview.md)
* [Políticas internas para Azure Machine Learning](policy-reference.md)
* [Trabalhando com políticas de segurança com a central de segurança do Azure](../security-center/tutorial-security-policy.md)