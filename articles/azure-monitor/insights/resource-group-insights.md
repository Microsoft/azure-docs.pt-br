---
title: Insights do Grupo de Recursos do Azure Monitor | Microsoft Docs
description: Compreender a integridade e o desempenho dos serviços e aplicativos distribuídos no nível do Grupo de Recursos com Azure Monitor
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bfe197befc781548dc41ff389f2d9558af7a8aae
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567395"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorar grupos de recursos com Azure Monitor (versão prévia)

Aplicativos modernos são frequentemente complexos e altamente distribuídos, com muitas partes discretas trabalhando em conjunto para fornecer um serviço. Reconhecendo essa complexidade, o Azure Monitor fornece insights de monitoramento para grupos de recursos. Isso facilita a triagem e o diagnóstico de quaisquer problemas encontrados pelos recursos individuais, oferecendo contexto sobre a integridade e o desempenho do grupo de recursos&mdash;e do aplicativo&mdash;como um todo.

## <a name="access-insights-for-resource-groups"></a>Acessar insights para grupos de recursos

1. Selecione **Grupos de recursos** na barra de navegação esquerda.
2. Escolha um dos grupos de recursos que você quer explorar. (Se você tiver um grande número de grupos de recursos, a filtragem por assinatura poderá, às vezes, ser útil.)
3. Para acessar insights de um grupo de recursos, clique em **Insights** no menu esquerdo de qualquer grupo de recursos.

