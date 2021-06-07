---
title: Automatizar o tratamento de incidentes no Azure Sentinel | Microsoft Docs
description: Este artigo explica como usar as regras de automação para automatizar o tratamento de incidentes, a fim de maximizar a eficiência e a eficácia do SOC em resposta a ameaças de segurança.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609025"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatizar o tratamento de incidentes no Azure Sentinel com regras de automação

Este artigo explica o que são regras de automação do Azure Sentinel e como usá-las para implementar suas operações de orquestração de segurança, automação e resposta (disparar), aumentando a eficácia do SOC e economizando tempo e recursos.

> [!IMPORTANT]
>
> - O recurso de **regras de automação** está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="what-are-automation-rules"></a>O que são regras de automação?

As regras de automação são um novo conceito no Azure Sentinel. Esse recurso permite que os usuários gerenciem centralmente a automação da manipulação de incidentes. Além de permitir que você atribua guias estratégicos a incidentes (não apenas para alertas como antes), as regras de automação também permitem automatizar as respostas para várias regras de análise de uma vez, marcar automaticamente, atribuir ou fechar incidentes sem a necessidade de guias estratégicos e controlar a ordem das ações executadas. As regras de automação simplificarão o uso da automação no Azure Sentinel e permitirão que você simplifique fluxos de trabalho complexos para seus processos de orquestração de incidentes.

## <a name="components"></a>Componentes

As regras de automação são constituídas por vários componentes:

### <a name="trigger"></a>Gatilho

As regras de automação são disparadas pela criação de um incidente. 

