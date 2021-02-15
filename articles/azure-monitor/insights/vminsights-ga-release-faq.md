---
title: Perguntas frequentes sobre Azure Monitor para VMs (GA) | Microsoft Docs
description: O Azure Monitor para VMs é uma solução do Azure que combina integridade e monitoramento de desempenho do sistema operacional da VM do Azure, bem como a descoberta automática dos componentes de aplicativo e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo responde a perguntas comuns sobre a versão GA.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: be192a5807c40ea65ea6533ec6244183e5a4b644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958789"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Perguntas frequentes de Azure Monitor para VMs disponíveis (GA)
Essas perguntas frequentes sobre disponibilidade geral abordam as alterações feitas no quarto trimestre de 2019 e no primeiro trimestre 2020, conforme preparamos para a GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Atualizações para Azure Monitor para VMs
Lançamos uma nova versão do Azure Monitor para VMs em janeiro de 2020 à frente de nosso comunicado de GA. Os clientes que habilitam o Azure Monitor para VMs receberão a versão GA, mas os clientes existentes que usam a versão do Azure Monitor para VMs do quarto trimestre de 2019 e anteriores serão solicitados a fazer a atualização. Estas perguntas frequentes oferecem orientação para executar uma atualização em escala se você tiver grandes implantações em vários espaços de trabalho.


Com essa atualização, Azure Monitor para VMs dados de desempenho são armazenados na mesma tabela *InsightsMetrics* que [Azure monitor para contêineres](container-insights-overview.md), o que torna mais fácil consultar os dois conjuntos de dados. Além disso, você pode armazenar conjuntos de dados mais diversificados que não pudemos armazenar na tabela usada anteriormente. 

Nossas exibições de desempenho agora estão usando os dados que armazenamos na tabela *InsightsMetrics* .  Se você ainda não tiver atualizado para usar a solução VMInsights mais recente em seu espaço de trabalho, seus gráficos não exibirão mais informações.  Você pode atualizar de nossa página de **introdução** , conforme descrito abaixo.


## <a name="what-is-changing"></a>O que está mudando?
Lançamos uma nova solução, chamada VMInsights, que inclui recursos adicionais para a coleta de dados, juntamente com um novo local para armazenar esses dados em seu espaço de trabalho de Log Analytics. 

No passado, habilitamos a solução ServiceMap em seu espaço de trabalho e configuramos contadores de desempenho em seu espaço de trabalho Log Analytics para enviar os dados para a tabela *perf* . Essa nova solução envia os dados para uma tabela chamada *InsightsMetrics* que também é usada pelo Azure monitor para contêineres. Esse esquema de tabela nos permite armazenar métricas adicionais e conjuntos de dados de serviço que não são compatíveis com o formato de tabela *perf* .

Atualizamos nossos gráficos de desempenho para usar os dados que armazenamos na tabela *InsightsMetrics* . Você pode atualizar para usar a tabela *InsightsMetrics* de nossa página de **introdução** , conforme descrito abaixo.


## <a name="how-do-i-upgrade"></a>Como fazer atualizar?
Quando um espaço de trabalho Log Analytics é atualizado para a versão mais recente do Azure Monitor para VMs, ele atualizará o agente de dependência em cada uma das VMs anexadas a esse espaço de trabalho. Cada VM que requer atualização será identificada na guia **introdução** em Azure Monitor para VMs no portal do Azure. Quando você optar por atualizar uma VM, ela atualizará o espaço de trabalho para essa VM junto com todas as outras VMs anexadas a esse espaço de trabalho. Você pode selecionar uma única VM ou várias VMs, grupos de recursos ou assinaturas. 

Use o seguinte comando para atualizar um espaço de trabalho usando o PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os contadores de desempenho em meu espaço de trabalho se eu instalar a solução VMInsights?

O método anterior de habilitar Azure Monitor para VMs contadores de desempenho usados em seu espaço de trabalho. A versão atual armazena esses dados em uma tabela chamada `InsightsMetrics` . Você pode optar por desabilitar esses contadores de desempenho em seu espaço de trabalho se não precisar mais usá-los. 

>[!NOTE]
>Se você tiver regras de alerta que referenciem esses contadores na `Perf` tabela, será necessário atualizá-los para fazer referência a novos dados armazenados na `InsightsMetrics` tabela. Consulte nossa documentação para obter exemplos de consultas de log que você pode usar para fazer referência a esta tabela.
>

Se você decidir manter os contadores de desempenho habilitados, você será cobrado pelos dados ingeridos e armazenados na `Perf` tabela com base em [preços de log Analytics [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como essa alteração afetará minhas regras de alerta?

Se você tiver criado [alertas de log](../platform/alerts-unified-log.md) que consultam a `Perf` tabela que direciona os contadores de desempenho que foram habilitados no espaço de trabalho, você deve atualizar essas regras para fazer referência à `InsightsMetrics` tabela em vez disso. Essa orientação também se aplica a qualquer regra de pesquisa de log usando `ServiceMapComputer_CL` e `ServiceMapProcess_CL` , porque esses conjuntos de dados estão sendo movidos para `VMComputer` `VMProcess` tabelas e.

Atualizaremos essas perguntas frequentes e nossa documentação para incluir regras de alerta de pesquisa de log de exemplo para os conjuntos de dados que coletamos.

## <a name="how-will-this-affect-my-bill"></a>Como isso afetará minha fatura?

A cobrança ainda se baseia nos dados ingeridos e retidos em seu espaço de trabalho Log Analytics.

Os dados de desempenho no nível da máquina que coletamos são os mesmos, é de um tamanho semelhante aos dados que armazenamos na `Perf` tabela e custarão aproximadamente o mesmo valor.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu quiser usar apenas Mapa do Serviço?

Tudo bem. Você verá prompts na portal do Azure ao exibir Azure Monitor para VMs sobre a próxima atualização. Depois de liberado, você receberá uma solicitação solicitando que você atualize para a nova versão. Se preferir usar apenas o recurso [Maps](vminsights-maps.md) , você poderá optar por não atualizar e continuar a usar o recurso maps no Azure monitor para VMs e a solução de mapa do serviço acessada do seu espaço de trabalho ou bloco do Dashboard.

Se você optar por habilitar manualmente os contadores de desempenho em seu espaço de trabalho, poderá ver os dados em alguns dos nossos gráficos de desempenho exibidos em Azure Monitor. Depois que a nova solução for lançada, atualizaremos nossos gráficos de desempenho para consultar os dados armazenados na `InsightsMetrics` tabela. Se você quiser ver os dados dessa tabela nesses gráficos, será necessário atualizar para a nova versão do Azure Monitor para VMs.

As alterações para mover dados do `ServiceMapComputer_CL` e `ServiceMapProcess_CL` afetarão Mapa do Serviço e Azure monitor para VMs, para que você ainda precise planejar essa atualização.

Se você optar por não atualizar para a solução **VMInsights** , continuaremos a fornecer versões herdadas de nossas pastas de trabalho de desempenho que se referem aos dados na `Perf` tabela.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados de Mapa do Serviço também serão armazenados em InsightsMetrics?

Os conjuntos de dados não serão duplicados se você usar ambas as soluções. As duas ofertas compartilham os conjuntos de dados que serão armazenados `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` tabelas para armazenar os conjuntos de dados do mapa que coletamos.  

A `InsightsMetrics` tabela armazenará a VM, o processo e os conjuntos de dados de serviço que coletamos e só serão populados se você estiver usando Azure monitor para VMs e a solução de informações de VM. A solução de Mapa do Serviço não coletará ou armazenará dados na `InsightsMetrics` tabela.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serei uma cobrança dupla se eu tiver as soluções Mapa do Serviço e VMInsights em meu espaço de trabalho?

Não, as duas soluções compartilham os conjuntos de dados do mapa que armazenamos `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` . Você não será cobrado duas vezes por esses dados se tiver as duas soluções em seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se eu remover a solução Mapa do Serviço ou VMInsights, ela removerá meus dados?

Não, as duas soluções compartilham os conjuntos de dados do mapa que armazenamos `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` . Se você remover uma das soluções, esses conjuntos de dados perceberão que ainda há uma solução em vigor que usa os dados e ela permanece no espaço de trabalho Log Analytics. Você precisa remover ambas as soluções do seu espaço de trabalho para que os dados sejam removidos dela.

## <a name="health-feature-is-in-limited-public-preview"></a>O recurso de integridade está em visualização pública limitada

Recebemos muitos comentários incríveis de clientes sobre nosso conjunto de recursos de integridade da VM. Há muito interesse em relação a esse recurso e empolga o seu potencial para dar suporte a fluxos de trabalho de monitoramento. Estamos planejando fazer uma série de alterações para adicionar funcionalidade e abordar os comentários que recebemos. 

Para minimizar o impacto dessas alterações em novos clientes, movemos esse recurso para uma **Visualização pública limitada**. Essa atualização ocorreu em outubro de 2019.

Planejamos reiniciar esse recurso de integridade no 2020, depois que Azure Monitor para VMs estiver em GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como os clientes existentes acessam o recurso de integridade?

Os clientes existentes que estão usando o recurso de integridade continuarão a ter acesso a ele, mas não serão oferecidos a novos clientes.  

Para acessar o recurso, você pode adicionar o seguinte sinalizador de recurso `feature.vmhealth=true` à URL de portal do Azure [https://portal.azure.com](https://portal.azure.com) . Exemplo `https://portal.azure.com/?feature.vmhealth=true` .

Você também pode usar essa URL curta, que define o sinalizador de recurso automaticamente: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Como um cliente existente, você pode continuar a usar o recurso de integridade em VMs que estão conectadas a uma configuração de espaço de trabalho existente com a funcionalidade de integridade.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Eu uso a integridade da VM agora com um ambiente e gostaria de implantá-la em uma nova

Se você for um cliente existente que está usando o recurso de integridade e deseja usá-lo para uma nova distribuição, entre em contato conosco em vminsights@microsoft.com para solicitar instruções.

## <a name="next-steps"></a>Próximas etapas

Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-enable-overview.md).
