---
title: Importar e executar notebooks de um repositório do GitHub para o Azure Cosmos DB
description: Saiba como se conectar ao GitHub e importar os notebooks de um repositório do GitHub para sua conta do Azure Cosmos. Após a importação, você pode executar, editar e salvar as alterações novamente no GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690644"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importar notebooks de um repositório do GitHub para o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Após [habilitar o suporte ao notebook](enable-notebooks.md) para suas contas do Azure Cosmos, você pode criar novos notebooks, carregar novos notebooks do computador local ou importar os notebooks existentes de suas contas do GitHub. Este artigo mostra como conectar seu espaço de trabalho de notebooks ao GitHub e importar os notebooks de um repositório do GitHub para sua conta do Azure Cosmos. Após a importação, você pode executá-las, fazer alterações e salvar as alterações de volta no GitHub.

## <a name="get-notebooks-from-github"></a>Obter notebooks do GitHub

Você pode se conectar a seus próprios repositórios do GitHub ou a outros repositórios do GitHub públicos para ler, criar e compartilhar notebooks no Azure Cosmos DB. Use as etapas a seguir para conectar-se a uma conta do GitHub:

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até sua conta do Azure Cosmos.

1. Abra a guia **Data Explorer**. Esta guia mostrará todos os seus bancos de dados, contêineres e notebooks existentes.

1. Selecione o item de menu **Conectar ao GitHub**.

1. Uma guia é aberta, onde você pode optar por se conectar aos **Repositórios públicos** apenas ou aos **Repositórios públicos e privados**.  Depois de escolher as opções necessárias, selecione **Autorizar acesso**. A autorização é necessária para que o Azure Cosmos DB acesse os repositórios na sua conta do GitHub.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autorizar o acesso do Azure Cosmos DB aos seus repositórios do GitHub":::

1. Você será redirecionado para a página da Web “github.com”, onde poderá confirmar a autorização. Selecione o botão **Autorizar AzureCosmosDBNotebooks** e insira a senha da sua conta do GitHub no prompt.

1. Após a autorização ser concedida, você voltará à sua conta do Azure Cosmos. Em seguida, você verá todos os repositórios públicos/privados da sua conta do GitHub. É possível selecionar um repositório da lista disponível ou adicionar um repositório diretamente usando sua URL.

1. Após selecionar o repositório necessário, a entrada do repositório é movida da seção **Repositórios não fixados**  para a seção **Repositórios fixos**. Se necessário, você também pode escolher um branch específico desse repositório para importar os notebooks.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Escolha um repositório e um branch":::

1. Selecione **OK** para concluir a operação de importação. Todos os notebooks disponíveis no branch selecionado do seu repositório são importados para sua conta do Azure Cosmos.

Após a integração com uma conta do GitHub, só você poderá ver a lista de repositórios e notebooks na sua conta do Azure Cosmos. Essa instrução é verdadeira mesmo que vários usuários façam logon na conta de Azure Cosmos DB e adicionem suas próprias contas. Em outras palavras, vários usuários podem usar a mesma conta do Azure Cosmos para conectar o espaço de trabalho do notebook ao GitHub. No entanto, os usuários veem apenas a lista de repositórios e notebooks que eles importaram. Os notebooks importados por outros não são visíveis para você.

Para desconectar sua conta do GitHub do espaço de trabalho do notebook, abra a guia **Data Explorer**, selecione `…` ao lado de **Repositórios do GitHub** e selecione **Desconectar do GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Edite um notebook e enviar por push alterações para o GitHub

Você pode editar um notebook existente ou adicionar um novo notebook ao repositório e salvar as alterações de volta no GitHub.

Após editar um notebook existente, selecione **Salvar**. Uma caixa de diálogo é aberta onde você pode inserir a mensagem de confirmação para as alterações feitas. Selecione **Confirmar** e o notebook no GitHub é atualizado. Você pode validar as atualizações entrando na sua conta do GitHub e verificando o histórico de confirmação.

No fluxo do GitHub regular, após confirmar as alterações, você normalmente enviará as alterações por push para um repositório remoto. No entanto, nesse caso, a opção Confirmar tem a finalidade de “preparar, confirmar e enviar por push” suas atualizações para o GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Editar notebooks e confirmar alterações no GitHub":::

Depois de editar o bloco de anotações, você poderá [publicá-lo na Galeria de blocos de anotações](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os benefícios dos [Notebooks do Jupyter do Azure Cosmos DB.](cosmosdb-jupyter-notebooks.md)
* [Explorar galeria de exemplos de notebook](https://cosmos.azure.com/gallery.html)
* [Publicar blocos de anotações na Galeria de notebooks Azure Cosmos DB](publish-notebook-gallery.md)
* [Usar recursos e comandos de notebook Python](use-python-notebook-features-and-commands.md)
* [Usar recursos e comandos de notebook C#](use-csharp-notebook-features-and-commands.md)
