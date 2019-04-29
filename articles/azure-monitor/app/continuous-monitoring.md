---
title: Monitoramento contínuo de seu pipeline de lançamento de DevOps com o Azure DevOps e o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente o monitoramento contínuo com Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 41999defb01e024773b6364f169a1ce3b1377237
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902276"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicionar monitoramento contínuo ao seu pipeline de versão

O Azure DevOps Services integra-se ao Azure Application Insights para permitir o monitoramento contínuo de seu pipeline de lançamento de DevOps em todo o ciclo de vida de desenvolvimento de software. 

Agora, o Azure DevOps Services dá suporte ao monitoramento contínuo por meio do qual os pipelines de lançamento podem incorporar dados de monitoramento do Application Insights e de outros recursos do Azure. Quando um alerta do Application Insights for detectado, a implantação poderá permanecer aberta ou ser revertida até que o alerta seja resolvido. Se todas as verificações forem aprovadas, as implantações poderão prosseguir automaticamente dos testes até a produção sem a necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar o monitoramento contínuo

1. Selecione um projeto existente do Azure DevOps Services.

2. Passe o mouse sobre **Build e Versão** > Selecione **Versões** > Clique no **sinal de mais** > **Criar definição de versão** > Procure **Monitoramento** > **Implantação do Serviço de Aplicativo do Azure com Monitoramento Contínuo.**

   ![Novo pipeline de lançamento do Azure DevOps Services](media/continuous-monitoring/001.png)

3. Clique em **Aplicar.**

4. Ao lado do ponto de exclamação vermelho, selecione o texto em azul para **Exibir tarefas do ambiente.**

   ![Exibir tarefas do ambiente](media/continuous-monitoring/002.png)

   Uma caixa de configuração será exibida, use a tabela a seguir para preencher os campos de entrada.

    | Parâmetro        | Value |
   | ------------- |:-----|
   | **Nome do ambiente**      | Nome que descreva o ambiente do pipeline de lançamento |
   | **Assinatura do Azure** | A lista suspensa é preenchida com assinaturas do Azure vinculadas à organização do Azure DevOps Services|
   | **Nome do Serviço de Aplicativo** | A entrada manual de um novo valor pode ser necessária para esse campo, dependendo de outras seleções |
   | **Grupo de recursos**    | A lista suspensa é preenchida com os Grupos de Recursos disponíveis |
   | **Nome do recurso do Application Insights** | A lista suspensa é preenchida com todos os recursos do Application Insights que correspondem ao grupo de recursos selecionado anteriormente.

5. Selecione **Configurar alertas do Application Insights**

6. Para regras de alerta padrão, selecione **Salvar** > Insira um comentário descritivo > clique **OK**

## <a name="modify-alert-rules"></a>Modificar as regras de alerta

1. Para modificar as configurações de Alerta predefinidas, clique na caixa com **reticências...**  à direita do **Regras de alerta.**

   (Quatro regras de alerta prontas para uso estão presentes: Disponibilidade, Solicitações com falha, Tempo de resposta de servidor, Exceções de servidor.)

2. Clique no símbolo de lista suspensa ao lado de **Disponibilidade.**

3. Modifique o **Limite** de disponibilidade para atender aos requisitos de nível de serviço.

   ![Modificar Alerta](media/continuous-monitoring/003.png)

4. Selecione **OK** > **Salvar** > Insira um comentário descritivo > clique em **OK.**

## <a name="add-deployment-conditions"></a>Adicionar condições de implantação

1. Clique em **Pipeline** > Selecione o símbolo das condições de **Pré** ou **Pós-implantação**, dependendo do estágio que exija um portão de monitoramento contínuo.

   ![Condições de pré-implantação](media/continuous-monitoring/004.png)

2. Defina **Portões** como **Habilitado** > **Portões de aprovação**> clique em **Adicionar.**

3. Selecione **Azure Monitor** (essa opção oferece a capacidade para acessar alertas tanto do Azure Monitor quanto do Application Insights)

    ![Azure Monitor](media/continuous-monitoring/005.png)

4. Insira um valor de **Tempo limite de portões**.

5. Insira um **Intervalo de Amostragem.**

## <a name="deployment-gate-status-logs"></a>Logs de status de portão de implantação

Depois de adicionar portões de implantação, um alerta no Application Insights que excede o limite definido anteriormente protege a implantação da promoção indesejada de versão. Quando o alerta for resolvido, a implantação poderá continuar automaticamente.

Para observar esse comportamento, selecione **Versões** > Clique com o botão direito do mouse em Nome da versão **abra** > **Logs.**

![Logs](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Pipelines experimente estes [inícios rápidos.](https://docs.microsoft.com/azure/devops/pipelines)
