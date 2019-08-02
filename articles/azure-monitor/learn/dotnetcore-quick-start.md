---
title: Início rápido com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente um Aplicativo Web ASP.NET Core para monitoramento com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 931de532aa6e09b2cd00955df6ba1f05d7e4f42c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428506"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Começar a monitorar o aplicativo Web ASP.NET Core

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo Web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. 

Este guia de início rápido lhe ajuda a adicionar o SDK do Application Insights para um aplicativo Web ASP.NET Core existente. Para saber mais sobre como configurar o Application Insights sem o Visual Studio, confira esse [artigo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- [Instale o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
  - Desenvolvimento Web e ASP.NET
  - Desenvolvimento do Azure
- [Instalar o SDK 2.0 do .NET Core](https://www.microsoft.com/net/core)
- Você precisará de uma assinatura do Azure e de um aplicativo Web .NET Core existente.

Se você não tiver um aplicativo Web ASP.NET Core, poderá usar nosso guia passo a passo para [criar um aplicativo ASP.NET Core e adicionar o Application Insights.](../../azure-monitor/app/asp-net-core.md)

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, independentemente de ele estar sendo executado localmente ou na nuvem. Use as etapas a seguir para começar a exibir esses dados.

1. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez criando um recurso do Application Insights, você pode aprender mais no documento [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

    Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

   | Configurações        |  Valor           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome para o novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | Leste dos EUA | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

2. Clique em **Criar**.

## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Abra o **projeto** de aplicativo Web ASP.NET Core no Visual Studio > Clique com o botão direito do mouse no AppName no **Gerenciador de Soluções** > Selecione **Adicionar** > **Application Insights Telemetry**.

    ![Adicionar Application Insights Telemetry](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Clique no botão **Introdução**

3. Selecione sua conta e assinatura > Selecione o **Recurso existente** criado no portal do Azure > Clique em **Registrar**.

4. Selecione **Depurar** > **Iniciar sem Depuração** (Ctrl + F5) para iniciar seu aplicativo

    ![Menu Visão Geral do Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Leva 3 a 5 minutos para que os dados comecem a aparecer no portal. Se esse aplicativo é um aplicativo de teste de baixo tráfego, tenha em mente que a maioria das métricas só são capturadas quando há operações ou solicitações ativas.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Reabra a página **Visão Geral** do Application Insights no portal do Azure selecionando **Página Inicial** e, em recursos recentes, selecione o recurso que você criou anteriormente para exibir detalhes sobre seu aplicativo em execução no momento.

   ![Menu Visão Geral do Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Clique em **Mapa do aplicativo** para obter um layout visual das relações de dependência entre os componentes do aplicativo. Cada componente mostra KPIs como alertas, desempenho, falhas e carregamento.

   ![Mapa de aplicativo](./media/dotnetcore-quick-start/5appmap.png)

3. Clique no ícone **Análise do Aplicativo** ![ícone Mapa do Aplicativo](./media/dotnetcore-quick-start/006.png) **Exibir no Analytics**. Isso abre a **Análise do Application Insights**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Nesse caso, uma consulta que renderiza a contagem de solicitações como um gráfico é gerada para você. Você pode escrever suas próprias consultas para analisar outros dados.

   ![Grafo de análise de solicitações de usuário durante um período de tempo](./media/dotnetcore-quick-start/6analytics.png)

4. Retorne à página **Visão Geral** e examine os Painéis de KPI.  Esse painel fornece estatísticas sobre a integridade do aplicativo, incluindo o número de solicitações de entrada, a duração dessas solicitações e as falhas que ocorrem. 

   ![Grafos de linha do tempo de Visão Geral de Integridade](./media/dotnetcore-quick-start/7kpidashboards.png)

5. À esquerda, clique em **Métricas**. Use o Metrics Explorer para investigar a integridade e o uso do recurso. Você pode clicar em **Adicionar novo gráfico** para criar exibições personalizadas adicionais ou selecionar **Editar** para modificar a altura, paleta de cores, agrupamentos, métricas e tipos de gráfico existentes. Por exemplo, é possível fazer um gráfico que exiba o tempo médio de carregamento de página de um navegador selecionando "Tempo de carregamento de página do navegador" no menu suspenso de métricas e "Média" de agregação. Para saber mais sobre o Azure Metrics Explorer, acesse [Introdução ao Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Guia Métricas: Gráfico de tempo médio de carregamento de página do navegador](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Vídeo

- Vídeo externo de passo a passo sobre [configuração do Application Insights com um .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) a partir do zero.
- Vídeo externo de passo a passo sobre [configuração do Application Insights com um .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) a partir do zero.

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar de testar, poderá excluir o grupo de recursos e todos os recursos relacionados. Para isso, siga as etapas abaixo.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**.
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar exceções de tempo de execução](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
