---
title: Executar um guia estratégico na versão prévia do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como executar um guia estratégico no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/25/2019
ms.author: rkarlin
ms.openlocfilehash: cdfe22b67585221e2d7e17f47c6a09ba929d68ef
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599021"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Tutorial: Configurar respostas de ameaças automatizado na visualização do Azure Sentinel

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda você a usar os Guias estratégicos de segurança no Azure Sentinel para definir as respostas de ameaças automatizadas para problemas relacionados à segurança detectados pelo Azure Sentinel.


> [!div class="checklist"]
> * Entender os Guias estratégicos
> * Criar um Guia estratégico
> * Executar um Guia estratégico
> * Automatizar as respostas a ameaças


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>O que é um Guia estratégico de segurança no Azure Sentinel?

Um Guia estratégico de segurança é uma coleção de procedimentos que podem ser executados em resposta a um alerta. Um guia estratégico de segurança pode ajudar a automatizar e coordenar a resposta e pode ser executado manualmente ou definido para ser executado automaticamente quando os alertas específicos forem disparados. Guias estratégicos de segurança no Azure Sentinel se baseiam em [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), o que significa que você obtém toda a potência, capacidade de personalização e modelos internos de aplicativos lógicos. Cada Guia estratégico é criado para a assinatura específica que você escolher, mas quando você examinar a página Guias estratégicos, você verá todos os Guias estratégicos em nenhuma assinatura selecionada.

> [!NOTE]
> O Guia estratégico aproveita os Aplicativos Lógicos do Azure e, portanto, geram encargos. Visite a página [Aplicativos Lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes de preços.

Por exemplo, se você estiver preocupado com invasores mal-intencionados acessando os recursos de rede, você pode definir um alerta que procura endereços IP mal-intencionados acessando sua rede. Em seguida, você pode criar um guia estratégico que faz o seguinte:
1. Quando o alerta for disparado, abra um tíquete no ServiceNow ou qualquer outro sistema de tíquete de TI.
2. Envie uma mensagem para o canal de operações de segurança no Microsoft Teams ou Slack para certificar-se de que os analistas de segurança estão cientes do incidente.
3. Envie todas as informações no alerta para o administrador de rede sênior e administrador de segurança. A mensagem de email também inclui dois botões de opção do usuário **Bloquear** ou **Ignorar**.
4. O Guia estratégico continuará a ser executado depois que uma resposta é recebida dos administradores.
5. Se os administradores escolher **Bloquear**, o endereço IP é bloqueado no firewall e o usuário está desabilitado no Azure Active Directory.
6. Se os administradores escolherem **Ignorar**, o alerta será fechado no Azure Sentinel e o incidente será fechado no ServiceNow.

Os Guias estratégicos de segurança podem ser executados manual ou automaticamente. Executá-los manualmente significa que, quando você receber um alerta, você pode optar por executar uma guia estratégico sob demanda como uma resposta para o alerta selecionado. Executá-los automaticamente significa que ao criar a regra de correlação, você a definiu para executar um ou mais Guias estratégicos quando o alerta for disparado.


## <a name="create-a-security-playbook"></a>Criar Guias Estratégicos de Segurança

Siga estas etapas para criar um novo guia estratégico de segurança no Azure Sentinel:

1. Abra o painel **Azure Sentinel**.
2. Em **Gerenciamento**, selecione **Guias estratégicos**.

   ![Aplicativo Lógico](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na página **Azure Sentinel - Guias Estratégicos (versão prévia)** , clique no botão **Adicionar**.

   ![Criar aplicativo lógico](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na página **Criar aplicativo lógico**, digite as informações solicitadas para criar seu novo aplicativo lógico e clique no botão **Criar**. 

5. Em [ **Designer de aplicativo lógico**](../logic-apps/logic-apps-overview.md), selecione o modelo que você deseja usar. Se você selecionar um modelo que exige credenciais, você precisará fornecê-las. Como alternativa, você pode criar um novo Guia estratégico em branco do zero. Selecione **Aplicativo lógico em branco**. 

   ![Designer de aplicativo lógico](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Você será direcionado para o Designer do aplicativo lógico no qual você pode criar novo ou editar o modelo. Para obter mais informações sobre como criar um Guia estratégico com [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Se você estiver criando um Guia estratégico em branco, no campo **Pesquisar todos os conectores e gatilhos**, digite *Azure Sentinel* e selecione **Quando uma resposta a um alerta do Azure Sentinel é acionada**. <br>Depois que é criado, o novo Guia estratégico aparecerá na lista de **Guias estratégicos**. Se isso não aparecer, clique no botão **Atualizar**. 

7. Agora você pode definir o que acontece ao adicionar o manual. Você pode adicionar uma ação, uma condição lógica, condições de caso de alternância ou loops.

   ![Designer de aplicativo lógico](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Como executar um Guia estratégico de segurança

Você pode executar um Guia estratégico sob demanda.

Executar um Guia estratégico sob demanda:

1. Na página **Casos**, selecione um caso e clique em **Exibir detalhes completos**.

2. Na guia **Alertas**, clique no alerta que você deseja executar o Guia estratégico e role até a direita e clique em **Exibir Guias estratégicos** e selecione um Guia estratégico para **Executar** das lista de Guias estratégicos disponíveis na assinatura. 



## <a name="automate-threat-responses"></a>Automatizar as respostas a ameaças

As equipes SIEM/SOC podem ser inundadas com alertas de segurança regularmente. O volume de alertas gerados é tão grande que os administradores de segurança disponíveis estão sobrecarregados. Muito frequentemente, isso gera situações em que muitos alertas não podem ser investigados, deixando a organização vulnerável a ataques que passam despercebidos. 

Muitos desses alertas, se não a maioria, estão em conformidade com os padrões recorrentes que podem ser resolvidos por ações de correção específicas e definidas. O Azure Sentinel já permite que você defina sua correção nos guias estratégicos. Também é possível definir a automação em tempo real como parte da definição de guia estratégico para permitir a automatização completa de uma resposta definida a alertas de segurança específicos. Com a automação em tempo real, as equipes de resposta podem reduzir significativamente a carga de trabalho automatizando completamente as respostas de rotina a tipos recorrentes de alertas, permitindo que você se concentre mais em alertas exclusivos, análise de padrões, busca de ameaças e muito mais.

Para automatizar as respostas:

1. Escolha o alerta para o qual você deseja automatizar a resposta.
1. No menu de navegação do workspace do Azure Sentinel, selecione **Análise**.
1. Selecione o alerta que você deseja automatizar. 
1. Na página **Editar regra de alerta**, em **Automação em tempo real**, escolha o **Guia estratégico disparado** que deve ser executado quando a regra de alerta for correspondente.
1. Clique em **Salvar**.

   ![automação em tempo real](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a executar um manual no Azure Sentinel. Continue para o [Como buscar ameaças proativamente](hunting.md) usando o Azure Sentinel.


