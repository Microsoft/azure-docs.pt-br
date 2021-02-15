---
title: Implantar em uma rede virtual existente
description: Descreve como habilitar usuários de seu aplicativo gerenciado para selecionar uma rede virtual existente. A rede virtual pode estar fora do aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260679"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Usar rede virtual existente com aplicativos gerenciados do Azure

Este artigo mostra como definir um aplicativo gerenciado do Azure que se integra a uma rede virtual existente na assinatura do consumidor. O aplicativo gerenciado permite que o consumidor decida se deseja criar uma nova rede virtual ou usar uma existente. A rede virtual existente pode estar fora do grupo de recursos gerenciado.

## <a name="main-template"></a>Modelo principal

Primeiro, vamos examinar o **mainTemplate.jsno** arquivo. O modelo completo para implantar uma máquina virtual e seus recursos associados é mostrado abaixo. Posteriormente, você examinará melhor as partes do modelo que estão relacionadas ao uso de uma rede virtual existente.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Observe que a rede virtual é [implantada condicionalmente](../templates/conditional-resource-deployment.md). O consumidor passa um valor de parâmetro que indica se deve criar um novo ou usar uma rede virtual existente. Se o consumidor selecionar uma nova rede virtual, o recurso será implantado. Caso contrário, o recurso será ignorado durante a implantação.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

A variável para a ID de rede virtual tem duas propriedades. Uma propriedade retorna a ID do recurso quando uma nova rede virtual é implantada. A outra propriedade retorna a ID do recurso quando uma rede virtual existente é usada. A ID de recurso para a rede virtual existente inclui o nome do grupo de recursos que contém a rede virtual.

A ID de sub-rede é construída com base no valor para a ID de rede virtual. Ele usa o valor corresponde à seleção de consumidores.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

A interface de rede é definida como a variável de ID de sub-rede.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definição da interface do usuário

Agora, vamos examinar o **createUiDefinition.jsno** arquivo. O arquivo inteiro é:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

O arquivo inclui um elemento de rede virtual.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Esse elemento permite que o consumidor selecione uma rede virtual nova ou existente.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Rede virtual nova ou existente":::

Nas saídas, você inclui um valor que indica se o consumidor selecionou uma rede virtual nova ou existente. Também há um valor de identidade gerenciada.

> [!NOTE]
> O valor de saída para a identidade gerenciada deve ser nomeado **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar o arquivo de definição de interface do usuário, consulte [CreateUiDefinition.jsem para a experiência de criação do aplicativo gerenciado do Azure](create-uidefinition-overview.md).
