---
title: Trabalho Web, tarefas em segundo plano, no Azure
description: Saiba mais sobre trabalhos Web.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452264"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Trabalhos Web executam tarefas em segundo plano no serviço Azure App

Este artigo mostra como implantar o WebJobs usando o [portal do Azure](https://portal.azure.com) para carregar um executável ou um script. Para obter informações sobre como desenvolver e implantar o WebJobs usando o Visual Studio, consulte [Implantar o WebJobs usando o Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Visão geral
O webjobs é um recurso do [serviço de Azure app](index.yml) que permite executar um programa ou script na mesma instância que um aplicativo Web, aplicativo de API ou aplicativo móvel. Não há nenhum custo adicional para usar Trabalhos Web.

> [!IMPORTANT]
> O WebJobs ainda não é suportado para o Serviço de Aplicativo no Linux.

O SDK do WebJobs do Azure pode ser usado com o WebJobs para simplificar muitas tarefas de programação. Para obter mais informações, consulte [O que é o SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk/wiki).

O Azure Functions oferece outra maneira de executar programas e scripts. Para obter uma comparação entre o WebJobs e o Functions, consulte [Escolher entre o Flow, Aplicativos Lógicos, Functions e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de WebJob

A tabela a seguir descreve as diferenças entre WebJobs *contínuos* e *disparados*.


|Contínuo  |Disparado  |
|---------|---------|
| É iniciado imediatamente quando o WebJob é criado. Para impedir o encerramento do trabalho, o programa ou o script normalmente faz seu trabalho dentro de um loop infinito. Se o trabalho for encerrado, você poderá reiniciá-lo. | É iniciado apenas quando disparado manualmente ou de acordo com um agendamento. |
| É executado em todas as instâncias nas quais o aplicativo Web é executado. Opcionalmente, você pode restringir o WebJob a uma única instância. |É executado em uma única instância selecionada pelo Azure para balanceamento de carga.|
| Dá suporte à depuração remota. | Não dá suporte à depuração remota.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Adicionar WebJob ao controle do código-fonte

Se você tiver o controle do código-fonte configurado com seu aplicativo, os trabalhos Web deverão ser implantados como parte da integração do controle do código-fonte. Depois que o controle do código-fonte é configurado com seu aplicativo, um WebJob não pode ser adicionado do portal do Azure.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipos de arquivo com suporte para scripts ou programas

Há suporte para os tipos de arquivo a seguir:

* .cmd, .bat, .exe (usando o cmd do Windows)
* .ps1 (usando o PowerShell)
* .sh (usando o Bash)
* .php (usando o PHP)
* .py (usando o Python)
* .js (usando o Node.js)
* .jar (usando o Java)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um WebJob](./webjobs-create-ieux.md)
* Exibir histórico de log de [trabalhos](./webjobs-create-ieux-view-log.md) Web
