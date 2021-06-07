---
title: 'Tutorial: Gerenciar consultas no portal do Azure'
description: Neste tutorial, você cria uma consulta do Resource Graph e compartilha a nova consulta com outras pessoas no portal do Azure.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916672"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Tutorial: Criar e compartilhar uma consulta do Azure Resource Graph no portal do Azure

O Azure Resource Graph Explorer permite salvar consultas do Resource Graph diretamente no portal do Azure. Há dois tipos de consultas: _Privada_ e _compartilhada_. Uma consulta privada é salva em suas configurações do portal do Azure. Já uma consulta compartilhada é um recurso do Azure Resource Manager que pode ser gerenciado com o RBAC (controle de acesso baseado em função) do Azure e protegido com bloqueios de recurso. Os dois tipos de consultas são criptografados em repouso.

Ao salvar consultas no portal do Azure, você economiza tempo que, de outro modo, poderia ter que gastar procurando suas consultas favoritas ou frequentemente usadas. Ao compartilhar consultas, você ajuda sua equipe a perceber as metas de consistência e eficiência por meio de repetição.

Neste tutorial, você concluirá as seguintes tarefas:

> [!div class="checklist"]
> - Criar e excluir uma consulta privada
> - Criar uma consulta compartilhada
> - Descobrir consultas compartilhadas
> - Excluir uma consulta compartilhada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-and-delete-a-private-query"></a>Criar e excluir uma consulta privada

As consultas particulares são acessíveis e visíveis somente para a conta que as cria. À medida que são salvas nas configurações do portal do Azure de uma conta, elas podem ser criadas, usadas e excluídas somente de dentro do portal do Azure. Uma consulta privada não é um recurso do Resource Manager. Para criar uma nova consulta privada, siga estas etapas:

1. No menu do portal, selecione **Todos os serviços** ou use a caixa do Azure Search na parte superior de todas as páginas. Pesquise pelo **Resource Graph Explorer** e selecione-o.

