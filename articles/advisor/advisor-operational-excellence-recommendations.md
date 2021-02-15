---
title: Melhorar o excellency operacional com o Advisor
description: Use o Azure Advisor para otimizar e amadurecer sua excelência operacional para suas assinaturas do Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 63e88129a7418e82ea13429c33d8735e96616476
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122612"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Obtenha excelência operacional usando o Azure Advisor

As recomendações de excelência operacional no Azure Advisor podem ajudá-lo com: 
- Eficiência do processo e do fluxo de trabalho.
- Capacidade de gerenciamento de recursos.
- Práticas recomendadas de implantação. 

Você pode obter essas recomendações na guia **excelência operacional** do painel do Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crie alertas de integridade do serviço do Azure para ser notificado quando os problemas do Azure afetarem você

Recomendamos que você configure alertas de integridade do serviço do Azure para que você seja notificado quando os problemas de serviço do Azure afetarem você. A [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Advisor identifica as assinaturas que não têm alertas configurados e recomenda configurá-los.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Projete suas contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure pode dar suporte a um máximo de 250 contas de armazenamento por assinatura. Depois de atingir esse limite, você não poderá criar contas de armazenamento nessa combinação de região/assinatura. O Advisor verifica suas assinaturas e fornece recomendações para você projetar para menos contas de armazenamento para qualquer região/assinatura que esteja perto de atingir o limite.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que você terá acesso aos especialistas de nuvem do Azure quando precisar

Ao executar uma carga de trabalho comercialmente crítica, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica as possíveis assinaturas críticas para os negócios que não têm suporte técnico incluído em seu plano de suporte. Ele recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Excluir e recriar o pool para remover um componente interno preterido

Se o seu pool estiver usando um componente interno preterido, exclua e recrie o pool para melhorar a estabilidade e o desempenho.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detecta regras de alerta que têm consultas inválidas especificadas na seção condição. Você pode criar regras de alerta de log no Azure Monitor e usá-las para executar consultas de análise em intervalos especificados. Os resultados da consulta determinarão se um alerta precisar ser disparado. As consultas de análise podem se tornar inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomenda que você corrija a consulta na regra de alerta para impedir que ela seja automaticamente desativada e garantir a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre como solucionar problemas de regras de alerta.](../azure-monitor/platform/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Usar recomendações de Azure Policy

Azure Policy é um serviço no Azure que você pode usar para criar, atribuir e gerenciar políticas. Essas políticas impõem regras e efeitos em seus recursos. As recomendações de Azure Policy a seguir podem ajudá-lo a obter o excellency operacional: 

**Gerenciar marcas.** Essa política adiciona ou substitui a tag e o valor especificados quando qualquer recurso é criado ou atualizado. Você pode corrigir os recursos existentes disparando uma tarefa de correção. Essa política não modifica marcas em grupos de recursos.

**Impor os requisitos de conformidade geográfica.** Esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. 

**Especifique SKUs de máquina virtual permitidas para implantações.** Esta política permite especificar um conjunto de SKUs de máquina virtual que sua organização pode implantar.

**Impor as *VMs de auditoria que não usam discos gerenciados*.**

**Habilitar *herdar uma marca de grupos de recursos*.** Essa política adiciona ou substitui a tag e o valor especificados do grupo de recursos pai quando qualquer recurso é criado ou atualizado. Você pode corrigir os recursos existentes disparando uma tarefa de correção.

O Advisor recomenda algumas políticas individuais do Azure que ajudam os clientes a alcançar a excelência operacional adotando as práticas recomendadas. Se um cliente decidir atribuir uma política recomendada, suprimiremos a recomendação. Se o cliente decidir remover a política mais tarde, o Advisor continuará a suprimir a recomendação porque interpretamos sua remoção como um sinal forte do seguinte:

1.  O cliente removeu a política porque, apesar da recomendação do consultor, ele não se aplica ao seu caso de uso específico. 
2.  O cliente está ciente e familiar com a política depois de atribuí-la e removê-la, e pode atribuí-la ou removê-la novamente, conforme necessário, sem orientação, se for mais tarde relevante para seu caso de uso. Se o cliente encontrá-lo em seu melhor interesse para atribuir a mesma política novamente, ele pode fazer isso em Azure Policy sem a necessidade de uma recomendação no Advisor. Observe que essa lógica se aplica especificamente à recomendação de política na categoria excelência operacional. Essas regras não se aplicam a recomendações de segurança.  


## <a name="no-validation-environment-enabled"></a>Nenhum ambiente de validação habilitado
O supervisor do Azure determina que você não tem um ambiente de validação habilitado na assinatura atual. Ao criar seus pools de hosts, você selecionou \" não \" para \" o ambiente de validação \" na guia Propriedades. Ter pelo menos um pool de hosts com um ambiente de validação habilitado garante a continuidade de negócios por meio de implantações do serviço de área de trabalho virtual do Windows com a detecção antecipada de possíveis problemas [Saiba mais](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Assegure que o ambiente de produção (não validação) seja beneficiado pela funcionalidade estável
O Azure Advisor detecta que muitos de seus pools de hosts têm o ambiente de validação habilitado. Para que os ambientes de validação atendam melhor às suas finalidades, você deve ter pelo menos um, mas nunca mais do que metade de seus pools de hosts no ambiente de validação. Tendo um equilíbrio íntegro entre seus pools de hosts com o ambiente de validação habilitado e aqueles com ele desabilitado, você poderá utilizar os benefícios das implantações de multiestágio que a área de trabalho virtual do Windows oferece com determinadas atualizações. Para corrigir esse problema, abra as propriedades do pool de hosts e selecione \" não ao \" lado da \" configuração do ambiente de validação \" .

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Habilitar Análise de Tráfego para exibir informações sobre os padrões de tráfego nos recursos do Azure
Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo no Azure. A Análise de Tráfego analisa os logs de fluxo de NSG (grupo de segurança de rede) do Observador de Rede para fornecer informações sobre o fluxo de tráfego. Com a Análise de Tráfego, você pode exibir os principais palestrantes no Azure e em implantações não Azure, investigar portas abertas, protocolos e fluxos mal-intencionados em seu ambiente e otimizar a implantação de rede para desempenho. Você pode processar os logs de fluxo em intervalos de processamento de 10 minutos e 60 minutos, oferecendo uma análise mais rápida sobre o tráfego. É uma prática recomendada habilitar Análise de Tráfego para os recursos do Azure. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de confiabilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [API REST do Advisor](/rest/api/advisor/)
