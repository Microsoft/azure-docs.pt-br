---
title: Investigar os alertas com o Azure Sentinel Preview | Microsoft Docs
description: Use este tutorial para saber como investigar alertas com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: a0a16fbda662ee1d3718dbafc4231de67aab277e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614837"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Tutorial: Detectar ameaças com a versão prévia do Azure Sentinel

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois que você [suas fontes de dados conectadas](quickstart-onboard.md) ao Azure Sentinel, você deseja ser notificado quando acontece algo suspeito. Para que você possa fazer isso, Sentinel do Azure permite que você crie regras de alerta, que geram casos em que você pode atribuir e avançadas use profundamente investigar ameaças em seu ambiente e anomalias. 

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras de detecção
> * Responda às ameaças

## <a name="create-detection-rules"></a>Criar regras de detecção

Para investigar os casos, primeiro você precisa criar regras de detecção. 

> [!NOTE]
> Alertas gerados no Azure Sentinel estão disponíveis por meio [segurança do Microsoft Graph](https://aka.ms/securitygraphdocs). Consulte a [documentação de alertas de segurança do Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) para obter mais detalhes e parceiros de integração.

Regras de detecção baseiam-se nos tipos de ameaças e anomalias que poderiam ser suspeitas em seu ambiente que você deseja saber sobre imediatamente, garantindo que sejam exibidas, investigados e corrigidos. 

1. No portal do Azure em Azure Sentinel, selecione **Analytics**.

   ![Análise](./media/tutorial-detect-threats/alert-rules.png)

2. Na barra de menus superior, clique em **+ adicionar**.  

   ![Criar regra de alerta](./media/tutorial-detect-threats/create-alert-rule.png)

3. Sob **criar regra de alerta**, forneça um nome descritivo e defina as **gravidade** conforme necessário. 

4. Criar a consulta no Log Analytics e, em seguida, cole-o na **conjunto de regra de alerta** campo. Aqui está um exemplo de consulta que seria alertá-lo quando um número anormal de recursos é criado na atividade do Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. No **mapeamento de entidade** seção, use os campos de **tipo de entidade** para mapear as colunas em sua consulta para campos de entidade reconhecidos pelo Azure Sentinel. Para cada campo, mapear a coluna relevante na consulta que você criou no Log Analytics para o campo de entidade adequado. Selecione o nome da coluna relevantes sob o **propriedade**. Cada entidade inclui vários campos, por exemplo, SID, GUID, etc. Você pode mapear a entidade de acordo com qualquer um dos campos, não apenas a entidade de nível superior.

6. Definir condições de gatilho de alerta **gatilho de alerta**. Isso define as condições que disparam o alerta. 

7. Defina as **frequência** para a frequência com que a consulta é executada – como com frequência cada 5 minutos ou raramente, como uma vez por dia. 

8. Defina as **período** para controlar a janela de tempo para a quantidade de dados a consulta é executada no - por exemplo, ele pode executado a cada hora entre 60 minutos de dados.

9. Você também pode definir as **supressão**. Supressão é útil quando você deseja parar alertas duplicados seja disparado para o mesmo incidente. Dessa forma, você pode parar alertas seja disparado durante um período específico. Isso pode ajudá-lo a evitar alertas duplicados para o mesmo incidente e permitem que você suprima alertas consecutivos por um período de tempo. Por exemplo, se o **agendamento de alerta** **frequência** está definido como 60 minutos e o **período de agendamento de alerta** é definido como duas horas, e os resultados da consulta foi ultrapassado o definido Além do limite, ele vai disparar um alerta duas vezes, depois, quando ele é detectada pela primeira vez nos últimos 60 minutos, e novamente quando ele está nos primeiros 60 minutos das horas de 2 de amostra de dados. É recomendável que, se um alerta for disparado, a supressão deve ser para a quantidade de tempo definido no período de alerta. Em nosso exemplo, você talvez queira definir supressão por 60 minutos, para que os alertas são disparados somente para eventos que ocorreram durante a hora mais recente.

8. Depois de colar a consulta para o **conjunto de regra de alerta** campo, você pode ver imediatamente uma simulação do alerta em **simulação de alerta de lógica** para que você possa ter a compreensão da quantidade de dados será gerado em um intervalo de tempo específico para o alerta que você criou. Isso dependerá do que você definiu **frequência** e **limite**. Se você vir que, em média, o alerta será disparado com muita frequência, você desejará definir o número de resultados mais alto para que fique acima da linha de base média.

9. Clique em **criar** para inicializar a regra de alerta. Depois que o alerta é criado, um caso é criado que contém o alerta. Você pode ver as regras de detecção definido como linhas na **análise de segurança** guia. Você também pode ver o número de correspondências para cada regra – os alertas disparados. Nesta lista, que você pode habilitar, desabilitar ou excluir cada regra. Você pode também selecione com o botão de reticências (...) no final da linha para cada alerta editar, desabilitar, clonar, Mostrar correspondências ou excluir uma regra. O **Analytics** página é uma galeria de todas as suas regras de alerta ativas, incluindo modelos você habilitar e você cria com base em modelos de regras de alerta.

1. Os resultados das regras de alerta podem ser vistos na **casos** página, onde você pode fazer a triagem, [investigar casos](tutorial-investigate-cases.md), e corrigir essas ameaças.



## <a name="respond-to-threats"></a>Responda às ameaças

Sentinela do Azure oferece duas opções principais para responder às ameaças usando as guias estratégicos. Você pode definir um guia estratégico para serem executados automaticamente quando um alerta é disparado, ou você pode executar manualmente um guia estratégico em resposta a um alerta.

- Defina um guia estratégico para serem executados automaticamente quando um alerta é disparado quando você configura o Guia estratégico. 

- Executar manualmente um guia estratégico de dentro do alerta, clicando em **exibir guias estratégicos** e, em seguida, selecionando um guia estratégico para ser executado.




## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como começar a usar detecção de ameaças usando o Azure Sentinel. 

Para aprender a automatizar suas respostas às ameaças, [como responder a ameaças usando as guias estratégicos automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responder a ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas às ameaças.

