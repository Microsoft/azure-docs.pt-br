---
title: Envie alertas do Azure Application Insights | Microsoft Docs
description: Tutorial para enviar alertas em resposta a erros em seu aplicativo usando o Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 78a1a0c7890ff88f993e215e0af9c0749291e3ef
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383262"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorar e alertar sobre a integridade do aplicativo com o Azure Application Insights

O Azure Application Insights permite monitorar seu aplicativo e enviar alertas quando ele não está disponível, apresentando falhas ou sofrendo problemas de desempenho.  Este tutorial descreve o processo de criação de testes para verificar de forma contínua a disponibilidade de seu aplicativo.

Você aprenderá como:

> [!div class="checklist"]
> * Criar um teste de disponibilidade para verificar continuamente a resposta do aplicativo
> * Enviar email aos administradores quando ocorrer um problema

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Crie um [recurso do Application Insights](../app/create-new-resource.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade

Testes de disponibilidade no Application Insights permitem testar automaticamente seu aplicativo de vários locais em todo o mundo.   Neste tutorial, você executará um teste de URL para garantir que o aplicativo Web esteja disponível.  Você também pode criar um passo a passo completo para testar sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione sua assinatura.  

2. Selecione **Disponibilidade** no menu **Investigar** e, em seguida, clique em **Criar teste**.

    ![Adicionar teste de disponibilidade](media/tutorial-alert/add-test-001.png)

3. Digite um nome para o teste e deixe os outros padrões.  Essa seleção disparará as solicitações para a URL do aplicativo a cada 5 minutos em cinco localizações geográficas diferentes.

4. Selecione **Alertas** para abrir o menu suspenso **Alertas**, no qual você poderá definir os detalhes de como responder em caso de falha do teste. Escolha **Quase em tempo real** e defina o status como **Habilitado.**

    Tipo de um endereço de email para enviar quando os critérios de alerta forem atendidos.  Opcionalmente, você pode digitar o endereço de um webhook para chamar quando os critérios de alerta forem atendidos.

    ![Criar teste](media/tutorial-alert/create-test-001.png)

5. Retorne ao painel de teste, selecione as reticências e edite o alerta para inserir a configuração para o alerta quase em tempo real.

    ![Editar alerta](media/tutorial-alert/edit-alert-001.png)

6. Defina as localizações com falha como superior ou igual a 3. Crie um [grupo de ações](../alerts/action-groups.md) para configurar quem será notificado quando o limite de alerta for violado.

    ![Salvar a interface do usuário do alerta](media/tutorial-alert/save-alert-001.png)

7. Depois de configurar o alerta, clique no nome do teste para exibir os detalhes de cada localização. Os testes podem ser exibidos no formato grafo de linhas ou gráfico de dispersão para visualizar o êxito/as falhas de determinado intervalo de tempo.

    ![Detalhes do teste](media/tutorial-alert/test-details-001.png)

8. Faça uma busca detalhada em qualquer teste clicando em seu ponto no gráfico de dispersão. Isso iniciará a exibição de detalhes da transação completa. O exemplo a seguir mostra os detalhes de uma solicitação com falha.

    ![Resultado do teste](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a alertar sobre problemas, avance para o próximo tutorial para aprender a analisar como os usuários estão interagindo com o seu aplicativo.

> [!div class="nextstepaction"]
> [Entender os usuários](./tutorial-users.md)