![Captura de tela da página de visão geral de insights do grupo de recursos](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Recursos com alertas ativos e problemas de integridade

A página de visão geral mostra quantos alertas foram acionados e ainda estão ativos, juntamente com o Azure Resource Health atual de cada recurso. Em conjunto, essas informações podem ajudá-lo a identificar rapidamente todos os recursos que estão enfrentando problemas. Os alertas ajudam você a detectar problemas no código e a configurar a infraestrutura. As superfícies do Azure Resource Health emitem problemas com a própria plataforma do Azure, que não são específicas para os aplicativos individuais.

![Captura de tela do painel do Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Para exibir o Azure Resource Health, selecione a caixa **Mostrar Azure Resource Health** acima da tabela. Esta coluna está oculta por padrão para ajudar a página carregar rapidamente.

![Captura de tela com o grafo de integridade do recurso adicionado](./media/resource-group-insights/0003-overview.png)

Por padrão, os recursos são agrupados por camada de aplicativo e tipo de recurso. **Camada de aplicativo** é uma categorização simples de tipos de recursos que existe apenas no contexto da página de visão geral de insights do grupo de recursos. Há tipos de recursos relacionados ao código do aplicativo, infraestrutura de computação, rede, armazenamento + bancos de dados. As ferramentas de gerenciamento recebem suas próprias camadas de aplicativos e todos os outros recursos são categorizados como pertencentes à camada de aplicativo **Outro**. Esse agrupamento pode ajudá-lo a ver rapidamente quais subsistemas do aplicativo estão íntegros e não íntegros.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticar problemas no grupo de recursos

A página de informações do grupo de recursos fornece várias outras ferramentas com escopo para ajudá-lo a diagnosticar problemas

   | Ferramenta | Descrição |
   | ---------------- |:-----|
   | [**Alertas**](../alerts/alerts-overview.md)      |  Exiba, crie e gerencie os alertas. |
   | [**Métrica**](../data-platform.md) | Visualize e explore os dados baseados em métricas.    |
   | [**Log de atividades**](../essentials/platform-logs-overview.md) | Eventos de nível de assinatura que ocorreram no Azure.  |
   | [**Mapa do aplicativo**](../app/app-map.md) | Navegue pela topologia do aplicativo distribuído para identificar gargalos de desempenho ou pontos de acesso. |

## <a name="failures-and-performance"></a>Falhas e desempenho

E se você notou que o aplicativo está executando lentamente ou os usuários relataram erros? É demorado pesquisar todos os recursos para isolar problemas.

As guias **Desempenho** e **Falhas** simplificam esse processo, reunindo exibições de diagnóstico de falhas e desempenho para muitos tipos de recursos comuns.

A maioria dos tipos de recursos abrirá uma galeria de modelos de pasta de trabalho do Azure Monitor. Cada pasta de trabalho que você cria pode ser personalizada, salva, compartilhada com a equipe e reutilizada no futuro para diagnosticar problemas semelhantes.

### <a name="investigate-failures"></a>Investigar falhas

Para testar a guia Falhas, selecione **Falhas** em **Investigar** no menu esquerdo.

A barra de menu do lado esquerdo muda depois que a seleção é feita, oferecendo novas opções.

![Captura de tela do painel de visão geral de Falha](./media/resource-group-insights/00004-failures.png)

Quando o Serviço de Aplicativo é escolhido, você recebe uma galeria dos modelos de pasta de trabalho do Azure Monitor.

![Captura de tela da galeria da pasta de trabalho do aplicativo](./media/resource-group-insights/0005-failure-insights-workbook.png)

A escolha do modelo para Insights de Falha abrirá a pasta de trabalho.

![Captura de tela do relatório de falhas](./media/resource-group-insights/0006-failure-visual.png)

É possível selecionar qualquer uma das linhas. A seleção é exibida em uma exibição de detalhes gráficos.

![Captura de tela dos detalhes da falha](./media/resource-group-insights/0007-failure-details.png)

As Pastas de Trabalho abstraem o difícil trabalho de criar relatórios e visualizações personalizados em um formato facilmente consumível. Enquanto alguns usuários podem querer apenas ajustar os parâmetros predefinidos, as pastas de trabalho são completamente personalizáveis.

Para ter uma noção de como essa pasta de trabalho funciona internamente, selecione **Editar** na barra superior.

![Captura de tela da opção de edição adicional](./media/resource-group-insights/0008-failure-edit.png)

Várias caixas **Editar** aparecem perto dos vários elementos da pasta de trabalho. Selecione a caixa **Editar** abaixo da tabela de operações.

![Captura de tela de caixas de edição](./media/resource-group-insights/0009-failure-edit-graph.png)

Isso revela a consulta de log subjacente que está impulsionando a visualização da tabela.

 ![Captura de tela da janela de consulta do log](./media/resource-group-insights/0010-failure-edit-query.png)

Você pode modificar a consulta diretamente. Ou, pode usá-la como referência e pegá-la emprestada ao criar sua própria pasta de trabalho parametrizada personalizada.

### <a name="investigate-performance"></a>Investigar o desempenho

O desempenho oferece sua própria galeria de pastas de trabalho. Para o Serviço de Aplicativo, a pasta de trabalho de Desempenho de Aplicativos predefinida oferece a seguinte exibição:

 ![Captura de tela da exibição de desempenho](./media/resource-group-insights/0011-performance.png)

Nesse caso, se você selecionar a edição, verá que esse conjunto de visualizações é ativado pelas métricas do Azure Monitor.

 ![Captura de tela da exibição de desempenho com Métricas do Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="enabling-access-to-alerts"></a>Habilitar o acesso para alertas

Para ver alertas no Azure Monitor para Grupos de Recursos, alguém com uma função de Proprietário ou Contribuinte para essa assinatura precisa abrir o Monitor do Azure para Grupos de Recursos para qualquer grupo de recursos da assinatura. Isso permitirá que qualquer pessoa com acesso de leitura veja alertas no Azure Monitor para Grupos de Recursos para todos os grupos de recursos da assinatura. Se você tiver uma função de Proprietário ou Colaborador, atualize esta página em alguns minutos.

O Monitor do Azure para grupos de recursos depende do sistema Gerenciamento de alertas do Monitor do Azure para recuperar o status de alerta. O Gerenciamento de Alertas não está configurado para todos os grupos de recursos e assinaturas por padrão, e só pode ser ativado por alguém com uma função de Proprietário ou Contribuinte. Pode ser ativado por:
* Abrindo o Azure Monitor para Grupos de Recursos para qualquer grupo de recursos na assinatura.
* Ou indo até a assinatura, clicando em **Provedores de Recursos** e, em seguida, clicando em **Register for Alerts.Management**.

## <a name="next-steps"></a>Próximas etapas

- [Pastas de trabalho do Azure Monitor](../visualize/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Alertas do Azure Monitor](../alerts/alerts-overview.md)
