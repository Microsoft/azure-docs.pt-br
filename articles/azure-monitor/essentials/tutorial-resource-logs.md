---
title: Coletar logs de recursos de um Recurso do Azure e analisá-los com Azure Monitor
description: Tutorial para definir as configurações de diagnóstico para coletar logs de recurso de um recurso do Azure para um workspace do Log Analytics em que eles podem ser analisados com uma consulta de log.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 43a9c88bc64b688bfd1171e331232bb254b0eebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032867"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Coletar e analisar logs de recursos de um recurso do Azure

Os logs de recursos fornecem informações sobre a operação detalhada de um recurso do Azure e são úteis para monitorar a integridade e a disponibilidade. Os recursos do Azure geram logs de recursos automaticamente, mas você deve configurar o local de que eles devem ser coletados. Este tutorial orienta você pelo processo de criação de uma configuração de diagnóstico para coletar logs de recursos para um recurso em sua assinatura do Azure e analisá-los com uma consulta de log.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um workspace do Log Analytics no Azure Monitor
> * Criar uma configuração de diagnóstico para coletar logs de recurso 
> * Criar uma consulta de log simples para analisar logs


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um recurso do Azure para monitorar. Você poderá usar qualquer recurso da sua assinatura do Azure que seja compatível com configurações de diagnóstico. Para determinar se um recurso é compatível com configurações de diagnóstico, acesse seu menu no portal do Azure e verifique se há a opção **Configurações de diagnóstico** na seção **Monitoramento** do menu.


## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Criar um workspace
Um workspace do Log Analytics no Azure Monitor coleta e indexa dados de log de uma variedade de fontes e permite uma análise avançada usando uma linguagem de consulta eficiente. O workspace do Log Analytics precisa existir antes de você criar uma configuração de diagnóstico para enviar dados para ele. Você pode usar um workspace existente em sua assinatura do Azure ou criar um com o procedimento a seguir. 

> [!NOTE]
> Ao trabalhar com os dados nos workspaces do Log Analytics no menu do **Azure Monitor**, você cria e gerencia workspaces no menu **Workspaces do Log Analytics**.

1. Em **Todos os serviços**, selecione **Workspaces do Log Analytics**.
2. Clique em **Adicionar** na parte superior da tela e forneça os seguintes detalhes para o workspace:
   - **Espaço de Trabalho do Log Analytics**: Nome do novo workspace. Esse nome deve ser globalmente exclusivo em todas as assinaturas do Azure Monitor.
   - **Assinatura**: Selecione a assinatura para armazenar o workspace. Essa assinatura não precisa ser igual à do recurso que está sendo monitorado.
   - **Grupo de Recursos**: Selecione um grupo de recursos ou clique em **Criar** para criar um. Esse grupo de recursos não precisa ser igual ao do recurso que está sendo monitorado.
   - **Localização**: Selecione uma região do Azure ou crie uma. Essa localização não precisa ser igual à do recurso que está sendo monitorado.
   - **Tipo de preço**: selecione *Pagamento Conforme o Uso* como o tipo de preço. Você pode alterar esse tipo de preço mais tarde. Clique no link **preço do Log Analytics** para saber mais sobre diferentes tipos de preço.

    ![Novo workspace](media/tutorial-resource-logs/new-workspace.png)

3. Clique em **OK** para criar o workspace.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
As [configurações de diagnóstico](../essentials/diagnostic-settings.md) definem para que local os logs de recursos devem ser enviados para um recurso específico. Uma única configuração de diagnóstico pode ter vários [destinos](../essentials/diagnostic-settings.md#destinations), mas usaremos apenas um workspace do Log Analytics neste tutorial.

1. Na seção **Monitoramento** do menu do recurso, selecione **Configurações de diagnóstico**.
2. Você deve ter uma mensagem "Nenhuma configuração de diagnóstico definida". Clique em **Adicionar configuração de diagnóstico**.

    ![Configurações de Diagnóstico](media/tutorial-resource-logs/diagnostic-settings.png)

3. Cada configuração de diagnóstico tem três partes básicas:
 
   - **Name**: Isso não tem nenhum efeito significativo e deve ser simplesmente descritivo para você.
   - **Destinos**: Um ou mais destinos para enviar os logs. Todos os serviços do Azure compartilham o mesmo conjunto de três destinos possíveis. Cada configuração de diagnóstico pode definir um ou mais destinos, mas não mais de um destino de um tipo específico. 
   - **Categorias**: Categorias de logs para enviar a cada um dos destinos. O conjunto de categorias vai variar para cada serviço do Azure.

4. Selecione **Enviar para o workspace do Log Analytics** e, em seguida, selecione o workspace que você criou.
5. Selecione as categorias que você deseja coletar. Veja a documentação de cada serviço para obter uma definição de suas categorias disponíveis.

    ![Configuração de diagnóstico](media/tutorial-resource-logs/diagnostic-setting.png)

6. Clique em **Salvar** para salvar as configurações de diagnóstico.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Usar uma consulta de log para recuperar logs
Os dados são recuperados de um workspace do Log Analytics usando uma consulta de log escrita em KQL (Linguagem de Consulta Kusto). As informações e soluções no Azure Monitor fornecerão consultas de log para recuperar dados de um serviço específico, mas você pode trabalhar diretamente com consultas de log e seus resultados no portal do Azure com o Log Analytics. 

1. Na seção **Monitoramento** do menu do recurso, selecione **Logs**.
2. O Log Analytics é aberto com uma janela de consulta vazia com o escopo definido para seu recurso. Todas as consultas incluirão somente os registros desse recurso.

    > [!NOTE]
    > Se você tiver aberto os logs no menu Azure Monitor, o escopo será definido como o workspace do Log Analytics. Nesse caso, todas as consultas incluirão todos os registros no workspace.
   
    ![A captura de tela mostra os Logs de um aplicativo lógico exibindo uma nova consulta com o nome do aplicativo lógico realçado.](media/tutorial-resource-logs/logs.png)

4. O serviço mostrado no exemplo grava logs de recursos na tabela **AzureDiagnostics**, mas outros serviços podem gravar em outras tabelas. Confira [Serviços, esquemas e categorias compatíveis com logs de recursos do Azure](../essentials/resource-logs-schema.md) para tabelas usadas por diferentes serviços do Azure.

    > [!NOTE]
    > Vários serviços gravam logs de recursos na tabela AzureDiagnostics. Se você iniciar o Log Analytics no menu Azure Monitor, precisará adicionar uma instrução `where` com a coluna `ResourceProvider` para especificar seu serviço específico. Quando você inicia o Log Analytics no menu de um recurso, o escopo é definido como somente registros desse recurso, de modo que essa coluna não é necessária. Confira a documentação do serviço para obter exemplos de consultas.


5. Digite uma consulta e clique em **Executar** para inspecionar os resultados. 
6. Confira [Introdução às consultas de log no Azure Monitor](../logs/get-started-queries.md) ara um tutorial sobre como escrever consultas de log.

    ![Consulta de log](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a coletar logs de recursos em um workspace do Log Analytics, conclua um tutorial sobre como escrever consultas de log para analisar esses dados.

> [!div class="nextstepaction"]
> [Introdução às consultas de log no Azure Monitor](../logs/get-started-queries.md)