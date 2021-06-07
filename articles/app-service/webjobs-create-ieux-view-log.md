---
title: Exibir histórico de log de trabalhos Web
description: Exibir histórico de log para trabalhos com falha e com êxito.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744211"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Exibir o histórico do WebJob no portal do Azure

Exibir histórico de log para trabalhos com falha e com êxito.

1. Selecione o WebJob do qual você deseja ver o histórico e, em seguida, selecione o botão **Logs**.

    ![Botão Logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. Na página **Detalhes do WebJob**, selecione uma hora para ver os detalhes de uma execução.

    ![Detalhes do WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

1. Na página **Detalhes da Execução do WebJob**, selecione **Alternar Saída** para ver o texto do conteúdo do log.

    ![Detalhes da execução do trabalho Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

    Para ver o texto de saída em uma janela separada do navegador, selecione **Baixar**. Para baixar o texto propriamente dito, clique com o botão direito do mouse em **Baixar** e use as opções do navegador para salvar o conteúdo do arquivo.

1. Selecione o link da barra de trilha **WebJobs** na parte superior da página para ir para uma lista de WebJobs.

    ![Barra de trilha do WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![Lista de Trabalhos Web no painel de histórico](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>Próximas etapas

* Use o [SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki) Web para simplificar muitas tarefas de programação

* Aprenda a [desenvolver e implantar trabalhos Web com o Visual Studio](webjobs-dotnet-deploy-vs.md)