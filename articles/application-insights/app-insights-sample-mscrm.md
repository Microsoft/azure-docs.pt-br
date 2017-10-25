---
title: Microsoft Dynamics CRM e Azure Application Insights | Microsoft Docs
description: "Obtenha a telemetria do Microsoft Dynamics CRM Online usando o Application Insights. Passo a passo da instalação, obtenção de dados, visualização e exportação."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: bwren
ms.openlocfilehash: a9593d5f198e05db80451a599428a296ed02e781
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Passo a passo: Ativar a telemetria para o Microsoft Dynamics CRM Online usando o Application Insights
Este artigo mostra como obter dados de telemetria no [Microsoft Dynamics CRM Online](https://www.dynamics.com/) usando o [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Percorreremos o processo completo de adição de um script do Application Insights ao seu aplicativo, captura de dados e visualização de dados.

> [!NOTE]
> [Procurar a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar o Application Insights a uma instância nova ou existente do CRM Online
Para monitorar seu aplicativo, você adiciona um SDK do Application Insights a ele. O SDK envia a telemetria para o [Portal do Application Insights](https://portal.azure.com), no qual você pode usar nossas potentes ferramentas de análise e diagnóstico ou exportar os dados para o armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso do Application Insights no Azure
1. Obtenha [uma conta no Microsoft Azure](http://azure.com/pricing). 
2. Entre no [Portal do Azure](https://portal.azure.com) e adicione um novo recurso do Application Insights. Este é o local em que os dados serão processados e exibidos.
   
    ![Clique em +, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Escolha ASP.NET como o tipo de aplicativo.
3. Abra a página de Introdução e abra "Monitorar e diagnosticar aplicativos do lado do cliente".
   
    ![Trecho de código para inserção na sua página da Web](./media/app-insights-sample-mscrm/03.png)

**Mantenha a página de código aberta** enquanto realiza a próxima etapa na outra janela do navegador. Você precisará do código em breve. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso da Web em JavaScript no Microsoft Dynamics CRM
1. Abra sua instância do CRM Online e faça logon com privilégios de administrador.
2. Abra o Microsoft Dynamics CRM Configurações, Personalizações, Personalizar o Sistema
   
    ![Configurações do Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/04.png)
   
    ![Configurações > Personalizações](./media/app-insights-sample-mscrm/05.png)

    ![Personalizar a opção do sistema](./media/app-insights-sample-mscrm/06.png)

1. Crie um recurso de JavaScript.
   
    ![Caixa de diálogo Novo Recurso da Web](./media/app-insights-sample-mscrm/07.png)
   
    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.
   
    ![Abrir o editor de texto](./media/app-insights-sample-mscrm/08.png)
2. Copie o código do Application Insights. Ao copiar, certifique-se de ignorar marcas de script. Consulte a captura de tela abaixo:
   
    ![Configurar sua chave de instrumentação](./media/app-insights-sample-mscrm/09.png)
   
    O código inclui a chave de instrumentação que identifica seu recurso do Application insights.
3. Salve e publique.
   
    ![Salvar e publicar](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formulários de instrumento
1. No Microsoft CRM Online, abra o formulário Conta
   
    ![Formulário da Conta](./media/app-insights-sample-mscrm/11.png)
2. Abra as propriedades do formulário
   
    ![Propriedades do formulário](./media/app-insights-sample-mscrm/12.png)
3. Adicione o recurso da Web de JavaScript que você criou
   
    ![Adicionar menu](./media/app-insights-sample-mscrm/13.png)
   
    ![Adicionar recurso da Web](./media/app-insights-sample-mscrm/14.png)
4. Salve e publique as personalizações do formulário.

## <a name="metrics-captured"></a>Métricas capturadas
Agora você configurou a captura de telemetria do formulário. Sempre que ele for usado, os dados serão enviados ao recurso do Application Insights.

Aqui estão exemplos dos dados que você verá.

#### <a name="application-health"></a>Integridade do aplicativo
![Tempo de carregamento de página de exemplo](./media/app-insights-sample-mscrm/15.png)

![Gráfico de exibições de página de exemplo](./media/app-insights-sample-mscrm/16.png)

Exceções de navegador:

![Gráfico de exceções de navegador](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![Lista de exceções](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso
![Usuários, seções e exibição de página](./media/app-insights-sample-mscrm/19.png)

![Gráficos de sessão](./media/app-insights-sample-mscrm/20.png)

![Versões do navegador](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Navegadores
![Divisão de tempo de carregamento de página](./media/app-insights-sample-mscrm/22.png)

![Contagem de sessões por versão do navegador](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização
![Contagem de sessões por país](./media/app-insights-sample-mscrm/24.png)

![Sessões e usuários por país](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitação de exibição de página interna
![Resumo de exibição de página](./media/app-insights-sample-mscrm/26.png)

![Pesquisar em eventos de exibição de página](./media/app-insights-sample-mscrm/27.png)

![Exibições de páginas semelhantes](./media/app-insights-sample-mscrm/28.png)

![Propriedades de exibição de página](./media/app-insights-sample-mscrm/29.png)

![Páginas por sessão](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Exemplo de código
[Procurar no código de amostra](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Você pode fazer análises ainda mais profundas se [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exemplo de Solução para o Microsoft Dynamics CRM
[Veja a solução do exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba mais
* [O que é o Application Insights?](app-insights-overview.md)
* [Application Insights para páginas da Web](app-insights-javascript.md)
* [Mais exemplos e explicações passo a passo](app-insights-code-samples.md)
