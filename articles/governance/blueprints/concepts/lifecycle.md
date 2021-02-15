---
title: Entender ciclo de vida de um blueprint
description: Saiba mais sobre o ciclo de vida que uma definição do plano gráfico passa e detalhes sobre cada estágio, incluindo a atualização e a remoção de atribuições de plano gráfico.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918510"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Entender o ciclo de vida de um Blueprint do Azure

Como muitos recursos no Azure, um blueprint no Azure Blueprints tem um ciclo de vida típico e natural. Eles são criados, implantados e finalmente excluídos quando não forem mais necessários ou relevantes. Os planos gráficos do Azure dão suporte a operações de ciclo de vida padrão. Ele cria, em seguida, após a eles para fornecer níveis de status adicionais que dão suporte a pipelines de implantação contínua e integração contínua comuns para as organizações que gerenciam sua infraestrutura como código – um elemento fundamental em DevOps.

Para entender completamente um blueprint e os estágios, vamos abordar um ciclo de vida padrão:

> [!div class="checklist"]
> - Criação e edição de um blueprint
> - Publicar o blueprint
> - Criação e edição de uma nova versão do blueprint
> - Publicar uma nova versão do blueprint
> - Exclusão de uma versão específica do blueprint
> - Exclusão do blueprint

## <a name="creating-and-editing-a-blueprint"></a>Criação e edição de um blueprint

Ao criar um blueprint, adicione artefatos, salve em um grupo de gerenciamento ou assinatura e forneça um nome e uma versão exclusivos. O blueprint agora está no modo **Rascunho** e ainda não pode ser atribuído. Enquanto no modo **Rascunho**, ele pode continuar a ser atualizado e alterado.

Um blueprint nunca publicado no **modo Rascunho** exibe um ícone diferente na página **Blueprint Definitions** do que aqueles que foram **Publicados**. A **versão mais recente** é exibida como **rascunho** para essas nunca publicadas.

Crie e edite um blueprint com o [portal do Azure](../create-blueprint-portal.md#create-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicar um blueprint

Depois que todas as alterações planejadas tiverem sido feitas em um blueprint no modo **Draft**, ele poderá ser **Publicado** e disponibilizado para atribuição. A versão **Publicada** do blueprint não pode ser alterada. Uma vez **Publicado**, o blueprint é exibido com um ícone diferente dos blueprints em **Rascunho** e exibirá o número de versão fornecido na coluna **Última Versão**.

Publique um blueprint com o [portal do Azure](../create-blueprint-portal.md#publish-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criação e edição de uma nova versão do blueprint

Uma versão **publicada** de um blueprint não pode ser alterada. No entanto, uma nova versão do blueprint pode ser adicionada ao blueprint existente e modificada conforme necessário. Faça alterações em um blueprint existente editando-o. Quando as novas alterações são salvas, o blueprint agora possui **Alterações não publicadas**. Essas alterações são uma nova versão **Draft** do blueprint.

Edite um blueprint com o [portal do Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicar uma nova versão do blueprint

Cada versão editada de um blueprint deve ser **Publicado** antes de poder ser atribuído. Quando **Mudanças não publicadas** foram feitas para um blueprint, mas não **Publicado**, o botão **Publicar Blueprint** está disponível na página de blueprint de edição. Se o botão não estiver visível, o blueprint já foi **Publicado** e não tem **Alterações Não Publicadas**.

> [!NOTE]
> Um único blueprint pode ter várias versões **Publicadas** que podem ser atribuídas a assinaturas.

Para publicar um blueprint com **Alterações não publicadas**, use as mesmas etapas para publicar um novo blueprint.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Exclusão de uma versão específica do blueprint

Cada versão de um blueprint é um objeto exclusivo que pode ser individualmente **Publicado**. Assim, cada versão de um blueprint também pode ser excluída. Excluir uma versão de um blueprint não tem nenhum impacto nas outras versões desse blueprint.

> [!NOTE]
> Não é possível excluir um blueprint com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **definições de plano gráfico** na página à esquerda e use as opções de filtro para localizar o plano gráfico para o qual você deseja excluir uma versão. Selecione-o para abrir a página Editar.

1. Selecione a guia **versões publicadas** e localize a versão que você deseja excluir.

1. Clique com o botão direito do mouse na versão a ser excluída e selecione **excluir esta versão**.

## <a name="deleting-the-blueprint"></a>Exclusão do blueprint

O blueprint principal também pode ser excluído. Excluir o plano gráfico de núcleo também exclui todas as versões de especificações técnicas de especificações técnicas, incluindo ambas **rascunho** e **publicado** planos gráficos. Assim como na exclusão de uma versão de um blueprint, a exclusão do blueprint principal não remove as atribuições existentes das versões dos blueprints.

> [!NOTE]
> Não é possível excluir um blueprint com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

Exclua um blueprint com o [portal do Azure](../create-blueprint-portal.md#delete-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários pontos durante o ciclo de vida em que um blueprint pode ser atribuído a uma assinatura. Quando o modo de uma versão do blueprint é **Publicado**, essa versão pode ser atribuída a uma assinatura. Esse ciclo de vida permite que versões de um blueprint sejam usadas e atribuídas ativamente enquanto uma versão mais nova está sendo desenvolvida.

Como as versões de blueprints são atribuídas, é importante entender o local em que elas estão atribuídas e com quais parâmetros elas foram atribuídas. Os parâmetros podem ser estáticos ou dinâmicos. Para obter mais informações, confira [parâmetros estáticos e dinâmicos](./parameters.md).

### <a name="updating-assignments"></a>Atualizando atribuições

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](./resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](./parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

Para saber como, confira [atualizar atribuições existentes](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Cancelando atribuição de atribuições

Se o plano gráfico não for mais necessário, ele poderá não ser atribuído do grupo de gerenciamento ou da assinatura. Durante a desatribuição do plano gráfico, ocorre o seguinte:

- Remoção do [bloqueio de recursos do Blueprint](./resource-locking.md)
- Exclusão do objeto de atribuição Blueprint
- Condiciona Se uma **identidade gerenciada atribuída pelo sistema** foi usada, ela também será excluída

> [!NOTE]
> Todos os recursos implantados pela atribuição Blueprint permanecem em vigor, mas não são mais protegidos pelas plantas do Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [parâmetros estáticos e dinâmicos](./parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](./sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](./resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).