Para revisar – os incidentes são criados a partir de alertas por regras de análise, dos quais há quatro tipos, conforme explicado no tutorial [detectar ameaças com regras de análise internas no Azure Sentinel](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Condições

Conjuntos de condições complexos podem ser definidos para controlar quando as ações (veja abaixo) devem ser executadas. Essas condições normalmente são baseadas nos Estados ou valores de atributos de incidentes e suas entidades, e podem incluir `AND` / `OR` / `NOT` / `CONTAINS` operadores.

### <a name="actions"></a>Ações

As ações podem ser definidas para execução quando as condições (veja acima) são atendidas. Você pode definir muitas ações em uma regra, e pode escolher a ordem na qual elas serão executadas (veja abaixo). As ações a seguir podem ser definidas usando regras de automação, sem a necessidade da [funcionalidade avançada de um guia estratégico](automate-responses-with-playbooks.md):

- Alterar o status de um incidente, mantendo o fluxo de trabalho atualizado.

  - Ao alterar para "closed", especificar o [motivo de fechamento](tutorial-investigate-cases.md#closing-an-incident) e adicionar um comentário. Isso ajuda você a acompanhar o desempenho e a eficácia e a ajustar os falsos positivos.

- Alterar a severidade de um incidente – você pode reavaliar e repriorizar com base na presença, ausência, valores ou atributos das entidades envolvidas no incidente.

- Atribuir um incidente a um proprietário – isso ajuda você a direcionar tipos de incidentes para a equipe mais adequada para lidar com eles ou para a equipe mais disponível.

- Adicionar uma marca a um incidente – isso é útil para classificar incidentes por assunto, por invasor ou por qualquer outro denominador comum.

Além disso, você pode definir uma ação para executar um [guia estratégico](tutorial-respond-threats-playbook.md), a fim de realizar ações de resposta mais complexas, incluindo qualquer um que envolva sistemas externos. Somente os guias estratégicos ativados pelo [gatilho de incidente](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) estão disponíveis para serem usados em regras de automação. Você pode definir uma ação para incluir vários guias estratégicos ou combinações de guias estratégicos e outras ações e a ordem na qual eles serão executados.

### <a name="expiration-date"></a>Data de validade

Você pode definir uma data de expiração em uma regra de automação. A regra será desabilitada após essa data. Isso é útil para lidar com incidentes de "ruído" (ou seja, fechamento) causados por atividades planejadas e limitadas por tempo, como testes de penetração.

### <a name="order"></a>Ordem

Você pode definir a ordem na qual as regras de automação serão executadas. As regras de automação posteriores avaliarão as condições do incidente de acordo com seu estado depois de serem acionadas pelas regras de automação anteriores.

Por exemplo, se "primeira regra de automação" tiver alterado a severidade de um incidente de médio para baixo e "segunda regra de automação" estiver definida para ser executada somente em incidentes com severidade média ou mais alta, ele não será executado nesse incidente.

## <a name="common-use-cases-and-scenarios"></a>Cenários e casos de uso comuns

### <a name="incident-triggered-automation"></a>Automação disparada por incidentes

Até agora, somente alertas podem disparar uma resposta automatizada, por meio do uso de guias estratégicos. Com as regras de automação, os incidentes agora podem disparar cadeias de respostas automatizadas, que podem incluir novos guias estratégicos disparados por incidentes ([permissões especiais são necessárias](#permissions-for-automation-rules-to-run-playbooks)) quando um incidente é criado. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Guias estratégicos de gatilho para provedores da Microsoft

As regras de automação fornecem uma maneira de automatizar a manipulação de alertas de segurança da Microsoft aplicando essas regras a incidentes criados com base nos alertas. As regras de automação podem chamar guias estratégicos ([são necessárias permissões especiais](#permissions-for-automation-rules-to-run-playbooks)) e passar os incidentes para eles com todos os seus detalhes, incluindo alertas e entidades. Em geral, as práticas recomendadas do Azure Sentinel ditam o uso da fila de incidentes como o ponto focal das operações de segurança.

Os alertas de segurança da Microsoft incluem o seguinte:

- MCAS (Microsoft Cloud App Security)
- Azure AD Identity Protection
- Azure defender (ASC)
- Azure Defender para IoT (anteriormente ASC para IoT)
- Microsoft Defender para Office 365 (anteriormente ATP do Office 365)
- Microsoft Defender para ponto de extremidade (anteriormente MDATP)
- Microsoft Defender para identidade (anteriormente ATP do Azure)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Vários guias estratégicos/ações sequenciadas em uma única regra

Agora você pode ter um controle quase completo sobre a ordem de execução de ações e guias estratégicos em uma única regra de automação. Você também controla a ordem de execução das regras de automação. Isso permite simplificar bastante os guias estratégicos, reduzindo-os a uma única tarefa ou a uma sequência pequena e simples de tarefas e combinar esses guias estratégicos pequenos em diferentes combinações em diferentes regras de automação.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Atribuir um guia estratégico a várias regras de análise de uma só vez

Se você tiver uma tarefa que deseja automatizar em todas as regras de análise – digamos, a criação de um tíquete de suporte em um sistema de emissão de tíquetes externo – você pode aplicar um único guia estratégico a qualquer uma ou todas as suas regras de análise, incluindo quaisquer regras futuras, em uma única imagem. Isso torna a manutenção simples, mas repetitiva e as tarefas de manutenção, muito menos de uma tarefa.

### <a name="automatic-assignment-of-incidents"></a>Atribuição automática de incidentes

Você pode atribuir incidentes ao proprietário correto automaticamente. Se o SOC tiver um analista especializado em uma determinada plataforma, todos os incidentes relacionados a essa plataforma poderão ser atribuídos automaticamente a esse analista.

### <a name="incident-suppression"></a>Supressão de incidente

Você pode usar regras para resolver automaticamente incidentes que são positivos falsos/benignos conhecidos sem o uso de guias estratégicos. Por exemplo, ao executar testes de penetração, fazer atualizações ou manutenção agendada ou testar procedimentos de automação, muitos incidentes falsos positivos podem ser criados e o SOC deseja ignorar. Uma regra de automação de tempo limitado pode fechar automaticamente esses incidentes à medida que eles são criados, enquanto os marca com um descritor da causa de sua geração.

### <a name="time-limited-automation"></a>Automação de tempo limitado

Você pode adicionar datas de expiração para suas regras de automação. Pode haver casos diferentes da supressão de incidentes que garantem a automação com tempo limitado. Talvez você queira atribuir um determinado tipo de incidente a um usuário específico (digamos, um estagiário ou um consultor) para um período de tempo específico. Se o período de tempo for conhecido com antecedência, você poderá efetivamente fazer com que a regra seja desabilitada no final de sua relevância, sem precisar se lembrar de fazê-lo.

### <a name="automatically-tag-incidents"></a>Marcar incidentes automaticamente

Você pode adicionar automaticamente marcas de texto livre a incidentes para agrupá-las ou classificá-las de acordo com qualquer critério de sua escolha.

## <a name="automation-rules-execution"></a>Execução de regras de automação

As regras de automação são executadas em sequência, de acordo com a ordem que você determinar. Cada regra de automação é executada após a conclusão da execução da anterior. Em uma regra de automação, todas as ações são executadas em sequência na ordem em que são definidas.

Para ações de guia estratégico, há um atraso de dois minutos entre o início da ação de guia estratégico e a próxima ação na lista.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Permissões para regras de automação para executar guias estratégicos

Quando uma regra de automação do Azure Sentinel executa um guia estratégico, ela usa uma conta de serviço especial do Azure Sentinel especialmente autorizada para essa ação. O uso dessa conta (em oposição à sua conta de usuário) aumenta o nível de segurança do serviço.

Para que uma regra de automação execute um guia estratégico, essa conta deve receber permissões explícitas para o grupo de recursos no qual o guia estratégico reside. Nesse ponto, qualquer regra de automação poderá executar qualquer guia estratégico nesse grupo de recursos.

Quando você estiver configurando uma regra de automação e adicionar uma ação **executar guia estratégico** , uma lista suspensa de guias estratégicos será exibida. Os guias estratégicos aos quais o Azure Sentinel não tem permissões serão mostrados como indisponíveis ("esmaecido"). Você pode conceder permissão do Azure Sentinel aos grupos de recursos dos guias estratégicos no local selecionando o link **gerenciar permissões de guias estratégicos** .

> [!NOTE]
> **Permissões em uma arquitetura multilocatário**
>
> As regras de automação dão suporte total a implantações entre espaços de trabalho e vários locatários (no caso de multilocatário, usando o [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> Portanto, se a sua implantação do Azure Sentinel usar uma arquitetura multilocatário (se você for um MSSP, por exemplo), você pode ter uma regra de automação em um locatário, executar um guia estratégico que reside em um locatário diferente, mas as permissões para o sentinela executar os guias estratégicos devem ser definidas no locatário em que residem os guias

## <a name="creating-and-managing-automation-rules"></a>Criando e gerenciando regras de automação

Você pode criar e gerenciar regras de automação de diferentes pontos na experiência do Azure Sentinel, dependendo de sua necessidade e caso de uso específicos.

- **Folha de automação**

    As regras de automação podem ser gerenciadas centralmente na folha nova **automação** (que substitui a folha **Guias estratégicos** ), na guia **regras de automação** . (agora você também pode gerenciar guias estratégicos nessa folha, na guia **Guias estratégicos** .) A partir daí, você pode criar novas regras de automação e editar as existentes. Você também pode arrastar regras de automação para alterar a ordem de execução e habilitá-las ou desabilitá-las.

    Na folha **automação** , você vê todas as regras definidas no espaço de trabalho, juntamente com seu status (habilitado/desabilitado) e a quais regras de análise elas são aplicadas.

    Quando você precisar de uma regra de automação que será aplicada a muitas regras de análise, crie-a diretamente na folha **automação** . No menu superior, clique em **criar** e **Adicionar nova regra**, que abre o painel **criar nova regra de automação** . A partir daqui, você tem flexibilidade total na configuração da regra: você pode aplicá-la a qualquer regra de análise (incluindo as futuras) e definir a maior variedade de condições e ações.

- **Assistente de regra de análise**

    Na guia **resposta automática** do assistente de regra de análise, você pode ver, gerenciar e criar regras de automação que se aplicam à regra de análise específica que está sendo criada ou editada no assistente.

    Ao clicar em **criar** e em um dos tipos de regra (**regra de consulta agendada** ou **regra de criação de incidentes da Microsoft**) no menu superior da folha de **análise** , ou se você selecionar uma regra de análise existente e clicar em **Editar**, você abrirá o assistente de regra. Ao selecionar a guia **resposta automática** , você verá uma seção chamada automação de **incidentes**, sob a qual as regras de automação que atualmente se aplicam a essa regra serão exibidas. Você pode selecionar uma regra de automação existente para editar ou clicar em **Adicionar nova** para criar uma nova.

    Você observará que, quando criar a regra de automação aqui, o painel **criar nova regra de automação** mostrará a condição de **regra de análise** como indisponível, pois essa regra já está definida para aplicar somente à regra de análise que você está editando no assistente. Todas as outras opções de configuração ainda estão disponíveis para você.

- **Folha incidentes**

    Você também pode criar uma regra de automação na folha **incidentes** para responder a um único incidente recorrente. Isso é útil ao criar uma [regra de supressão](#incident-suppression) para fechar automaticamente incidentes "com ruídos". Selecione um incidente na fila e clique em **criar regra de automação** no menu superior.

    Você observará que o painel **criar nova regra de automação** preencheu todos os campos com valores do incidente. Ele nomeia a regra com o mesmo nome que o incidente, aplica-o à regra de análise que gerou o incidente e usa todas as entidades disponíveis no incidente como condições da regra. Ele também sugere uma ação de supressão (fechamento) por padrão e sugere uma data de expiração para a regra. Você pode adicionar ou remover condições e ações e alterar a data de validade, como desejar.

## <a name="auditing-automation-rule-activity"></a>Atividade de regra de automação de auditoria

Você pode estar interessado em saber o que aconteceu com um determinado incidente e o que uma determinada regra de automação pode ou não ter feito. Você tem um registro completo de crônicas de incidente disponível na tabela *SecurityIncident* na folha **logs** . Use a consulta a seguir para ver todas as suas atividades de regra de automação:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar as regras de automação para gerenciar sua fila de incidentes do Azure Sentinel e implementar uma automação básica de tratamento de incidentes.

- Para saber mais sobre opções de automação avançadas, confira [automatizar a resposta contra ameaças com guias estratégicos no Azure Sentinel](automate-responses-with-playbooks.md).
- Para obter ajuda na implementação de regras de automação e guias estratégicos, consulte [tutorial: usar guias estratégicos para automatizar respostas de ameaças no Azure Sentinel](tutorial-respond-threats-playbook.md).
