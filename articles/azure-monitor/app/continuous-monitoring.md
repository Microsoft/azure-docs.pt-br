---
title: Monitoramento contínuo de seu pipeline de versão do DevOps com o Azure Pipelines e o Aplicativo Azure insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente o monitoramento contínuo com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/16/2019
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c39a2f75fe74b61463af464078b4446bba07dec0
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277691"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicionar monitoramento contínuo ao seu pipeline de versão

O Azure Pipelines integra-se com o Aplicativo Azure insights para permitir o monitoramento contínuo de seu pipeline de versão do DevOps em todo o ciclo de vida do desenvolvimento de software. 

Com o monitoramento contínuo, os pipelines de versão podem incorporar dados de monitoramento de Application Insights e outros recursos do Azure. Quando o pipeline de liberação detecta um alerta de Application Insights, o pipeline pode portar ou reverter a implantação até que o alerta seja resolvido. Se todas as verificações forem aprovadas, as implantações poderão prosseguir automaticamente do teste até a produção, sem a necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar o monitoramento contínuo

1. No [Azure DevOps](https://dev.azure.com), selecione uma organização e um projeto.
   
1. No menu à esquerda da página do projeto, selecione  > **lançamentos**de pipelines. 
   
1. Solte a seta ao lado de **novo** e selecione **novo pipeline de liberação**. Ou, se você ainda não tiver um pipeline, selecione **novo pipeline** na página que aparece.
   
1. No painel **selecionar um modelo** , procure e selecione **Azure app implantação de serviço com monitoramento contínuo**e, em seguida, selecione **aplicar**. 

   ![Novo pipeline de lançamento de Azure Pipelines](media/continuous-monitoring/001.png)

1. Na caixa **estágio 1** , selecione o hiperlink para **exibir as tarefas do estágio.**

   ![Exibir tarefas do estágio](media/continuous-monitoring/002.png)

1. No painel configuração do **estágio 1** , preencha os seguintes campos: 

    | Parâmetro        | Valor |
   | ------------- |:-----|
   | **Nome do estágio**      | Forneça um nome de estágio ou deixe-o no **estágio 1**. |
   | **Assinatura do Azure** | Clique na lista suspensa e selecione a assinatura do Azure vinculada que você deseja usar.|
   | **Tipo de aplicativo** | Clique no menu suspenso e selecione seu tipo de aplicativo. |
   | **Nome do Serviço de Aplicativo** | Insira o nome do seu serviço de Azure App. |
   | **Nome do grupo de recursos para Application Insights**    | Clique no menu suspenso e selecione o grupo de recursos que deseja usar. |
   | **Nome do recurso do Application Insights** | Selecione o recurso de Application Insights para o grupo de recursos que você selecionou.

1. Para salvar o pipeline com as configurações de regra de alerta padrão, selecione **salvar** no canto superior direito na janela DevOps do Azure. Insira um comentário descritivo e, em seguida, selecione **OK**.

## <a name="modify-alert-rules"></a>Modificar as regras de alerta

Pronto para uso, a **implantação do serviço de Azure app com** o modelo de monitoramento contínuo tem quatro regras de alerta: **Disponibilidade**, **solicitações com falha**, **tempo de resposta do servidor**e **exceções de servidor**. Você pode adicionar mais regras ou alterar as configurações de regra para atender às suas necessidades de nível de serviço. 

Para modificar as configurações da regra de alerta:

1. No painel esquerdo da página de pipeline de liberação, selecione **configurar alertas de Application insights**.

1. No painel **alertas Azure monitor** , selecione as reticências **...** ao lado de **regras de alerta**.
   
1. Na caixa de diálogo **regras de alerta** , selecione o símbolo suspenso ao lado de uma regra de alerta, como **disponibilidade**. 
   
1. Modifique o **limite** e outras configurações para atender às suas necessidades.
   
   ![Modificar alerta](media/continuous-monitoring/003.png)
   
1. Selecione **OK**e, em seguida, selecione **salvar** no canto superior direito na janela DevOps do Azure. Insira um comentário descritivo e, em seguida, selecione **OK**.

## <a name="add-deployment-conditions"></a>Adicionar condições de implantação

Quando você adiciona Gates de implantação ao seu pipeline de lançamento, um alerta que excede os limites definidos impede a promoção de liberação indesejada. Depois de resolver o alerta, a implantação pode continuar automaticamente.

Para adicionar Gates de implantação:

1. Na página principal do pipeline, em **estágios**, selecione o símbolo **condições de pré-implantação** ou **pós-implantação** , dependendo de qual estágio precisa de um portão de monitoramento contínuo.
   
   ![Condições de pré-implantação](media/continuous-monitoring/004.png)
   
1. No painel configuração de **condições de pré-implantação** , defina **Gates** como **habilitado**.
   
1. Ao lado de **entradas de implantação**, selecione **Adicionar**.
   
1. Selecione **Azure monitor alertas de consulta** no menu suspenso. Essa opção permite que você acesse os alertas de Azure Monitor e Application Insights.
   
   ![Alertas de Azure Monitor de consulta](media/continuous-monitoring/005.png)
   
1. Em **Opções de avaliação**, insira os valores desejados para configurações como **o tempo entre a reavaliação de Gates** e **o tempo limite após o qual as Gates falharão**. 

## <a name="view-release-logs"></a>Exibir logs de liberação

Você pode ver o comportamento do portão de implantação e outras etapas de versão nos logs de liberação. Para abrir os logs:

1. Selecione **versões** no menu à esquerda da página pipeline. 
   
1. Selecione qualquer versão. 
   
1. Em **estágios**, selecione qualquer estágio para exibir um resumo de lançamento. 
   
1. Para exibir os logs, selecione **Exibir logs** no resumo da versão, selecione o hiperlink **bem-sucedido** ou **com falha** em qualquer estágio, ou passe o mouse sobre qualquer estágio e selecione **logs**. 
   
   ![Exibir logs de liberação](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Azure Pipelines, consulte a [documentação do Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines).
