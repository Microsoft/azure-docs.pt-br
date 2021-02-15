---
title: Examinar solicitações de pull em ambientes de pré-produção nos Aplicativos Web Estáticos do Azure
description: Saiba como usar ambientes de pré-produção para examinar as alterações de solicitações de pull nos Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: f8f2e352ae458e3e2825c9701437ea652ba07375
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825664"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Examinar solicitações de pull em ambientes de pré-produção na Versão Prévia dos Aplicativos Web Estáticos do Azure

Este artigo demonstra como usar ambientes de pré-produção para examinar alterações nos aplicativos implantados com os [Aplicativos Web Estáticos do Azure](overview.md).

Um ambiente de pré-produção (preparo) é uma versão de preparo totalmente funcional do seu aplicativo que inclui alterações não disponíveis na produção.

Os Aplicativos Web Estáticos do Azure geram um fluxo de trabalho de Ações do GitHub no repositório. Quando uma solicitação de pull é criada em uma ramificação que o fluxo de trabalho observa, o ambiente de pré-produção é criado. O ambiente de pré-produção prepara o aplicativo, permitindo que você execute revisões antes de enviar por push para produção.

Vários ambientes de pré-produção podem coexistir ao mesmo tempo ao usar Aplicativos Web Estáticos do Azure. Cada vez que você cria uma solicitação de pull em relação à ramificação observada, uma versão preparada com suas alterações é implantada em um ambiente de pré-produção distinto.

Há muitos benefícios em usar ambientes de pré-produção. Por exemplo, você pode:

- Examine as alterações visuais entre as fases de produção e preparo. Por exemplo, exibindo atualizações para conteúdo e layout.
- Demonstre as alterações para a sua equipe.
- Compare versões diferentes do seu aplicativo.
- Valide as alterações usando testes de aceitação.
- Execute verificações de integridade antes de implantar na produção.

> [!NOTE]
> Durante a versão prévia, só é permitido [no máximo um ambiente de preparação](quotas.md) por vez.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório GitHub existente configurado com Aplicativos Web Estáticos do Azure. Consulte [Como criar seu primeiro aplicativo estático](getting-started.md) se você não tiver um.

## <a name="make-a-change"></a>Fazer uma alteração

Comece fazendo uma alteração em seu repositório. Você pode fazer isso diretamente no GitHub, conforme mostrado nas etapas a seguir.

1. Navegue até o repositório do seu projeto no GitHub e, em seguida, clique no botão **Branch** para criar uma nova ramificação.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::]

    Digite um nome de Branch e clique em **criar Branch**.

1. Vá até a pasta do _aplicativo_ e altere algum conteúdo do texto. Por exemplo, você pode alterar um título ou parágrafo. Assim que você tiver encontrado o arquivo que deseja editar, clique em **Editar** para fazer a alteração.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

1. Depois de fazer as alterações, clique em **Confirmar alterações** para confirmar suas alterações no branch.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

## <a name="create-a-pull-request"></a>Crie uma solicitação de pull

Em seguida, crie uma solicitação de pull com base nessa alteração.

1. Abra a gruia **Solicitação de pull** do seu projeto no GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

1. Clique no botão **Comparação e solicitação de pull** do seu Branch.

1. Opcionalmente, você pode preencher alguns detalhes sobre suas alterações e clicar em **Criar solicitação de pull**.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

Você pode atribuir revisores e adicionar comentários para discutir suas alterações, se necessário.

> [!NOTE]
> Você pode fazer várias alterações enviando por push novas confirmações para o seu branch. A solicitação de pull é atualizada automaticamente para refletir todas as alterações.

## <a name="review-changes"></a>Analisar revisão

Depois que a solicitação de pull for criada, o fluxo de trabalho de implantação do [GitHub Actions](https://github.com/features/actions) será executado e implantará suas alterações em um ambiente de pré-produção.

Depois que o fluxo de trabalho tiver concluído a criação e implantação do seu aplicativo, o bot do GitHub adicionará um comentário à sua solicitação de pull que contém a URL do ambiente de pré-produção. Você pode clicar neste link para ver as alterações preparadas.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

Clique na URL gerada para ver as alterações.

Se você examinar mais de perto a URL, poderá ver que ela é composta desta forma: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`.

Para uma determinada solicitação de pull, a URL permanece a mesma mesmo se você enviar novas atualizações por push. Além da URL permanecer constante, o mesmo ambiente de pré-produção é reutilizado durante a vida útil da solicitação de pull.

## <a name="publish-changes"></a>Publicar alterações

Depois que as alterações forem aprovadas, você poderá publicar suas alterações na produção mesclando a solicitação de pull.

Clique em **mesclar solicitação de pull**:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Como criar um novo Branch usando a interface do GitHub":::

Mesclar as cópias das suas alterações na ramificação rastreada (o branch de "produção"). Em seguida, o fluxo de trabalho de implantação é iniciado na ramificação rastreada e as alterações ficam ativas depois que o aplicativo é recriado.

Para verificar as alterações na produção, abra a URL de produção para carregar a versão ativa do site.

## <a name="limitations"></a>Limitações

As versões em etapas do seu aplicativo estão atualmente acessíveis publicamente por sua URL, mesmo que o repositório GitHub seja privado.

> [!WARNING]
> Tenha cuidado ao publicar conteúdo confidencial em versões de preparação, pois o acesso a ambientes de pré-produção não é restrito.

O número de ambientes de pré-produção disponíveis para cada aplicativo implantado com Aplicativos Web Estáticos depende da camada de SKU que você está usando. Por exemplo, com a Camada gratuita, você pode ter 1 ambiente de pré-produção além do ambiente de produção.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
