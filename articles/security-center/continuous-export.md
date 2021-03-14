---
title: A exportação contínua pode enviar alertas e recomendações da central de segurança do Azure para Log Analytics espaços de trabalho ou hubs de eventos do Azure
description: Saiba como configurar a exportação contínua de alertas de segurança e recomendações para Log Analytics espaços de trabalho ou hubs de eventos do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: fc3774a01665b88ccae2e25ae8382497f8010c35
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096965"
---
# <a name="continuously-export-security-center-data"></a>Exportar continuamente os dados da Central de Segurança

A central de segurança do Azure gera recomendações e alertas de segurança detalhados. Você pode exibi-los no portal ou por meio de ferramentas programáticas. Talvez você também precise exportar algumas ou todas essas informações para acompanhamento com outras ferramentas de monitoramento em seu ambiente. 

A **exportação contínua** permite que você personalize totalmente *o que* será exportado e o *local em que* ele vai. Por exemplo, você pode configurá-lo para que:

- Todos os alertas de severidade alta são enviados para um hub de eventos do Azure
- Todas as conclusões de severidade médias ou mais altas das verificações de avaliação de vulnerabilidade de seus SQL Servers são enviadas para um espaço de trabalho específico do Log Analytics
- Recomendações específicas são entregues a um hub de eventos ou Log Analytics espaço de trabalho sempre que são geradas 
- A pontuação segura de uma assinatura é enviada para um espaço de trabalho Log Analytics sempre que a pontuação de um controle é alterada por 0, 1 ou mais 

Embora o recurso seja chamado de *contínuo*, também há uma opção para exportar instantâneos semanais de Pontuação segura ou dados de conformidade regulatória.

Este artigo descreve como configurar a exportação contínua para Log Analytics espaços de trabalho ou hubs de eventos do Azure.

> [!NOTE]
> Se você precisar integrar a central de segurança com um SIEM, consulte [transmitir alertas para uma solução Siem, disparar ou de gerenciamento de serviço de ti](export-to-siem.md).

