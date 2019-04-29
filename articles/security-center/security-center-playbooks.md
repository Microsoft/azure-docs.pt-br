---
title: Guia estratégico de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar guias estratégicos de segurança na Central de Segurança do Azure para automatizar a resposta a incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ec16e6daec099adbede625c5ec6fe6909059143b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906887"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Guia estratégico de segurança na Central de Segurança do Azure (versão prévia)
Este documento ensina a usar guias estratégicos de segurança na Central de Segurança do Azure para responder a problemas de segurança.

## <a name="what-is-security-playbook-in-security-center"></a>O que é o guia estratégico de segurança na Central de Segurança?
Guia estratégico de segurança é uma coleção de procedimentos que podem ser executados na Central de Segurança depois que determinado guia estratégico é disparado pelo alerta selecionado. O guia estratégico de segurança pode ajudar a automatizar e coordenar a resposta a um alerta de segurança específico detectado pela Central de Segurança. Os guias estratégicos de segurança na Central de Segurança se baseiam nos [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), o que significa que você pode usar os modelos que são fornecidos na categoria Segurança nos modelos de Aplicativos Lógicos; pode modificá-los com base em suas necessidades ou pode criar novos guias estratégicos usando o [fluxo de trabalho dos Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app)e a Central de Segurança como o gatilho.

> [!NOTE]
> O guia estratégico aproveita os Aplicativos Lógicos do Azure e, portanto, geram encargos. Visite a página [Aplicativos Lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes de preços.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Como criar um guia estratégico de segurança na Central de Segurança?
Siga estas etapas para criar um novo guia estratégico de segurança na Central de Segurança:

1.  Abra o painel **Central de Segurança**.
2.  Na seção **Automação e Orquestração** no painel esquerdo, clique em **Guias Estratégicos (Versão prévia)**.

    ![Aplicativo Lógico](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. Em **Central de Segurança - Guias estratégicos (Versão prévia)**, clique no botão **Adicionar**.

    ![Criar aplicativo lógico](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Na página **Criar aplicativo lógico**, digite as informações solicitadas para criar seu novo aplicativo lógico e clique no botão **Criar**. Depois de concluir a criação, o novo guia estratégico aparecerá na lista. Se ele não aparecer, clique no botão **Atualizar**. Ao encontrá-lo, clique no guia estratégico para começar a editá-lo.

    ![Criar aplicativo lógico](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. O **Designer de Aplicativo Lógico** é exibido. Clique em **Aplicativo Lógico em Branco** para criar um novo guia estratégico. Você também pode selecionar **Segurança** nas categorias e usar um dos modelos.

    ![Designer de aplicativo lógico](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. No campo **Pesquisar todos os gatilhos e conectores**, digite *Central de Segurança do Azure*e selecione **Quando uma resposta a um alerta da Central de Segurança do Azure é disparada**.

    ![Gatilho](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Agora você pode definir o que acontece ao adicionar o manual. Você pode adicionar uma ação, uma condição lógica, condições de caso de alternância ou loops.

    ![Designer de aplicativo lógico](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Como executar um guia estratégico de segurança na Central de Segurança?

Você pode executar um guia estratégico de segurança na Central de Segurança quando quiser orquestrar, saber mais sobre outros serviços ou fazer correções. Para acessar os guias, siga estas etapas:

1.  Abra o painel **Central de Segurança**.
2.  No painel esquerdo, em **Detecção de Ameaças**, clique em **Incidentes de segurança e alertas**.

    ![Alertas](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Clique no alerta que você deseja investigar.
4.  Na parte superior da página do alerta, clique no botão **Executar guias estratégicos**.

    ![Executar guia estratégico](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Na página Guias estratégicos, selecione o guia estratégico que você deseja executar e clique no botão **Executar**. Se quiser ver o guia estratégico antes de dispará-lo, você pode clicar nele para abrir o designer.

    ![Guias estratégicos](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Histórico

Depois de executar o manual, você também pode acessar execuções anteriores e etapas com mais informações sobre o status de guias estratégicos executados anteriormente. O histórico é contextualizado por alerta, o que significa que o histórico do guia estratégico que você vê nessa página está relacionado ao alerta que disparou o guia estratégico.

![Histórico](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Para ver mais detalhes sobre a execução de um guia estratégico específico, clique no próprio guia estratégico e a página de execução do Aplicativo Lógico é exibida com o fluxo de trabalho inteiro.

![Detalhes](./media/security-center-playbooks/security-center-playbooks-fig14.png)

Nesse fluxo de trabalho, você pode ver o tempo que cada tarefa levou para ser executada e pode expandir cada tarefa para ver o resultado.

### <a name="changing-an-existing-playbook"></a>Alterando um guia estratégico existente

Você pode alterar um guia estratégico existente na Central de Segurança para adicionar uma ação ou algumas condições. Para fazer isso, você precisa apenas clicar no nome do guia estratégico que deseja alterar, na guia Guias Estratégicos, para abrir o Designer de Aplicativos Lógicos.

> [!NOTE]
> Para saber mais sobre como criar seu próprio guia estratégico usando o Aplicativo Lógico do Azure, leia [Criar seu primeiro fluxo de trabalho de aplicativo lógico para automatizar processos entre aplicativos de nuvem e serviços de nuvem](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar guias estratégicos na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
