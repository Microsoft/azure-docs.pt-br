---
title: 'Início Rápido: Criar um grupo de gerenciamento com a API REST'
description: Neste guia de início rápido, você usará a API REST para criar um grupo de gerenciamento para organizar seus recursos em uma hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 26a1c0a8025a00155bcf0498e6dfa89a8a73d983
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259125"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Início Rápido: Criar um grupo de gerenciamento com a API REST

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Caso ainda não tenha feito isso, instale o [ARMClient](https://github.com/projectkudu/ARMClient). Trata-se de uma ferramenta que envia solicitações HTTP para APIs REST baseadas no Azure Resource Manager. Em vez disso, use o recurso "Experimentar" na documentação da REST ou em ferramentas como o [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) do PowerShell ou o [Postman](https://www.postman.com).

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Criação na API REST

Para a API REST, use o ponto de extremidade [Grupos de Gerenciamento – Criar ou Atualizar](/rest/api/managementgroups/managementgroups/createorupdate) para criar um grupo de gerenciamento. Neste exemplo, a **groupId** do grupo de gerenciamento é _Contoso_.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Sem corpo da solicitação

A **groupId** é um identificador exclusivo que está sendo criado. Essa ID é usada por outros comandos para referenciar esse grupo e não poderá ser alterada posteriormente.

Caso deseje que o grupo de gerenciamento mostre outro nome no portal do Azure, adicione a propriedade **properties.displayName** ao corpo da solicitação. Por exemplo, para criar um grupo de gerenciamento com a **groupId** _Contoso_ e o nome de exibição _Contoso Group_, use o seguinte ponto de extremidade e corpo da solicitação:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

Nos exemplos anteriores, o grupo de gerenciamento é criado no grupo de gerenciamento raiz. Para especificar outro grupo de gerenciamento como o pai, use a propriedade **properties.parent.id**.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gerenciamento criado acima, use o ponto de extremidade [Grupos de Gerenciamento – Excluir](/rest/api/managementgroups/managementgroups/delete):

- URI da API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Sem corpo da solicitação

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)
