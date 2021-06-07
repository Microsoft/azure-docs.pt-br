---
title: 'Início Rápido: Criar um grupo de gerenciamento com a CLI do Azure'
description: Neste guia de início rápido, você usará a CLI do Azure para criar um grupo de gerenciamento para organizar seus recursos em uma hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592442"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Início Rápido: Criar um grupo de gerenciamento com a CLI do Azure

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este início rápido exige a execução da CLI do Azure versão 2.0.76 ou posterior para instalar e usar a CLI localmente. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Criação na CLI do Azure

Na CLI do Azure, use o comando [az account management-group create](/cli/azure/account/management-group#az_account_management_group_create) para criar um grupo de gerenciamento. Neste exemplo, o **name** do grupo de gerenciamento é _Contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

O **name** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para referenciar esse grupo e não poderá ser alterada posteriormente.

Caso deseje que o grupo de gerenciamento mostre outro nome no portal do Azure, adicione o parâmetro **display-name**. Por exemplo, para criar um grupo de gerenciamento com o GroupName Contoso e o nome de exibição "Contoso Group", use o seguinte comando:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

Nos exemplos anteriores, o grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar outro grupo de gerenciamento como o pai, use o parâmetro **parent** e forneça o nome do grupo pai.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gerenciamento criado acima, use o comando [az account management-group delete](/cli/azure/account/management-group#az_account_management_group_delete):

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)