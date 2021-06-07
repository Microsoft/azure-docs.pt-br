---
title: Suporte ao mapa de origem para aplicativos JavaScript-Azure Monitor Application Insights
description: Saiba como carregar mapas de origem em seu próprio contêiner de blob de conta de armazenamento usando Application Insights.
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933936"
---
# <a name="source-map-support-for-javascript-applications"></a>Suporte do mapa de origem para aplicativos JavaScript

Application Insights dá suporte ao carregamento de mapas de origem para seu próprio contêiner de BLOB da conta de armazenamento.
Os mapas de origem podem ser usados para unminify pilhas de chamadas encontradas na página de detalhes de transação de ponta a ponta. Qualquer exceção enviada pelo [SDK do JavaScript][ApplicationInsights-JS] ou pelo [ SDK doNode.js][ApplicationInsights-Node.js] pode ser unminified com mapas de origem.

![Unminify uma pilha de chamadas vinculando-se a uma conta de armazenamento](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Criar uma nova conta de armazenamento e um contêiner de BLOBs

Se você já tiver uma conta de armazenamento ou um contêiner de blob existente, poderá ignorar esta etapa.

1. [Criar uma nova conta de armazenamento][create storage account]
2. [Crie um contêiner de BLOBs][create blob container] dentro de sua conta de armazenamento. Certifique-se de definir o "nível de acesso público" como `Private` , para garantir que os mapas de origem não sejam acessíveis publicamente.

> [!div class="mx-imgBorder"]
>![O nível de acesso do contêiner deve ser definido como privado](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Enviar por push seus mapas de origem para o contêiner de BLOB

Você deve integrar seu pipeline de implantação contínua à sua conta de armazenamento Configurando-a para carregar automaticamente os mapas de origem para o contêiner de blob configurado.

Os mapas de origem podem ser carregados no contêiner de armazenamento de BLOBs com a mesma estrutura de pastas em que foram compilados & implantados. Um caso de uso comum é prefixar uma pasta de implantação com sua versão, por exemplo, `1.2.3/static/js/main.js` . Quando unminifying por meio de um contêiner de blob do Azure chamado `sourcemaps` , ele tentará buscar um mapa de origem localizado em `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Carregar mapas de origem por meio de Azure Pipelines (recomendado)

Se você estiver usando Azure Pipelines para criar e implantar o aplicativo continuamente, adicione uma tarefa [cópia de arquivo do Azure][azure file copy] ao seu pipeline para carregar automaticamente os mapas de origem.

> [!div class="mx-imgBorder"]
> ![Adicionar uma tarefa de cópia de arquivo do Azure ao seu pipeline para carregar os mapas de origem para o armazenamento de BLOBs do Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Configurar o recurso de Application Insights com uma conta de armazenamento de mapa de origem

### <a name="from-the-end-to-end-transaction-details-page"></a>Na página detalhes da transação de ponta a ponta

Na guia detalhes da transação de ponta a ponta, você pode clicar em *Unminify* e ele exibirá um prompt para configurar se o recurso não estiver configurado.

1. No portal, exiba os detalhes de uma exceção que é reduzidos.
2. Clique em *Unminify*
3. Se o recurso não tiver sido configurado, uma mensagem será exibida, solicitando que você configure.

### <a name="from-the-properties-page"></a>Na página Propriedades

Se você quiser configurar ou alterar a conta de armazenamento ou o contêiner de BLOB que está vinculado ao recurso Application Insights, poderá fazer isso exibindo a guia *Propriedades* do recurso Application insights.

1. Navegue até a guia *Propriedades* do recurso de Application insights.
2. Clique em *alterar contêiner de blob do mapa de origem*.
3. Selecione um contêiner de BLOBs diferente como seu contêiner de mapas de origem.
4. Clique em `Apply`.

> [!div class="mx-imgBorder"]
> ![Reconfigure o contêiner de BLOBs do Azure selecionado navegando até a folha Propriedades](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>Configurações necessárias do Azure RBAC (controle de acesso baseado em função) no seu contêiner de BLOB

Qualquer usuário no portal que usa esse recurso deve ser pelo menos atribuído como um [leitor de dados de blob de armazenamento][storage blob data reader] para seu contêiner de BLOB. Você deve atribuir essa função a qualquer outra pessoa que usará os mapas de origem por meio desse recurso.

> [!NOTE]
> Dependendo de como o contêiner foi criado, isso pode não ter sido atribuído automaticamente a você ou à sua equipe.

### <a name="source-map-not-found"></a>Mapa de origem não encontrado

1. Verifique se o mapa de origem correspondente é carregado para o contêiner de blob correto
2. Verifique se o arquivo do mapa de origem é nomeado após o arquivo JavaScript para o qual ele é mapeado, com sufixo `.map` .
    - Por exemplo, `/static/js/main.4e2ca5fa.chunk.js` procurará o blob chamado `main.4e2ca5fa.chunk.js.map`
3. Verifique o console do navegador para ver se algum erro está sendo registrado. Inclua isso em qualquer tíquete de suporte.

## <a name="next-steps"></a>Próximas etapas

* [Tarefa de cópia de arquivo do Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
