---
title: Criar um grupo de gerenciamento com o portal
description: Neste guia de início rápido, você usará o portal do Azure para criar um grupo de gerenciamento para organizar seus recursos em uma hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 1b0f7b0d98b2cde1343325d5e4a2979e3e663b68
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535912"
---
# <a name="quickstart-create-a-management-group"></a>Início Rápido: Crie um grupo de gerenciamento

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

### <a name="create-in-portal"></a>Criar no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Gerenciamento + governança**.

1. Selecione **Grupos de Gerenciamento**.

1. Escolha **+ Adicionar grupo de gerenciamento**.

   :::image type="content" source="./media/main.png" alt-text="Captura de tela da página Grupos de gerenciamento mostrando assinaturas e grupos de gerenciamento filho.":::

1. Mantenha a opção **Criar** selecionada e preencha o campo da ID do grupo de gerenciamento.

   - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, pois é usado em todo o sistema do Azure para identificar esse grupo. O [grupo de gerenciamento raiz](./overview.md#root-management-group-for-each-directory) é criado automaticamente com uma ID que é a ID do Azure Active Directory. Para todos os outros grupos de gerenciamento, atribua uma ID exclusiva.
   - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Captura de tela das opções &quot;Adicionar grupo de gerenciamento&quot; para criar um grupo de gerenciamento.":::

1. Clique em **Salvar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de gerenciamento criado, siga estas etapas:

1. Selecione **Todos os serviços** > **Gerenciamento + governança**.

1. Selecione **Grupos de Gerenciamento**.

1. Encontre o grupo de gerenciamento criado acima, selecione-o e escolha **Detalhes** ao lado do nome.
   Depois, selecione **Excluir** e confirme o prompt.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)
