---
title: 'Tutorial: usar guias estratégicos com regras de automação no Azure Sentinel'
description: Use este tutorial como auxílio para usar os guias estratégicos junto com as regras de automação no Azure Sentinel para automatizar a resposta a incidentes e corrigir ameaças à segurança.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600558"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Tutorial: usar guias estratégicos com regras de automação no Azure Sentinel

Este tutorial mostra como usar os guias estratégicos junto com as regras de automação para automatizar sua resposta a incidentes e corrigir ameaças de segurança detectadas pelo Azure Sentinel. Depois de concluir este tutorial, você poderá:

> [!div class="checklist"]
>
> * Criar uma regra de automação
> * Criar um Guia estratégico
> * Adicionar ações a um guia estratégico
> * Anexar um guia estratégico a uma regra de automação ou a uma regra de análise para automatizar a resposta a ameaças

## <a name="what-are-automation-rules-and-playbooks"></a>O que são regras de automação e guias estratégicos?

As regras de automação ajudam você a fazer a triagem de incidentes no Azure Sentinel. Você pode usá-las para atribuir incidentes automaticamente aos funcionários certos, fechar incidentes com ruído ou falsos positivos conhecidos, alterar a severidade deles e adicionar marcas. Eles também são o mecanismo pelo qual você pode executar guias estratégicos em resposta a incidentes.

Guias estratégicos são coleções de procedimentos que podem ser executados do Azure Sentinel em resposta a um alerta ou incidente. Um guia estratégico pode ajudar a automatizar e orquestrar sua resposta e pode ser definido para ser executado automaticamente quando alertas ou incidentes específicos forem gerados, sendo anexados a uma regra de análise ou uma regra de automação, respectivamente. Ele também pode ser executado manualmente sob demanda.

Guias estratégicos no Azure Sentinel se baseiam em fluxos de trabalho criados nos [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), o que significa que você obtém toda a potência, capacidade de personalização e modelos internos de Aplicativos Lógicos. Cada guia estratégico é criado para a assinatura específica à qual ele pertence, mas a exibição **Guias estratégicos** mostra todos os guias estratégicos disponíveis em todas as assinaturas selecionadas.

