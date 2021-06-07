---
title: Aprimorar a excelência operacional com o Assistente
description: Use o Assistente do Azure para otimizar e desenvolver excelência operacional para suas assinaturas do Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579960"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Obter excelência operacional usando o Assistente do Azure

As recomendações de excelência operacional no Assistente do Azure podem ajudar você com: 
- Eficiência do processo e do fluxo de trabalho.
- Capacidade de gerenciamento de recursos.
- Melhores práticas de implantação. 

Você pode obter essas recomendações na guia **Excelência operacional** do painel do Assistente.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Criar alertas de Integridade do Serviço do Azure para receber notificação quando problemas do Azure afetarem você

Recomendamos que você configure alertas de Integridade do Serviço do Azure para receber notificação quando os problemas de serviço do Azure afetarem você. A [Integridade do Serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece diretrizes e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Assistente identifica as assinaturas que não têm alertas configurados e recomenda a criação deles.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Criar contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure dá suporte a um máximo de 250 contas de armazenamento por assinatura. Depois de atingir esse limite, você não poderá criar contas de armazenamento nessa combinação de região/assinatura. O Assistente verifica suas assinaturas e fornece recomendações de criação para menos contas de armazenamento para qualquer região/assinatura que esteja perto de atingir o limite.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que você terá acesso aos especialistas de nuvem do Azure quando precisar

Ao executar uma carga de trabalho comercialmente crítica, é importante ter acesso ao suporte técnico quando necessário. O Assistente identifica possíveis assinaturas comercialmente críticas que não têm suporte técnico incluído nos planos de suporte. Ele recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Excluir e recriar o pool para remover um componente interno preterido

Se o seu pool estiver usando um componente interno preterido, exclua e recrie o pool para aprimorar a estabilidade e o desempenho.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Assistente do Azure detecta regras de alerta que têm consultas inválidas especificadas na seção de condição. É possível criar regras de alertas de log no Azure Monitor e usá-las para executar consultas de análise em intervalos especificados. Os resultados da consulta determinarão se um alerta precisar ser disparado. As consultas de análise podem se tornar inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Assistente recomenda corrigir a consulta na regra de alertas para impedir que ela seja desabilitada automaticamente e assegurar a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre a solução de problemas de regras de alertas.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Usar recomendações do Azure Policy

O Azure Policy é um serviço no Azure que você pode usar para criar, atribuir e gerenciar políticas. Essas políticas impõem regras e efeitos em seus recursos. As seguintes recomendações do Azure Policy podem ajudar você a obter excelência operacional: 

**Gerenciar marcas.** Essa política adiciona ou substitui a tag e o valor especificados quando qualquer recurso é criado ou atualizado. Você pode corrigir os recursos existentes disparando uma tarefa de correção. Essa política não modifica marcas em grupos de recursos.

**Impor requisitos de conformidade geográfica.** Esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. 

**Especificar SKUs de máquina virtual permitidas para implantações.** Esta política permite especificar um conjunto de SKUs de máquina virtual que sua organização pode implantar.

**Impor *Auditar VMs que não usam discos gerenciados*.**

**Habilitar *Herdar marca de grupos de recursos*.** Essa política adiciona ou substitui a tag e o valor especificados do grupo de recursos pai quando qualquer recurso é criado ou atualizado. Você pode corrigir os recursos existentes disparando uma tarefa de correção.

O Assistente recomenda algumas políticas individuais do Azure que ajudam os clientes a alcançar a excelência operacional adotando as melhores práticas. Se um cliente decidir atribuir uma política recomendada, suprimiremos a recomendação. Se o cliente decidir remover a política mais tarde, o Assistente continuará a suprimir a recomendação porque interpretamos a remoção como um sinal forte do seguinte:

1.  O cliente removeu a política porque, apesar da recomendação do Assistente, ele não se aplica ao seu caso de uso específico. 
2.  O cliente está ciente e familiarizado com a política depois de atribui-la e removê-la e pode atribui-la ou removê-la novamente, conforme necessário, sem diretrizes, se for mais tarde relevante para o caso de uso dele. Se o cliente achar melhor atribuir a mesma política novamente, ele poderá fazer isso no Azure Policy sem a necessidade de uma recomendação no Assistente. Observe que essa lógica se aplica especificamente à recomendação de política na categoria Excelência Operacional. Essas regras não se aplicam a recomendações de segurança.  


## <a name="no-validation-environment-enabled"></a>Nenhum ambiente de validação habilitado
O Assistente do Azure determina que você não tem um ambiente de validação habilitado na assinatura atual. Ao criar seus pools de host, você escolheu \"Não\" para \"Ambiente de validação\" na guia Propriedades. Ter pelo menos um pool de host com um ambiente de validação habilitado garante a continuidade dos negócios por meio de implantações do serviço de Área de Trabalho Virtual do Windows com a detecção antecipada de possíveis problemas. [Saiba mais](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Assegure que o ambiente de produção (não validação) seja beneficiado pela funcionalidade estável
O Assistente do Azure detecta que os ambientes de validação de muitos dos pools de host estão habilitados. Para que os ambientes de validação atendam melhor às finalidades deles, você deve ter pelo menos um, mas nunca mais do que metade dos pools de host no ambiente de validação. Mediante um equilíbrio íntegro entre os pools de host com o ambiente de validação habilitado e com o ambiente de validação desabilitado, você poderá aproveitar melhor os benefícios das implantações em vários estágios que a Área de Trabalho Virtual do Windows oferece para certas atualizações. Para corrigir esse problema, abra as propriedades do pool de host e selecione \"Não\" ao lado da configuração \"Ambiente de Validação\".

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Habilitar Análise de Tráfego para exibir informações sobre os padrões de tráfego nos recursos do Azure
Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo no Azure. A Análise de Tráfego analisa os logs de fluxo de NSG (grupo de segurança de rede) do Observador de Rede para fornecer informações sobre o fluxo de tráfego. Com a Análise de Tráfego, você pode exibir os principais palestrantes no Azure e em implantações não Azure, investigar portas abertas, protocolos e fluxos mal-intencionados em seu ambiente e otimizar a implantação de rede para desempenho. Você pode processar os logs de fluxo em intervalos de processamento de 10 minutos e 60 minutos, oferecendo uma análise mais rápida sobre o tráfego. É uma melhor prática habilitar a Análise de Tráfego para os recursos do Azure. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de custo do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de confiabilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Assistente](advisor-security-recommendations.md)
* [API REST do Assistente](/rest/api/advisor/)
