---
title: Auditar e gerenciar a conformidade da política
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Policy para usar políticas internas para Azure Machine Learning para garantir que seus espaços de trabalho estejam em conformidade com seus requisitos.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 22901c4e8409fc4846c1566a57b2679f4fa92396
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444549"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditar e gerenciar Azure Machine Learning usando Azure Policy

[Azure Policy](../governance/policy/index.yml) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Com Azure Machine Learning, você pode atribuir as seguintes políticas:

* **Chave gerenciada pelo cliente** : auditar ou impor se os espaços de trabalho devem usar uma chave gerenciada pelo cliente.
* **Link privado** : auditar se os espaços de trabalho usam um ponto de extremidade privado para se comunicar com uma rede virtual.

As políticas podem ser definidas em escopos diferentes, como no nível de assinatura ou grupo de recursos. Para obter mais informações, consulte a [documentação do Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Políticas internas

Azure Machine Learning fornece um conjunto de políticas que você pode usar para cenários comuns com Azure Machine Learning. Você pode atribuir essas definições de política à sua assinatura existente ou usá-las como base para criar suas próprias definições personalizadas. Para obter uma lista completa das políticas internas para Azure Machine Learning, consulte [políticas internas para Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Para exibir as definições de política internas relacionadas a Azure Machine Learning, use as seguintes etapas:

1. Vá para __Azure Policy__ na [portal do Azure](https://portal.azure.com).
1. Selecione __definições__.
1. Para __tipo__ , selecione _interno_ e para __categoria__ , selecione __Machine Learning__.

A partir daqui, você pode selecionar as definições de política para exibi-las. Ao exibir uma definição, você pode usar o link __atribuir__ para atribuir a política a um escopo específico e configurar os parâmetros para a política. Para obter mais informações, consulte [atribuir uma política-portal](../governance/policy/assign-policy-portal.md).

Você também pode atribuir políticas usando [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [CLI do Azure](../governance/policy/assign-policy-azurecli.md)e [modelos](../governance/policy/assign-policy-template.md).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Criptografia de espaços de trabalho com chave gerenciada pelo cliente

Controla se os espaços de trabalho devem ser criptografados com uma chave gerenciada pelo cliente ou usando uma chave gerenciada pela Microsoft para criptografar métricas e metadados. Para obter mais informações sobre como usar a chave gerenciada pelo cliente, consulte a seção [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) do artigo criptografia de dados.

Para configurar essa política, defina o parâmetro de efeito como __auditoria__ ou __negar__. Se estiver definido como __auditoria__ , você poderá criar espaços de trabalho sem uma chave gerenciada pelo cliente e um evento de aviso será criado no log de atividades.

Se a política estiver definida como __negar__ , você não poderá criar um espaço de trabalho a menos que ele especifique uma chave gerenciada pelo cliente. A tentativa de criar um espaço de trabalho sem uma chave gerenciada pelo cliente resulta em um erro semelhante a `Resource 'clustername' was disallowed by policy` e cria um erro no log de atividades. O identificador de política também é retornado como parte desse erro.

## <a name="workspaces-should-use-private-link"></a>Os espaços de trabalho devem usar o link privado

Controla se os espaços de trabalho devem usar o link privado do Azure para se comunicar com a rede virtual do Azure. Para obter mais informações sobre como usar o link privado, consulte [Configurar link privado para um espaço de trabalho](how-to-configure-private-link.md).

Para configurar essa política, defina o parâmetro de efeito como __auditoria__. Se você criar um espaço de trabalho sem usar o link privado, um evento de aviso será criado no log de atividades.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure Policy](../governance/policy/overview.md)
* [Políticas internas para Azure Machine Learning](policy-reference.md)
* [Trabalhando com políticas de segurança com a central de segurança do Azure](../security-center/tutorial-security-policy.md)