> [!NOTE]
> Como os guias estratégicos fazem uso de Aplicativos Lógicos do Azure, encargos adicionais podem ser aplicados. Visite a página [Aplicativos Lógicos do Azure](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes de preços.

Por exemplo, se você quiser impedir que usuários potencialmente comprometidos se movam pela rede e roubem informações, poderá criar uma resposta multifacetada automatizada para incidentes gerados por regras que detectem usuários comprometidos. Comece criando um guia estratégico que executa as seguintes ações:

1. Quando o guia estratégico é chamado por uma regra de automação passando um incidente, o guia estratégico abre um tíquete no [ServiceNow](/connectors/service-now/) ou em qualquer outro sistema de emissão de tíquetes de TI.

1. Ele envia uma mensagem para o canal de operações de segurança no [Microsoft Teams](/connectors/teams/) ou no [Slack](/connectors/slack/) para assegurar que os analistas de segurança estão cientes do incidente.

1. Ele também envia as informações sobre o incidente em uma mensagem de email para que o administrador de rede sênior e administrador de segurança. A mensagem de email também incluirá dois botões de opção de usuário, **Bloquear** e **Ignorar**.

1. O guia estratégico aguarda até que uma resposta seja recebida dos administradores e, depois, continua com as próximas etapas.

1. Se os administradores escolherem **Bloquear**, ele enviará um comando para o Azure AD a fim de desabilitar o usuário e um para o firewall a fim de bloquear o endereço IP.

1. Se os administradores escolherem **Ignorar**, o guia estratégico fechará o incidente no Azure Sentinel e o tíquete no ServiceNow.

Para disparar o guia estratégico, você criará uma regra de automação que é executada quando esses incidentes são gerados. Essa regra executará estas etapas:

1. A regra altera o status do incidente para **Ativo**.

1. Ele atribui o incidente para o analista que tem a tarefa de gerenciar esse tipo de incidente.

1. Ele adiciona a marca "usuário comprometido".

1. Por fim, ele chama o guia estratégico que você acabou de criar. ([Permissões especiais são necessárias para esta etapa](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Os guias estratégicos podem ser executados automaticamente em resposta a incidentes, criando regras de automação que chamam os guias estratégicos como ações, conforme no exemplo acima. Eles também podem ser executados automaticamente em resposta a alertas, informando a regra de análise para executar automaticamente um ou mais guias estratégicos quando o alerta é gerado. 

Você também pode optar por executar um guia estratégico manualmente sob demanda, como uma resposta a um alerta selecionado.

Obtenha uma introdução mais completa e detalhada para automatizar a resposta contra ameaças usando [regras de automação](automate-incident-handling-with-automation-rules.md) e [guias estratégicos](automate-responses-with-playbooks.md) no Azure Sentinel.

> [!IMPORTANT]
>
> - As **regras de automação** e o uso do **gatilho de incidente** para guias estratégicos estão atualmente em **VERSÃO PRÉVIA**. Veja os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

## <a name="create-a-playbook"></a>Criar um Guia estratégico

Siga estas etapas para criar um guia estratégico no Azure Sentinel:

### <a name="prepare-the-playbook-and-logic-app"></a>Preparar o guia estratégico e o Aplicativo Lógico

1. No menu de navegação do workspace do **Azure Sentinel**, selecione **Automação**.

1. No menu superior, selecione **Criar** e **Adicionar novo guia estratégico**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Adicionar um novo guia estratégico":::

    Uma nova guia do navegador será aberta e levará você para o assistente para **Criar um aplicativo lógico**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Criar um aplicativo lógico":::

1. Insira a sua **Assinatura** e o seu **Grupo de recursos** e dê um nome ao guia estratégico em **Nome do aplicativo lógico**.

1. Para **Região**, selecione a região do Azure onde as informações do Aplicativo Lógico serão armazenadas.

1. Se você quiser monitorar a atividade deste manual para fins de diagnóstico, marque a caixa de seleção **Habilitar análise de logs** e insira o nome do **Workspace do Log Analytics**.

1. Se você quiser aplicar marcas ao seu guia estratégico, clique em **Avançar: Marcas >** (não conectado a marcas aplicadas por regras de automação. [Saiba mais sobre as marcas](../azure-resource-manager/management/tag-resources.md)). Caso contrário, clique em **Examinar + criar**. Confirme os detalhes fornecidos e clique em **Criar**.

1. Enquanto o manual está sendo criado e implantado (isso levará alguns minutos), você será levado para uma tela chamada **Microsoft.EmptyWorkflow**. Quando a mensagem "a implantação for concluída" for exibida, clique em **Ir para o recurso.**

1. Você será levado para o [Designer de Aplicativos Lógicos](../logic-apps/logic-apps-overview.md) do seu novo guia estratégico, no qual poderá começar a criar o fluxo de trabalho. Você verá uma tela com um breve vídeo introdutório e alguns modelos e gatilhos de Aplicativo Lógico usados com frequência. [Saiba mais](../logic-apps/logic-apps-create-logic-apps-from-templates.md) sobre como criar um guia estratégico com Aplicativos Lógicos.

1. Escolha o modelo **Aplicativo Lógico em branco**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galeria de modelos do Designer de Aplicativos Lógicos":::

### <a name="choose-the-trigger"></a>Escolha o gatilho

Cada guia estratégico precisa começar com um gatilho. O gatilho define a ação que iniciará o guia estratégico e o esquema que o guia estratégico esperará receber.

1. Na barra de pesquisa, procure Azure Sentinel. Selecione **Azure Sentinel** quando ele for exibido nos resultados da pesquisa.

1. Na guia **Gatilhos** resultante, você verá os dois gatilhos oferecidos pelo Azure Sentinel:
    - Quando uma resposta a um alerta do Azure Sentinel é disparada
    - Quando a regra de criação de incidentes do Azure Sentinel foi acionada

   Escolha o gatilho que corresponde ao tipo de guia estratégico que você está criando.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Escolher um gatilho para o seu guia estratégico":::

### <a name="add-actions"></a>Adicionar ações

Agora você pode definir o que acontece ao chamar o guia estratégico. Você pode adicionar ações, condições lógicas, loops ou alternar condições de caso, tudo selecionando **Nova etapa**. Essa seleção abre um novo quadro no designer, no qual você pode escolher um sistema ou um aplicativo com o qual interagir ou uma condição para definir. Insira o nome do sistema ou aplicativo na barra de pesquisa na parte superior do quadro e escolha um dos resultados disponíveis.

Em cada uma dessas etapas, clicar em qualquer campo exibe um painel com dois menus: **Conteúdo dinâmico** e **Expressão**. No menu de **Conteúdo dinâmico**, você pode adicionar referências aos atributos do alerta ou incidente que foi passado para o guia estratégico, incluindo os valores e atributos de todas as entidades envolvidas. No menu **Expressão**, você pode escolher entre uma grande biblioteca de funções para adicionar lógica adicional às suas etapas.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Designer de aplicativo lógico":::

Esta captura de tela mostra as ações e condições que você adicionaria ao criar o guia estratégico descrito no exemplo no início deste documento. A única diferença é que, no guia estratégico mostrado aqui, você está usando o **gatilho de alerta** em vez do **gatilho de incidente**. Isso significa que você chamará este manual de uma regra de análise diretamente, não de uma regra de automação. As duas maneiras de chamar um guia estratégico serão descritas abaixo.

## <a name="automate-threat-responses"></a>Automatizar as respostas a ameaças

Você criou o guia estratégico e definiu o gatilho, definiu as condições e prescreveu as ações que ele executará e as saídas que ele produzirá. Agora, você precisa determinar os critérios sob os quais ele será executado e configurará o mecanismo de automação que o executará quando esses critérios forem atendidos.

### <a name="respond-to-incidents"></a>Responder a incidentes

Você usa um guia estratégico para responder a um **incidente** criando uma [regra de automação](automate-incident-handling-with-automation-rules.md) que será executada quando o incidente for gerado e, por sua vez, chamará o guia estratégico.

Para criar uma regra de automação:

1. Na folha **Automação** no menu de navegação do Azure Sentinel, selecione **Criar** no menu superior e, depois, **Adicionar nova regra**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Adicionar uma nova regra":::

1. O painel **Criar regra de automação** é aberto. Digite um nome para a sua regra.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Criar uma regra de automação":::

1. Se você quiser que a regra de automação entre em vigor somente em determinadas regras de análise, especifique quais delas modificando a condição **Se Nome de regra de análise**.

1. Adicione outras condições nas quais você deseja que a ativação da regra de automação dependa. Clique em **Adicionar condição** e escolha condições na lista suspensa. A lista de condições é preenchida por detalhes de alerta e campos de identificador de entidade.

1. Escolha as ações que você deseja que essa regra de automação execute. As ações disponíveis incluem **Atribuir proprietário**, **Alterar status**, **Alterar severidade**, **Adicionar marcas** e **Executar guia estratégico**. Você pode adicionar quantas ações desejar.

1. Se você adicionar uma ação **Executar guia estratégico**, precisará escolher uma opção na lista suspensa de guias estratégicos disponíveis. Somente guias estratégicos que começam com o **gatilho de incidente** podem ser executados por meio de regras de automação, portanto, somente eles aparecerão na lista.

    > [!IMPORTANT]
    > O Azure Sentinel precisa receber permissões explícitas para executar guias estratégicos por meio de regras de automação. Se um guia estratégico aparecer "esmaecido" na lista suspensa, significará que o Sentinel não tem permissão para o grupo de recursos desse guia estratégico. Clique no link **Gerenciar permissões do guia estratégico** para atribuir permissões.
    > No painel **Gerenciar permissões** que é aberto, marque as caixas de seleção dos grupos de recursos que contêm os guias estratégicos que você deseja executar e clique em **Aplicar**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Gerenciar permissões":::
    > - Você precisa ter permissões de **proprietário** em qualquer grupo de recursos ao qual deseja conceder permissões do Azure Sentinel e precisa ter a função de **Colaborador do Aplicativo Lógico** em qualquer grupo de recursos que contenha guias estratégicos que você deseja executar.
    > - Em uma implantação multilocatário, se o guia estratégico que desejar executar estiver em um locatário diferente, você deverá conceder permissão ao Azure Sentinel para executar o guia estratégico no respectivo locatário.
    >    1. No menu de navegação do Azure Sentinel, no locatário dos guias estratégicos, selecione **Configurações**.
    >    1. Na folha **Configurações**, selecione a guia **Configurações** e, depois, o expansor **Permissões do guia estratégico**.
    >    1. Clique no botão **Configurar permissões** para abrir o painel **Gerenciar permissões** mencionado acima e continue conforme descrito lá.

1. Defina uma data de vencimento para a sua regra de automação se desejar que ela tenha uma.

1. Insira um número em **Ordem** para determinar onde na sequência de regras de automação essa regra será executada.

1. Clique em **Aplicar**. Pronto!

[Descubra outras maneiras](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) de criar regras de automação.

### <a name="respond-to-alerts"></a>Responder a alertas

Você usa um guia estratégico para responder a um **alerta** criando uma **regra de análise** ou editando uma existente, que é executada quando o alerta é gerado, e selecionando seu guia estratégico como uma resposta automática no [assistente de regra de análise](tutorial-detect-threats-custom.md).

1. Na folha **Análise**, no menu de navegação do Azure Sentinel, selecione a regra de análise para a qual você deseja automatizar a resposta e clique em **Editar** no painel de detalhes.

1. Na página **Assistente de regra de análise – Editar regra existente**, selecione a guia **Resposta automática**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Guia de resposta automatizada":::

1. Escolha o guia estratégico na lista suspensa. Você pode escolher mais de um guia estratégico, mas somente os guias estratégicos que usam o **gatilho de alerta** estarão disponíveis.

1. Na guia **Examinar e criar**, selecione **Salvar**.

## <a name="run-a-playbook-on-demand"></a>Executar um guia estratégico sob demanda

Você também pode executar um guia estratégico sob demanda.

 > [!NOTE]
 > Somente os guias estratégicos que usam o **gatilho de alerta** podem ser executados sob demanda.

Executar um Guia estratégico sob demanda:

1. Na página **Incidentes**, selecione um incidente e clique em **Exibir detalhes completos**.

2. Na guia **Alertas**, clique no alerta que você deseja executar o Guia estratégico e role até a direita e clique em **Exibir Guias estratégicos** e selecione um Guia estratégico para **Executar** das lista de Guias estratégicos disponíveis na assinatura.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os guias estratégicos e as regras de automação no Azure Sentinel para responder às ameaças. 
- Saiba como [buscar ameaças proativamente](hunting.md) usando o Azure Sentinel.