1. Na guia **Consulta 1** na página do Azure Resource Graph Explorer, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre essa consulta, veja [Amostras – contar máquinas virtuais por tipo de sistema operacional](../samples/starter.md#count-os).

1. Selecione **Salvar** ou **Salvar como**, insira **Contar VMs por sistema operacional** como o nome, deixe o tipo como **Consulta privada** e, em seguida, selecione **Salvar** na parte inferior do painel **Salvar consulta**. O título da guia muda de **Consulta 1** para **Contar VMs por SO**.

1. Saia do Azure Resource Graph Explorer no portal do Azure e, em seguida, retorne a ele. Observe que a consulta salva não é mais exibida e que a guia **Consulta 1** retornou.

1. Selecione **Abrir uma consulta**. Verifique se o tipo é **Consulta privada**. O nome salvo **Contar VMs por sistema operacional** agora aparece na lista **Nome da Consulta**. Quando você seleciona o link de título da consulta salva, ele é carregado em uma nova guia com o nome dessa consulta.

   > [!NOTE] 
   > Quando uma consulta salva estiver aberta e a guia mostrar o nome dela, selecionar o botão **Salvar** atualizará a consulta com as alterações feitas. Para criar uma nova consulta salva com base nessa consulta aberta, selecione **Salvar como** e prossiga como se você estivesse salvando uma consulta totalmente nova.

1. Para excluir a consulta salva, selecione novamente **Abrir uma consulta** e verifique se o campo **Tipo** está definido como **Consulta privada**. Na linha da consulta `Count VMs by OS` salva, selecione **Excluir** (ícone de Lixeira). Na caixa de diálogo de confirmação, selecione **Sim** para concluir a exclusão da consulta.
   Em seguida, feche o painel **Abrir uma consulta**.

## <a name="create-a-shared-query"></a>Criar uma consulta compartilhada

Ao contrário de uma consulta privada, uma consulta compartilhada é um recurso do Resource Manager. Esse fato significa que a consulta é salva em um grupo de recursos, pode ser gerenciada e controlada com RBAC do Azure e pode até mesmo ser protegida com bloqueios de recursos. Por ser um recurso, qualquer pessoa que tenha as permissões apropriadas pode vê-lo e usá-lo. Para criar uma nova consulta compartilhada, siga estas etapas:

1. No menu do portal, selecione **Todos os serviços** ou use a caixa do Azure Search na parte superior de todas as páginas para pesquisar pelo **Azure Resource Graph Explorer** e selecioná-lo.

1. Na guia **Consulta 1** na página do Azure Resource Graph Explorer, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre essa consulta, veja [Amostras – contar máquinas virtuais por tipo de sistema operacional](../samples/starter.md#count-os).

1. Selecione **Salvar** ou **Salvar como**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Salvar a nova consulta usando o botão Salvar" border="false":::

1. No painel **Salvar consulta**, defina o nome como **Contar VMs por sistema operacional**.

1. Altere o tipo para **Consulta compartilhada**, defina a descrição para **Contagem de máquinas virtuais por tipo de sistema operacional** e defina a **Assinatura** para especificar onde o recurso de consulta será criado.

1. Deixe a caixa de seleção **Publicar no grupo de recursos resource-graph-queries** marcada e a **Localização do Grupo de Recursos** definida como **(EUA) Centro-oeste dos EUA**.

1. Selecione **Salvar** na parte inferior do painel **Salvar consulta**. O título da guia muda de **Consulta 1** para **Contar VMs por SO**. Na primeira vez que o grupo de recursos **Resource-Graph-queries** é usado, o salvamento demora mais do que o esperado à medida que o grupo de recursos é criado.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Salvar a nova consulta como uma consulta compartilhada" border="false":::

   > [!NOTE] 
   > Se desejar fornecer o nome de um grupo de recursos existente no qual salvar a consulta compartilhada, você poderá desmarcar a caixa de seleção **Publicar no grupo de recursos resource-graph-queries**. Usar o grupo de recursos com o nome padrão para consultas torna as consultas compartilhadas mais fáceis de descobrir. Essa escolha também torna a finalidade desse grupo de recursos mais aparente. No entanto, você pode optar por selecionar um grupo de recursos existente por motivos de segurança, com base em permissões existentes.

1. Saia do Azure Resource Graph Explorer no portal do Azure e, em seguida, retorne a ele. Observe que a consulta salva não é mais exibida e que a guia **Consulta 1** retornou.

1. Selecione **Abrir uma consulta**. Verifique se o tipo está definido como **Consulta compartilhada** e a combinação de **Assinatura** e **Grupo de recursos** corresponde ao local em que você salvou a consulta. O item salvo **Contar VMs por sistema operacional** agora aparece na lista **Nome da Consulta**. Selecione o link de título da consulta salva para carregá-la em uma nova guia com o nome dessa consulta. Como uma consulta compartilhada, ela exibe um ícone na guia ao lado do título, o qual indica que ela é compartilhada.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Mostrar a consulta compartilhada com o ícone" border="false":::

   > [!NOTE] 
   > Quando uma consulta salva estiver aberta e a guia mostrar o nome dela, o botão **Salvar** atualizará a consulta com as alterações feitas. Para criar uma nova consulta salva, selecione **Salvar como** e prossiga como se você estivesse salvando uma consulta totalmente nova.

## <a name="discover-shared-queries"></a>Descobrir consultas compartilhadas

Já que uma consulta compartilhada é um recurso do Resource Manager, há várias maneiras de encontrar uma:

- No Resource Graph Explorer, selecione **Abrir uma consulta** e defina o tipo como **Consulta compartilhada**.
- Na página do portal de consultas do Resource Graph.
- No grupo de recursos em que a consulta compartilhada foi salva.
- Por meio de uma consulta ao Resource Graph.

### <a name="view-resource-graph-queries"></a>Exibir consultas do Resource Graph

No portal do Azure, a página Consultas do Resource Graph exibe consultas compartilhadas às quais a conta conectada tem acesso. Esta página habilita a filtragem por nome, assinatura, grupo de recursos e outras propriedades da consulta do Resource Graph. Você também pode marcar, exportar e excluir consultas do Resource Graph usando essa interface.

Selecionar uma das consultas abre a página de consulta do Resource Graph. Assim como outros recursos do Resource Manager, esta página oferece uma visão geral interativa juntamente com o log de atividades, o controle de acesso e as marcas. Você também pode aplicar um bloqueio de recurso diretamente desta página.

Acesse a página de consultas do Resource Graph no menu do portal selecionando **Todos os serviços** ou usando a caixa Azure Search na parte superior de todas as páginas. Pesquise pelo **Resource Graph Explorer** e selecione-o.

### <a name="list-resource-groups-resources"></a>Listar recursos de grupos de recursos

A consulta do Resource Graph é listada junto com outros recursos que fazem parte de um grupo de recursos.
Selecionar a consulta do Resource Graph abre a página para essa consulta. As opções de menu de atalho e reticências (disparadas clicando com o botão direito do mouse) funcionam da mesma forma que na página consulta do Resource Graph.

### <a name="query-resource-graph"></a>Consultar o Resource Graph

Você pode encontrar consultas do Resource Graph por meio de uma consulta ao Resource Graph. A consulta do Resource Graph a seguir limita os resultados pelo tipo `Microsoft.ResourceGraph/queries` e, em seguida, usa `project` para listar apenas o nome, a hora da última modificação e a consulta propriamente dita:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Executar uma consulta compartilhada

Uma consulta compartilhada do Resource Graph pode ser executada com a sintaxe `{{shared-query-uri}}` (versão prévia). Para obter mais informações, confira [Sintaxe das consultas compartilhadas](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Excluir uma consulta compartilhada

Se uma consulta compartilhada não for mais necessária, exclua-a. Ao excluir uma consulta compartilhada, você remove o recurso do Resource Manager correspondente. Todos os painéis aos quais o gráfico de resultados foi fixado agora exibem uma mensagem de erro. Quando essa mensagem de erro for exibida, use o botão **Remover do painel** para limpar o painel.

Você pode excluir uma Consulta compartilhada por meio das seguintes interfaces:
- Página de consultas do Resource Graph
- Página de consulta do Resource Graph
- A página **Abrir uma consulta** no Resource Graph Explorer
- Página de grupos de recursos

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, exclua as consultas particulares e compartilhadas que você criou se não as quiser mais.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou consultas Privadas e Compartilhadas. Para saber mais sobre a linguagem do Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](../concepts/query-language.md)