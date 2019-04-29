---
title: Exportar para o Power BI do Azure Application Insights | Microsoft Docs
description: As consultas do Analytics podem ser exibidas no Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: a57393918992019844e2ff4ccc13d671f0b90ed5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900062"
---
# <a name="feed-power-bi-from-application-insights"></a>Alimentar o Power BI do Application Insights
O [Power BI](https://www.powerbi.com/) é um conjunto de ferramentas de negócios que ajudam a analisar dados e a compartilhar informações. Painéis avançados estão disponíveis em cada dispositivo. Você pode combinar dados de várias fontes, incluindo consultas do Analytics do [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Há três métodos de exportação de dados do Application Insights para Power BI:

* [**Exportar consultas do Analytics**](#export-analytics-queries). Este é o método preferencial. Grave qualquer consulta que você deseja e exporte-a para o Power BI. Você pode colocar essa consulta em um painel com outros dados.
* [**A exportação contínua e o Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Este método será útil, se você quiser armazenar os dados por longos períodos de tempo. Se você não tiver um requisito de retenção de dados estendido, use o método de consulta de análise de exportação. A exportação contínua e o Stream Analytics envolvem mais trabalho para configurar e sobrecarga adicional de armazenamento.
* **Adaptador do Power BI**. O conjunto de gráficos é predefinido, mas você pode adicionar suas próprias consultas de outras fontes.

> [!NOTE]
> O adaptador do Power BI agora está **preterido**. Os gráficos predefinidos para essa solução são preenchidos por consultas não editáveis estáticas. Não é possível editar essas consultas e, dependendo de determinadas propriedades dos dados, é possível que a conexão com o Power BI seja executada com êxito, mas nenhum dado preenchido. Isso ocorre devido aos critérios de exclusão definidos na consulta codificada. Embora essa solução ainda funcione para alguns clientes, devido à falta de flexibilidade do adaptador, a solução recomendada é usar a funcionalidade [**exportar consulta do Analytics**](#export-analytics-queries).

## <a name="export-analytics-queries"></a>Exportar consultas do Analytics
Essa rota permite que você grave qualquer consulta do Analytics desejada ou que a exporte do Funil de Uso, e então fazer a exportação para um painel Power BI. (Você pode adicionar ao painel criado pelo adaptador).

### <a name="one-time-install-power-bi-desktop"></a>Uma vez: instalar o Power BI Desktop
Para importar sua consulta do Application Insights, você deve usar a versão da área de trabalho do Power BI. Em seguida, você poderá publicá-la na Web ou em seu workspace de nuvem do Power BI. 

Instalar o [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportar uma consulta do Analytics
1. [Abra o Analytics e escreva sua consulta](../../azure-monitor/log-query/get-started-portal.md).
2. Teste e ajuste a consulta até ficar satisfeito com os resultados. Certifique-se de que a consulta seja executada corretamente no Analytics antes de ser exportada.
3. No menu **Exportar**, escolha **Power BI (M)**. Salve o arquivo de texto.
   
    ![Captura de tela do Analytics, com o menu Exportar realçado](./media/export-power-bi/analytics-export-power-bi.png)
4. No Power BI Desktop, selecione **Obter Dados** > **Consulta em Branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor Avançado**.

    Cole o script M Language exportado no Editor Avançado.

    ![Captura de tela do Power BI Desktop, com o Editor Avançado realçado](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Para permitir que o Power BI acesse o Azure, talvez seja necessário fornecer credenciais. Use a **conta Organizacional** para entrar com sua conta da Microsoft.
   
    ![Captura de tela da caixa de diálogo Configurações de Consulta do Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Se você precisar verificar as credenciais, use o comando de menu **Configurações de Fonte de Dados** no editor de consultas. Especifique as credenciais usadas para o Azure, que podem ser diferentes das suas credenciais para o Power BI.
6. Escolha uma visualização para a sua consulta e selecione os campos dos eixos x, y e a dimensão de segmentação.
   
    ![Captura de tela das opções de visualização do Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publique seu relatório em seu workspace de nuvem do Power BI. A partir daí, você pode inserir uma versão sincronizada em outras páginas da Web.
   
    ![Captura de tela do Power BI Desktop, com o botão Publicar realçado](./media/export-power-bi/publish-power-bi.png)
8. Atualize o relatório manualmente em intervalos ou configure uma atualização agendada na página de opções.

### <a name="export-a-funnel"></a>Exportar um Funil
1. [Crie o seu funil](../../azure-monitor/app/usage-funnels.md).
2. Selecione **Power BI**.

   ![Captura de tela do botão do Power BI](./media/export-power-bi/button.png)

3. No Power BI Desktop, selecione **Obter Dados** > **Consulta em Branco**. Em seguida, no editor de consultas, em **Exibir**, selecione **Editor Avançado**.

   ![Captura de tela do Power BI Desktop, com o botão Consulta em Branco realçado](./media/export-power-bi/blankquery.png)

   Cole o script M Language exportado no Editor Avançado. 

   ![Captura de tela do Power BI Desktop, com o Editor Avançado realçado](./media/export-power-bi/advancedquery.png)

4. Selecione os itens da consulta e escolha a visualização de Funil.

   ![Captura de tela das opções de visualização do Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Altere o título para torná-lo significativo e publique seu relatório no seu workspace de nuvem do Power BI. 

   ![Captura de tela do Power BI Desktop, com a alteração de título realçada](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>solução de problemas

Você pode encontrar erros relacionados às credenciais ou ao tamanho do conjunto de dados. Aqui estão algumas informações sobre o que fazer com esses erros.

### <a name="unauthorized-401-or-403"></a>Não Autorizado (401 ou 403)
Isso pode acontecer se o token de atualização não tiver sido atualizado. Repita estas etapas para garantir que você ainda terá acesso:

1. Faça logon no portal do Azure e certifique-se de que você pode acessar o recurso.
2. Tente atualizar as credenciais para o painel.

   Se você tiver acesso e a atualização das credenciais não funcionar, abra um tíquete de suporte.

### <a name="bad-gateway-502"></a>Gateway Incorreto (502)
Isso geralmente é causado por uma Consulta de análise que retorna um número de dados excessivo. Tente usar um intervalo de tempo menor para a consulta. 

Se reduzir o conjunto de dados da consulta de análise não atender às suas necessidades, considere o uso da [API](https://dev.applicationinsights.io/documentation/overview) para efetuar pull de um conjunto de dados maior. Veja como converter a exportação de Consulta M para usar a API.

1. Criar uma [chave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Atualize o script M do Power BI que você exportou do Analytics, substituindo a URL do Azure Resource Manager com a API do Application Insights.
   * Substitua **https:\//management.azure.com/subscriptions/...**
   * com, **https:\//api.applicationinsights.io/beta/apps/...**
3. Por fim, atualize as credenciais para as básicas e use sua chave de API.

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script atualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Sobre amostragem
Dependendo da quantidade de dados enviados pelo aplicativo, talvez você queira usar o recurso de amostragem adaptativa que envia apenas uma porcentagem da sua telemetria. Isso também será verdadeiro se você tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adaptador Power BI (preterido)
Esse método cria um painel completo de telemetria para você. O conjunto de dados inicial é predefinido, mas você pode adicionar mais dados a ele.

### <a name="get-the-adapter"></a>Obter o adaptador
1. Entre no [Power BI](https://app.powerbi.com/).
2. Abra **Obter Dados** ![Captura de tela do ícone GetData no canto inferior esquerdo](./media/export-power-bi/001.png), **Serviços**.

    ![Capturas de tela de Obter da fonte de dados do Application Insights](./media/export-power-bi/002.png)

3. Selecione **Obter agora** no Application Insights.

   ![Capturas de tela de Obter da fonte de dados do Application Insights](./media/export-power-bi/003.png)
4. Forneça os detalhes do recurso do Application Insights e, em seguida, **Entrar**.

    ![Captura de tela de Obter da fonte de dados do Application Insights](./media/export-power-bi/005.png)

     Essas informações podem ser encontradas no painel de Visão Geral do Application Insights:

     ![Captura de tela de Obter da fonte de dados do Application Insights](./media/export-power-bi/004.png)

5. Abra o Aplicativo do Power BI do Application Insights criado recentemente.

6. Aguarde um minuto ou dois para que os dados sejam importados.

    ![Captura de tela de adaptador do Power BI](./media/export-power-bi/010.png)

Você pode editar o painel, combinando os gráficos do Application Insights a outros de outras fontes e a consultas do Analytics. Você pode obter mais gráficos na galeria de visualização e cada um deles possui parâmetros que podem ser definidos.

Após a importação inicial, o painel e os relatórios continuarão a ser atualizados diariamente. Você pode controlar o agendamento de atualização no conjunto de dados.

## <a name="next-steps"></a>Próximas etapas
* [Power BI - Saiba mais](https://www.powerbi.com/learning/)
* [Tutorial do Analytics](../../azure-monitor/log-query/get-started-portal.md)