> [!TIP]
> A central de segurança também oferece a opção de executar uma única exportação manual para CSV. Saiba mais em [uma única exportação manual de alertas e recomendações](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Gratuita|
|Funções e permissões necessárias:|<ul><li>**Administrador de segurança** ou **proprietário** no grupo de recursos</li><li>Permissões de gravação para o recurso de destino</li><li>Se você estiver usando as Azure Policy políticas ' DeployIfNotExist ' descritas abaixo, também precisará de permissões para atribuir políticas</li></ul>|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov, outros governos<br>![Sim](./media/icons/yes-icon.png) Governo da China|
|||


## <a name="what-data-types-can-be-exported"></a>Quais tipos de dados podem ser exportados?

A exportação contínua pode exportar os seguintes tipos de dados sempre que eles forem alterados:

- Alertas de segurança
- Recomendações de segurança 
- Conclusões de segurança que podem ser consideradas como recomendações "sub", como descobertas de scanners de avaliação de vulnerabilidade ou atualizações específicas do sistema. Você pode optar por incluí-las com suas recomendações "pai", como "as atualizações do sistema devem ser instaladas em seus computadores".
- Pontuação segura (por assinatura ou por controle)
- Dados de conformidade regulatória

> [!NOTE]
> A exportação de dados de conformidade regulatória e de Pontuação segura é um recurso de visualização e não está disponível em nuvens governamentais. 

## <a name="set-up-a-continuous-export"></a>Configurar uma exportação contínua 

Você pode configurar a exportação contínua nas páginas da central de segurança no portal do Azure, por meio da API REST da central de segurança ou em escala usando os modelos de Azure Policy fornecidos. Selecione a guia apropriada abaixo para obter detalhes de cada um.

### <a name="use-the-azure-portal"></a>[**Usar o portal do Azure**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Configurar a exportação contínua das páginas da central de segurança no portal do Azure

As etapas a seguir são necessárias se você estiver configurando uma exportação contínua para Log Analytics espaço de trabalho ou hubs de eventos do Azure.

1. Na barra lateral da central de segurança, selecione **preços & configurações**.
1. Selecione a assinatura específica para a qual você deseja configurar a exportação de dados.
1. Na barra lateral da página de configurações dessa assinatura, selecione **exportação contínua**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Opções de exportação na central de segurança do Azure":::

    Aqui você vê as opções de exportação. Há uma guia para cada destino de exportação disponível. 

1. Selecione o tipo de dados que você deseja exportar e escolha um dos filtros em cada tipo (por exemplo, exportar somente alertas de severidade alta).
1. Selecione a frequência de exportação apropriada:
    - **Streaming** – as avaliações serão enviadas em tempo real quando o estado de integridade de um recurso for atualizado (se nenhuma atualização ocorrer, nenhum dado será enviado).
    - **Instantâneos** – um instantâneo do estado atual de todas as avaliações de conformidade regulatória será enviado a cada semana (essa é uma versão prévia do recurso para instantâneos semanais de classificações de segurança e dados de conformidade regulatória).

1. Opcionalmente, se sua seleção incluir uma dessas recomendações, você poderá incluir as descobertas de avaliação de vulnerabilidade junto com elas:
    - As descobertas de avaliação de vulnerabilidade em seus bancos de dados SQL devem ser corrigidas
    - As descobertas de avaliação de vulnerabilidade em seus SQL Servers em computadores devem ser corrigidas (visualização)
    - As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas (da plataforma Qualys)
    - As vulnerabilidades nas suas máquinas virtuais devem ser corrigidas
    - As atualizações do sistema devem ser instaladas em suas máquinas

    Para incluir as conclusões com essas recomendações, habilite a opção **incluir conclusões de segurança** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir a alternância de descobertas de segurança na configuração de exportação contínua" :::

1. Na área "destino de exportação", escolha onde você deseja que os dados sejam salvos. Os dados podem ser salvos em um destino em uma assinatura diferente (por exemplo, em uma instância central de Hub de eventos ou em um espaço de trabalho central Log Analytics).
1. Clique em **Salvar**.

### <a name="use-the-rest-api"></a>[**Usar a API REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Configurar a exportação contínua usando a API REST

A exportação contínua pode ser configurada e gerenciada por meio da [API de automação](/rest/api/securitycenter/automations)da central de segurança do Azure. Use esta API para criar ou atualizar regras para exportar para qualquer um dos seguintes destinos possíveis:

- Hub de Eventos do Azure
- Espaço de trabalho do Log Analytics
- Aplicativos Lógicos do Azure 

A API fornece funcionalidade adicional não disponível no portal do Azure, por exemplo:

* **Volume maior** -a API permite que você crie várias configurações de exportação em uma única assinatura. A página de **exportação contínua** na interface do usuário do portal da central de segurança dá suporte a apenas uma configuração de exportação por assinatura.

* **Recursos adicionais** – a API oferece parâmetros adicionais que não são mostrados na interface do usuário. Por exemplo, você pode adicionar marcas ao recurso de automação, bem como definir a exportação com base em um conjunto mais amplo de propriedades de alerta e recomendação do que aquelas oferecidas na página de **exportação contínua** na interface do usuário do portal da central de segurança.

* **Escopo mais focalizado** -a API fornece um nível mais granular para o escopo de suas configurações de exportação. Ao definir uma exportação com a API, você pode fazer isso no nível do grupo de recursos. Se você estiver usando a página **exportação contínua** na interface do usuário do portal da central de segurança, precisará defini-la no nível da assinatura.

    > [!TIP]
    > Se você tiver configurado várias configurações de exportação usando a API ou se tiver usado parâmetros somente de API, esses recursos adicionais não serão exibidos na interface do usuário da central de segurança. Em vez disso, haverá uma faixa informando que existem outras configurações.

Saiba mais sobre a API automations na [documentação da API REST](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Implantar em escala com Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Configurar a exportação contínua em escala usando as políticas fornecidas

A automatização dos processos de monitoramento e resposta a incidentes da sua organização pode aprimorar significativamente o tempo necessário para investigar e atenuar incidentes de segurança.

Para implantar suas configurações de exportação contínua em sua organização, use as políticas de "DeployIfNotExist" fornecidas Azure Policy descritas abaixo para criar e configurar procedimentos de exportação contínua.

**Para implementar essas políticas**

1. Na tabela a seguir, selecione a política que você deseja aplicar:

    |Goal  |Política  |ID da Política  |
    |---------|---------|---------|
    |Exportação contínua para o hub de eventos|[Implantar a exportação para o Hub de Eventos para os alertas e as recomendações Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Exportação contínua para o workspace do Log Analytics|[Implantar a exportação para o workspace do Log Analytics para os alertas e as recomendações da Central de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Você também pode encontrá-los pesquisando Azure Policy:
    > 1. Abra Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Acessando Azure Policy":::
    > 2. No menu Azure Policy, selecione **definições** e pesquise-as por nome. 

1. Na página Azure Policy relevante, selecione **atribuir**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Atribuindo o Azure Policy":::

1. Abra cada guia e defina os parâmetros conforme desejado:
    1. Na guia **noções básicas** , defina o escopo da política. Para usar o gerenciamento centralizado, atribua a política ao grupo de gerenciamento que contém as assinaturas que usarão a configuração de exportação contínua. 
    1. Na guia **parâmetros** , defina o grupo de recursos e os detalhes do tipo de dados. 
        > [!TIP]
        > Cada parâmetro tem uma dica de ferramenta explicando as opções disponíveis para você.
        >
        > A guia de parâmetros de Azure Policy (1) fornece acesso a opções de configuração semelhantes como página de exportação contínua da central de segurança (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Comparando os parâmetros na exportação contínua com Azure Policy" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Opcionalmente, para aplicar essa atribuição a assinaturas existentes, abra a guia **correção** e selecione a opção para criar uma tarefa de correção.
1. Examine a página Resumo e selecione **criar**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informações sobre como exportar para um espaço de trabalho Log Analytics

Se você quiser analisar os dados da central de segurança do Azure dentro de um espaço de trabalho Log Analytics ou usar alertas do Azure junto com alertas da central de segurança, configure a exportação contínua para seu espaço de trabalho Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabelas e esquemas

Alertas de segurança e recomendações são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendation* , respectivamente. 

O nome da solução de Log Analytics que contém essas tabelas depende se você tem o Azure defender habilitado: segurança (' Segurança e Auditoria ') ou SecurityCenterFree. 

> [!TIP]
> Para ver os dados no espaço de trabalho de destino, você deve habilitar uma dessas soluções **segurança e auditoria** ou **SecurityCenterFree**.

![A tabela * SecurityAlert * no Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para exibir os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de tabela de log Analytics](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Exibir alertas exportados e recomendações no Azure Monitor

Você também pode optar por exibir alertas de segurança exportados e/ou recomendações em [Azure monitor](../azure-monitor/alerts/alerts-overview.md). 

O Azure Monitor fornece uma experiência de alerta unificada para uma variedade de alertas do Azure, incluindo log de diagnóstico, alertas de métrica e alertas personalizados com base em consultas de espaço de trabalho Log Analytics.

Para exibir alertas e recomendações da central de segurança no Azure Monitor, configure uma regra de alerta com base em consultas Log Analytics (alerta de log):

1. Na página **alertas** do Azure monitor, selecione **nova regra de alerta**.

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página Criar regra, configure sua nova regra (da mesma maneira que você configurou uma [regra de alerta de log em Azure monitor](../azure-monitor/alerts/alerts-unified-log.md)):

    * Para **recurso**, selecione o espaço de trabalho log Analytics para o qual você exportou alertas de segurança e recomendações.

    * Para **condição**, selecione **pesquisa de logs personalizada**. Na página que aparece, configure a consulta, o período de lookback e o período de frequência. Na consulta de pesquisa, você pode digitar *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados que a central de segurança exporta continuamente para quando você habilita a exportação contínua para log Analytics recurso. 
    
    * Opcionalmente, configure o [grupo de ações](../azure-monitor/alerts/action-groups.md) que você gostaria de disparar. Os grupos de ações podem disparar envio de email, tíquetes de ITSM, WebHooks e muito mais.
    ![Azure Monitor regra de alerta](./media/continuous-export/azure-monitor-alert-rule.png)

Agora, você verá novos alertas ou recomendações da central de segurança do Azure (dependendo de suas regras de exportação contínua configuradas e da condição que você definiu em sua Azure Monitor regra de alerta) em Azure Monitor alertas, com o disparo automático de um grupo de ações (se fornecido).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Exportação única de alertas e recomendações manuais

Para baixar um relatório CSV para alertas ou recomendações, abra a página **alertas de segurança** ou **recomendações** e selecione o botão **baixar relatório CSV** .

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Baixar dados de alertas como um arquivo CSV" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Esses relatórios contêm alertas e recomendações para recursos das assinaturas atualmente selecionadas.


## <a name="faq---continuous-export"></a>Perguntas frequentes-exportação contínua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quais são os custos envolvidos na exportação de dados?

Não há custos para habilitar uma exportação contínua. Os custos podem ser incorridos para ingestão e retenção de dados em seu espaço de trabalho Log Analytics, dependendo de sua configuração. 

Saiba mais sobre os [preços do espaço de trabalho do log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Saiba mais sobre os [preços do hub de eventos do Azure](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>A exportação inclui dados sobre o estado atual de todos os recursos?

Não. A exportação contínua foi criada para streaming de **eventos**:

- Os **alertas** recebidos antes de habilitar a exportação não serão exportados.
- As **recomendações** são enviadas sempre que o estado de conformidade de um recurso é alterado. Por exemplo, quando um recurso muda de íntegro para não íntegro. Portanto, assim como os alertas, as recomendações para recursos que não mudaram de estado desde que você habilitou a exportação não serão exportadas.
- A **Pontuação segura (versão prévia)** por controle de segurança ou assinatura é enviada quando a pontuação de um controle de segurança é alterada por 0, 1 ou mais. 
- O **status de conformidade regulatória (versão prévia)** é enviado quando o status da conformidade do recurso é alterado.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>Por que as recomendações são enviadas em intervalos diferentes?

Recomendações diferentes têm intervalos de avaliação de conformidade diferentes, que podem variar de alguns minutos a cada poucos dias. Consequentemente, as recomendações serão diferentes na quantidade de tempo necessário para que elas apareçam em suas exportações.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>A exportação contínua dá suporte a qualquer cenário de continuidade de negócios ou BCDR (recuperação de desastre)?

Ao preparar seu ambiente para cenários BCDR, em que o recurso de destino está passando por uma interrupção ou outro desastre, é responsabilidade da organização evitar a perda de dados estabelecendo backups de acordo com as diretrizes dos hubs de eventos do Azure, do espaço de trabalho Log Analytics e do aplicativo lógico.

Saiba mais nos [hubs de eventos do Azure – recuperação de desastres geograficamente](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>A exportação contínua está disponível com a central de segurança do Azure gratuita?

Sim. Observe que muitos alertas da central de segurança são fornecidos apenas quando você habilitou o Azure defender. Uma boa maneira de visualizar os alertas que você obterá em seus dados exportados é Ver os alertas mostrados nas páginas da central de segurança na portal do Azure.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar as exportações contínuas de suas recomendações e alertas. Você também aprendeu a baixar seus dados de alertas como um arquivo CSV. 

Para obter material relacionado, consulte a seguinte documentação: 

- Saiba mais sobre [modelos de automação de fluxo de trabalho](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Documentação dos Hubs de Eventos do Azure](../event-hubs/index.yml)
- [Documentação do Azure Sentinel](../sentinel/index.yml)
- [Documentação do Azure Monitor](../azure-monitor/index.yml)
- [Exportar esquemas de tipos de dados](https://aka.ms/ASCAutomationSchemas)