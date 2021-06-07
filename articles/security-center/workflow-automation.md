---
title: Automação de fluxo de trabalho na central de segurança do Azure | Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096166"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatizar respostas para gatilhos da central de segurança

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de stakeholders relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga. Além disso, ela pode aprimorar sua segurança, garantindo que as etapas do processo sejam feitas de maneira rápida, consistente e de acordo com seus requisitos predefinidos.

Este artigo descreve o recurso de automação de fluxo de trabalho da central de segurança do Azure. Esse recurso pode disparar aplicativos lógicos sobre alertas de segurança, recomendações e alterações na conformidade regulatória. Por exemplo, talvez você queira que a Central de Segurança envie por email um usuário específico quando uma avaliação de conformidade falhar. Você também aprenderá a criar aplicativos lógicos usando [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|**Função de administrador de segurança** ou **proprietário** no grupo de recursos<br>Também deve ter permissões de gravação para o recurso de destino<br><br>Para trabalhar com fluxos de trabalho de aplicativos lógicos do Azure, você também deve ter as seguintes funções/permissões de aplicativos lógicos:<br> - Permissões de [operador de aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-operator) são necessárias ou acesso de leitura/gatilho do aplicativo lógico (essa função não pode criar ou editar aplicativos lógicos; *executar* apenas os existentes)<br> - As permissões de [colaborador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor) são necessárias para a criação e modificação do aplicativo lógico<br>Se você quiser usar conectores de aplicativos lógicos, talvez precise de credenciais adicionais para entrar em seus respectivos serviços (por exemplo, suas instâncias de Outlook/equipes/margem de atraso)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Criar um aplicativo lógico e definir quando ele deve ser executado automaticamente 

1. Na barra lateral da central de segurança, selecione **automação de fluxo de trabalho**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista de automaçãos de fluxo de trabalho":::

    Nessa página, você pode criar novas regras de automação, bem como habilitar, desabilitar ou excluir aquelas existentes.

1. Para definir um novo fluxo de trabalho, clique em **Adicionar automação de fluxo de trabalho**. 

    Um painel é exibido com as opções para sua nova automação. Aqui você pode inserir:
    1. Um nome e uma descrição para a automação.
    1. Os gatilhos que iniciarão esse fluxo de trabalho automático. Por exemplo, talvez você queira que seu Aplicativo Lógico seja executado quando um alerta de segurança que contenha "SQL" for gerado.

        > [!NOTE]
        > Se o gatilho for uma recomendação que tenha "subrecomendações", por exemplo, as **descobertas de avaliação de vulnerabilidade em seus bancos de dados SQL devem ser corrigidas**, o aplicativo lógico não será disparado para cada nova descoberta de segurança; somente quando o status da recomendação pai for alterado.

    1. O Aplicativo Lógico que será executado quando suas condições de disparo forem atendidas. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Adicionar painel de automação de fluxo de trabalho":::

1. Na seção ações, clique em **criar um novo** para iniciar o processo de criação do aplicativo lógico.

    Você será levado para os Aplicativos Lógicos do Azure.

    [![Criando um novo aplicativo lógico](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Insira um nome, grupo de recursos e local e clique em **criar**.

1. Em seu novo aplicativo lógico, você pode escolher entre modelos predefinidos internos da categoria segurança. Ou você pode definir um fluxo de eventos personalizado para ocorrer quando esse processo é disparado.

    > [!TIP]
    > Às vezes, em um aplicativo lógico, os parâmetros são incluídos no conector como parte de uma cadeia de caracteres e não em seu próprio campo. Para obter um exemplo de como extrair parâmetros, consulte etapa #14 de [trabalhando com parâmetros de aplicativo lógico ao criar automaçãos de fluxo de trabalho da central de segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    O designer do aplicativo lógico dá suporte a esses gatilhos da central de segurança:

    - **Quando uma recomendação da central de segurança do Azure é criada ou disparada** – se o seu aplicativo lógico depender de uma recomendação que seja preterida ou substituída, a automação deixará de funcionar e você precisará atualizar o gatilho. Para controlar as alterações nas recomendações, consulte [notas de versão da central de segurança do Azure](release-notes.md).

    - **Quando um alerta da central de segurança do Azure é criado ou disparado** , você pode personalizar o gatilho para que ele se relacione apenas a alertas com os níveis de severidade que lhe interessam.
    
    - **Quando uma avaliação de conformidade regulatória da central de segurança é criada ou aciona** automaçãos disparadas com base em atualizações de avaliações de conformidade regulatória.

    > [!NOTE]
    > Se você estiver usando o gatilho herdado "quando uma resposta a um alerta da central de segurança do Azure for disparada", seus aplicativos lógicos não serão iniciados pelo recurso de automação do fluxo de trabalho. Em vez disso, use qualquer um dos gatilhos mencionados acima. 

    [![Aplicativo lógico de exemplo](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir seu aplicativo lógico, retorne ao painel de definição de automação de fluxo de trabalho ("Adicionar automação de fluxo de trabalho"). Clique em **Atualizar** para garantir que seu novo aplicativo lógico esteja disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione seu aplicativo lógico e salve a automação. Observe que o menu suspenso do aplicativo lógico mostra apenas os aplicativos lógicos com suporte aos conectores da central de segurança mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Disparar um aplicativo lógico manualmente

Você também pode executar Aplicativos Lógicos manualmente ao exibir qualquer alerta de segurança ou recomendação.

Para executar manualmente um aplicativo lógico, abra um alerta ou uma recomendação e clique em **disparar aplicativo lógico**:

[![Disparar um aplicativo lógico manualmente](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Configurar a automação do fluxo de trabalho em escala usando as políticas fornecidas

A automatização dos processos de monitoramento e resposta a incidentes da sua organização pode aprimorar significativamente o tempo necessário para investigar e atenuar incidentes de segurança.

Para implantar suas configurações de automação em sua organização, use as políticas do Azure Policy ' DeployIfNotExist ' fornecidas abaixo para criar e configurar procedimentos de automação de fluxo de trabalho.

Introdução aos [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Para implementar essas políticas:

1. Na tabela a seguir, selecione a política que você deseja aplicar:

    |Goal  |Política  |ID da Política  |
    |---------|---------|---------|
    |Automação de fluxo de trabalho para alertas de segurança|[Implantar a Automação de Fluxo de Trabalho para alertas da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Automação de fluxo de trabalho para recomendações de segurança|[Implantar a Automação de Fluxo de Trabalho para recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Automação do fluxo de trabalho para alterações de conformidade regulatória|[Implantar a Automação de Fluxo de Trabalho para conformidade regulatória da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Você também pode encontrá-los pesquisando Azure Policy:
    > 1. Abra Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Acessando Azure Policy":::
    > 2. No menu Azure Policy, selecione **definições** e pesquise-as por nome. 

1. Na página Azure Policy relevante, selecione **atribuir**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Atribuindo o Azure Policy":::

1. Abra cada guia e defina os parâmetros conforme desejado:
    1. Na guia **noções básicas** , defina o escopo da política. Para usar o gerenciamento centralizado, atribua a política ao grupo de gerenciamento que contém as assinaturas que usarão a configuração de automação do fluxo de trabalho. 
    1. Na guia **parâmetros** , defina o grupo de recursos e os detalhes do tipo de dados. 
        > [!TIP]
        > Cada parâmetro tem uma dica de ferramenta explicando as opções disponíveis para você.
        >
        > A guia de parâmetros de Azure Policy (1) fornece acesso a opções de configuração semelhantes da página de automação de fluxo de trabalho da central de segurança (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Comparando os parâmetros na automação do fluxo de trabalho com Azure Policy" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Opcionalmente, para aplicar essa atribuição a assinaturas existentes, abra a guia **correção** e selecione a opção para criar uma tarefa de correção.

1. Examine a página Resumo e selecione **criar**.


## <a name="data-types-schemas"></a>Esquemas de tipos de dados

Para exibir os esquemas de eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância do aplicativo lógico, visite os [esquemas de tipos de dados de automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas). Isso pode ser útil em casos em que você não está usando conectores de aplicativos lógicos internos da central de segurança mencionados acima, mas, em vez disso, está usando o conector HTTP genérico do aplicativo lógico – você pode usar o esquema JSON de evento para analisá-lo manualmente como desejar.


## <a name="faq-for-workflow-automation"></a>Perguntas frequentes sobre automação de fluxo de trabalho

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>A automação de fluxo de trabalho dá suporte a qualquer cenário de continuidade de negócios ou BCDR (recuperação de desastre)?

Ao preparar seu ambiente para cenários BCDR, em que o recurso de destino está passando por uma interrupção ou outro desastre, é responsabilidade da organização evitar a perda de dados estabelecendo backups de acordo com as diretrizes dos hubs de eventos do Azure, do espaço de trabalho Log Analytics e do aplicativo lógico.

Para cada automação ativa, recomendamos que você crie uma automação idêntica (desabilitada) e armazene-a em um local diferente. Quando houver uma interrupção, você poderá habilitar essas automaçãos de backup e manter as operações normais.

Saiba mais sobre [continuidade de negócios e recuperação de desastre para aplicativos lógicos do Azure](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar aplicativos lógicos, automatizar sua execução na central de segurança e executá-los manualmente.

Para obter materiais relacionados, confira: 

- [O módulo Microsoft Learn sobre como usar a automação de fluxo de trabalho para automatizar uma resposta de segurança](/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
- [Sobre os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md)
- [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)
- [Esquemas dos tipos de dados de automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas)