---
title: Investigar alertas com o Azure Sentinel Preview | Microsoft Docs
description: Use este tutorial para aprender a investigar alertas com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: ad9c752898733286701db2d0f0b1fc40029b7521
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370690"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Tutorial: Detectar ameaças com o Azure Sentinel Preview

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois de [conectar suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, você deseja ser notificado quando algo suspeito acontecer. Para permitir que você faça isso, o Azure Sentinel permite que você crie regras de alerta avançadas, que geram casos que você pode atribuir e usar para investigar profundamente as anomalias e ameaças em seu ambiente. 

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras de detecção
> * Automatizar respostas de ameaças

## <a name="create-detection-rules"></a>Criar regras de detecção

Para investigar os casos, primeiro você precisa criar regras de detecção. 

> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis por meio do [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Consulte a [documentação de alertas de segurança do Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) para obter mais detalhes e parceiros de integração.

As regras de detecção baseiam-se nos tipos de ameaças e anomalias que podem ser suspeitas em seu ambiente que você deseja conhecer imediatamente, garantindo que elas sejam apresentadas, investigadas e corrigidas. 

1. No portal do Azure em Azure Sentinel, selecione **análise**.

   ![Análise](./media/tutorial-detect-threats/alert-rules.png)

2. Na barra de menus superior, clique em **+ Adicionar**.  

   ![Criar regra de alerta](./media/tutorial-detect-threats/create-alert-rule.png)

3. Em **criar regra de alerta**, forneça um nome descritivo e defina a **severidade** conforme necessário. 

4. Crie a consulta no Log Analytics e cole-a no campo **definir regra de alerta** . Aqui está um exemplo de consulta que alertaria quando um número anormal de recursos for criado na atividade do Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > O comprimento da consulta deve ter entre 1 e 10000 caracteres e não pode conter "Search *" e "Union *".


5. Na seção **mapeamento de entidade** , use os campos em **tipo de entidade** para mapear as colunas em sua consulta para os campos de entidade reconhecidos pelo Azure sentinela. Para cada campo, mapeie a coluna relevante na consulta que você criou em Log Analytics, para o campo de entidade apropriado. Selecione o nome da coluna relevante na **Propriedade**. Cada entidade inclui vários campos, por exemplo, SID, GUID, etc. Você pode mapear a entidade de acordo com qualquer um dos campos, não apenas a entidade de nível superior.

6. Defina condições de gatilho de alerta em **gatilho de alerta**. Isso define as condições que disparam o alerta. 

7. Defina a **frequência** para a frequência com que a consulta é executada-com frequência a cada 5 minutos ou com pouca frequência como uma vez por dia. 

8. Defina o **período** para controlar a janela de tempo para a quantidade de dados em que a consulta é executada, por exemplo, pode ser executada a cada hora em 60 minutos de dados.

9. Você também pode definir a **supressão**. A supressão é útil quando você deseja impedir que alertas duplicados sejam disparados para o mesmo incidente. Dessa forma, você pode impedir que os alertas sejam disparados durante um período específico. Isso pode ajudá-lo a evitar alertas duplicados para o mesmo incidente e permitir que você omita alertas consecutivos por um período de tempo. Por exemplo, se a  **frequência** de agendamento de alerta for definida como 60 minutos e o **período de agendamento de alerta** for definido como duas horas, e os resultados da consulta ultrapassarem o limite definido, ele disparará um alerta duas vezes, uma vez quando for detectado primeiro nos últimos 60 minutos, e novamente quando estiver nos primeiros 60 minutos de 2 horas de dados sendo amostrados. Recomendamos que, se um alerta for disparado, a supressão deve ser para o tempo definido no período de alerta. Em nosso exemplo, talvez você queira definir a supressão por 60 minutos, para que os alertas sejam disparados apenas para eventos que ocorreram durante a hora mais recente.

8. Depois de colar sua consulta no campo **definir regra de alerta** , você pode ver imediatamente uma simulação do alerta em **simulação de alerta de lógica** para que você possa entender a quantidade de dados que será gerada em um intervalo de tempo específico para o alerta Você criou. Isso dependerá do que você definiu para **frequência** e **limite**. Se você perceber que, em média, seu alerta será disparado com muita frequência, você desejará definir o número de resultados como mais alto para que esteja acima da sua linha de base média.

9. Clique em **criar** para inicializar sua regra de alerta. Depois que o alerta é criado, é criado um caso que contém o alerta. Você pode ver as regras de detecção definidas como linhas na guia **análise de segurança** . Você também pode ver o número de correspondências para cada regra-os alertas disparados. Nessa lista, você pode habilitar, desabilitar ou excluir cada regra. Você também pode selecionar com o botão direito do mouse nas reticências (...) no final da linha de cada alerta para editar, desabilitar, clonar, mostrar correspondências ou excluir uma regra. A página **análise** é uma galeria de todas as suas regras de alerta ativas, incluindo modelos que você habilita e regras de alerta que você cria com base em modelos.

1. Os resultados das regras de alerta podem ser vistos na página **casos** , em que você pode fazer a triagem, [investigar casos](tutorial-investigate-cases.md)e corrigir as ameaças.



## <a name="automate-threat-responses"></a>Automatizar respostas de ameaças

As equipes SIEM/SOC podem ser inundadas com alertas de segurança regularmente. O volume de alertas gerados é tão grande, que os administradores de segurança disponíveis estão sobrecarregados. Isso resulta muito frequentemente em situações em que muitos alertas não podem ser investigados, deixando a organização vulnerável a ataques que passam despercebidos. 

Muitos, se não a maioria, desses alertas estão em conformidade com padrões recorrentes que podem ser resolvidos por ações de correção específicas e definidas. O Azure Sentinel já permite que você defina sua correção nos guias estratégicos. Também é possível definir a automação em tempo real como parte de sua definição de guia estratégico para permitir que você automatize completamente uma resposta definida para alertas de segurança específicos. Usando a automação em tempo real, as equipes de resposta podem reduzir significativamente sua carga de trabalho automatizando completamente as respostas de rotina para tipos recorrentes de alertas, permitindo que você se concentre mais em alertas exclusivos, análise de padrões, busca de ameaças e muito mais.

Para automatizar as respostas:

1. Escolha o alerta para o qual você deseja automatizar a resposta.
1. No menu de navegação do espaço de trabalho do Azure Sentinel, selecione **análise**.
1. Selecione o alerta que você deseja automatizar. 
1. Na página **Editar regra de alerta** , em **automação em tempo real**, escolha o **guia estratégico** disparado que você deseja executar quando essa regra de alerta for correspondida.
1. Clique em **Salvar**.

   ![automação em tempo real](./media/tutorial-detect-threats/rt-configuration.png)


Além disso, você pode corrigir manualmente um alerta executando um guia estratégico de dentro do alerta, clicando em **Exibir guias estratégicos** e, em seguida, selecionando um guia estratégico para executar. Para saber como criar um novo guia estratégico ou editar um existente, consulte [trabalhando com guias estratégicos no Azure Sentinel](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel. 

Para saber como automatizar suas respostas a ameaças, [como responder a ameaças usando guias estratégicos automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responda às ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

