---
title: Anotações de versão para o Application Insights | Microsoft Docs
description: Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604555"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações sobre gráficos de métricas no Application Insights

As anotações [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) gráficos mostram onde você implantou um novo build ou outros eventos significativos. Anotações tornam fácil ver se suas alterações tinham qualquer efeito sobre o desempenho do aplicativo. Elas podem ser criadas automaticamente pelo [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) sistema de compilação. Você também pode criar anotações para sinalizar eventos desejados criando-as no PowerShell.

> [!NOTE]
> Este artigo reflete a experiência de métricas clássicas **reprovada**. Anotações só estão disponíveis no momento em que a experiência clássica em **[pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)** . Para saber mais sobre a experiência de métricas atual, consulte [recursos avançados do Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Anotações de versão com a compilação de Pipelines do Azure

Anotações de versão são um recurso do serviço baseado em nuvem Azure Pipelines de DevOps do Azure.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão Annotations (uma vez)
Para poder criar anotações de versão, você precisará instalar uma das muitas extensões de DevOps do Azure disponíveis no Visual Studio Marketplace.

1. Entrar no seu [DevOps do Azure](https://azure.microsoft.com/services/devops/) projeto.
   
1. No Visual Studio Marketplace [extensão anotações de versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) página, selecione sua organização de DevOps do Azure e, em seguida, selecione **instalar** para adicionar a extensão para sua organização de DevOps do Azure.
   
   ![Selecione uma organização de DevOps do Azure e, em seguida, selecione instalar.](./media/annotations/1-install.png)
   
Você só precisa instalar a extensão de uma vez para sua organização de DevOps do Azure. Agora você pode configurar as anotações de versão para qualquer projeto na sua organização.

### <a name="configure-release-annotations"></a>Criar anotações de versão

Crie uma chave de API separada para cada um dos seus modelos de versão de Pipelines do Azure.

1. Entrar para o [portal do Azure](https://portal.azure.com) e abra o recurso Application Insights que monitora seu aplicativo. Ou, se você não tiver uma, [criar um novo recurso do Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Abra o **acesso à API** guia e copiar a **ID do Application Insights**.
   
   ![Em acesso de API, copie a ID do aplicativo.](./media/annotations/2-app-id.png)

1. Em uma janela separada do navegador, abra ou crie o modelo de versão que gerencia suas implantações de Pipelines do Azure.
   
1. Selecione **adicionar tarefa**e, em seguida, selecione o **anotação de versão do Application Insights** tarefa no menu.
   
   ![Selecione Adicionar tarefa e selecione anotação de versão do Application Insights.](./media/annotations/3-add-task.png)
   
1. Sob **ID do aplicativo**, cole a ID do Application Insights você copiou o **acesso à API** guia.
   
   ![Cole a ID do Application Insights](./media/annotations/4-paste-app-id.png)
   
1. No Application Insights **acesso à API** janela, selecione **criar chave de API**. 
   
   ![Na guia acesso à API, selecione Criar chave de API.](./media/annotations/5-create-api-key.png)
   
1. No **criar chave de API** janela, digite uma descrição, selecione **gravar anotações**e, em seguida, selecione **gerar chave**. Copie a nova chave.
   
   ![Na janela de chave de API de criação, digite uma descrição, marque gravar anotações e, em seguida, selecione Gerar chave.](./media/annotations/6-create-api-key.png)
   
1. Na janela de modelo de versão, sobre o **variáveis** guia, selecione **Add** para criar uma definição de variável para a nova chave de API.

1. Sob **nome**, insira `ApiKey`e, em **valor**, cole a chave de API que você copiou do **acesso à API** guia.
   
   ![Na guia variáveis de DevOps do Azure, selecione Add, o nome da variável ApiKey e cole a chave de API em valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **salvar** na janela de modelo de versão principal para salvar o modelo.

## <a name="view-annotations"></a>Exibir anotações
Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação é enviada ao Application Insights. As anotações são exibidos nos gráficos **Metrics Explorer**.

Selecione qualquer marcador de anotação (seta cinza clara) para abrir os detalhes sobre a versão, incluindo o solicitante, ramificação de controle do código-fonte, pipeline de lançamento e ambiente.

![Selecione um marcador de anotação de versão.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas no PowerShell
Você pode usar o [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) script do PowerShell do GitHub para criar anotações de qualquer processo que desejar, sem o uso de DevOps do Azure. 

1. Faça uma cópia local do [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Use as etapas no procedimento anterior para obter sua ID do Application Insights e criar uma chave de API a partir do Application Insights **acesso à API** guia.
   
1. Chame o script do PowerShell com o código a seguir, substituindo os espaços reservados entre colchetes angulares com seus valores. O `-releaseProperties` são opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Você pode modificar o script, por exemplo, para criar anotações para os últimos.

## <a name="next-steps"></a>Próximas etapas

* [Criar um item de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automação com o PowerShell](../../azure-monitor/app/powershell.md)
