---
title: Usar relatórios do Cloudyn no Azure | Microsoft Docs
description: Este artigo descreve a finalidade dos relatórios do Cloudyn incluídos no portal do Cloudyn para ajudar a usá-los efetivamente.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 484b39de52cef4e4772745eed38e8461a5594601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003658"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Relatórios disponíveis no portal Cloudyn

Este artigo descreve a finalidade dos relatórios Cloudyn que estão incluídos no portal do Cloudyn. Ele também descreve como você pode usar com eficiência os relatórios. A maioria dos relatórios é intuitiva e tem uma aparência uniforme. A maioria das ações que você pode fazer em um relatório, também poderá fazer em outros. Para obter uma visão geral sobre como usar os relatórios da Cloudyn, incluindo como personalizar e salvar ou agendar relatórios, consulte [Noções básicas sobre relatórios de custo](understanding-cost-reports.md).

O Azure Cost Management oferece funcionalidade semelhante ao Cloudyn. O Gerenciamento de Custos do Azure é uma solução de gerenciamento de custos nativa do Azure. Ele ajuda você a analisar custos, criar e gerenciar orçamentos, exportar dados e rever e agir de acordo com recomendações de otimização para economizar dinheiro. Para obter mais informações, consulte [gerenciamento de custos do Azure](overview-cost-mgt.md).

## <a name="report-types"></a>Tipos de relatório

Existem três tipos de relatórios Cloudyn:

- Relatórios ao longo do tempo. Por exemplo, o relatório Custo ao Longo do Tempo. Os relatórios ao longo do tempo mostram uma série de tempo dos dados ao longo de um intervalo selecionado com uma resolução predefinida e mostram uma resolução semanal para os últimos dois meses. Você pode usar o agrupamento e a filtragem para ampliar e ver vários pontos de dados.
  - Os relatórios ao longo do tempo podem ajudá-lo a exibir as tendências e detectar picos ou anomalias.
- Relatórios de análise. Por exemplo, o relatório de Análise de Custo. Esses relatórios mostram dados agregados ao longo de um período que você define e permite o agrupamento e a filtragem dos dados.
  - Os relatórios de análise podem ajudá-lo a exibir os picos e determinar as causas raiz de anomalias e mostrar uma análise granular dos seus dados.
- Relatórios tabulares. Você pode exibir qualquer relatório como uma tabela, mas alguns relatórios são exibidos apenas como uma tabela. Esses relatórios fornecem listas detalhadas de itens.
  - As recomendações são relatórios tabulares, não há visualizações para recomendações. No entanto, você pode visualizar os resultados de recomendações. Por exemplo, a economia ao longo do tempo.
  - Os relatórios tabulares são úteis como listas de ações ou para exportação de dados para processamento adicional. Por exemplo, um relatório de estorno.

Os relatórios de custos mostram os custos _reais_ ou _amortizados_.

Os relatórios de custo real exibem os pagamentos feitos durante o período de tempo selecionado. Por exemplo, todos os valores únicos, como compras de instância reservada (RI), são mostrados em relatórios de custo real como picos de custo.

Os relatórios de custos amortizados abrangem tarifas únicas ao longo de um período ao qual eles se aplicam. Por exemplo, as tarifas únicas para compras de RI são distribuídas durante o prazo de reserva e não são mostradas como pico. O modo de exibição amortizado é a única maneira de ver as tendências reais e fazer projeções de custo.

Em alguns casos, a amortização é apresentada como um relatório separado. Os exemplos incluem os relatórios de análise de custos e de análise de custo amortizada. Em outros casos, a amortização é uma política de relatório, como os relatórios de alocação de custos e de análise de custos.

Você pode agendar qualquer relatório para entrega periódica. Os relatórios de custos permitem a configuração de um limite que são úteis para alertas.

## <a name="cost-analysis-vs-cost-allocation"></a>Análise de custos versus alocação de custos

Os relatórios de _análise de custo_ exibem dados de cobrança de seus provedores de nuvem. Usando os relatórios, você pode agrupar e analisar vários segmentos de dados especificados no arquivo de cobrança. Os relatórios permitem a navegação detalhada nos custos por todos os dados brutos de cobrança dos provedores de nuvem.

Alguns relatórios de _análise de custos_ não agrupam os custos por marcas de recurso. E, as informações de cobrança baseadas em marcas só aparecem em relatórios depois de alocar os custos com a criação de um modelo de custo usando [Alocação de custos 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Os relatórios de _alocação de custos_ estão disponíveis depois que você cria um modelo de custo usando a [Alocação de Custos 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). O Cloudyn processa os dados de custo e faturamento e faz a _correspondência_ dos dados com os dados de uso e tag de suas contas na nuvem. Para corresponder aos dados, o Cloudyn requer acesso aos seus dados de uso. Se tiver contas sem credenciais, elas serão rotuladas como _recursos não categorizados_.

## <a name="dashboards"></a>Painéis

Os painéis no Cloudyn fornecem uma visão geral de relatórios. Painéis são compostos de widgets e cada widget é essencialmente uma miniatura do relatório. Quando você [personalizar relatórios](understanding-cost-reports.md#save-and-schedule-reports), salvá-los em Meus relatórios e elas são adicionadas ao painel. Para obter mais informações sobre painéis, consulte [Exibir as principais métricas de custo com painéis](dashboards.md).

## <a name="budget-information-in-reports"></a>Informações de orçamento em relatórios

Muitos relatórios Cloudyn mostram informações de orçamento depois de ter criado um manualmente. Assim, os relatórios não mostrarão as informações de orçamento até você criar um orçamento. Para obter mais informações, consulte [Configurações de gerenciamento de orçamento](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Relatórios e recursos de relatórios

O Cloudyn inclui os seguintes relatórios e recursos de relatório.

### <a name="cost-navigator-report"></a>Relatório do Navegador de Custo

O relatório do Navegador de Custo é uma maneira rápida de exibir o consumo de cobrança usando uma exibição de painel. Ele tem um subconjunto de filtros e exibições básicas para mostrar imediatamente uma exibição resumida dos custos da organização. Os custos são mostrados por data. Como o relatório pretende ser um modo de exibição inicial de seus custos, ele não é tão flexível ou abrangente quanto muitos outros relatórios ou painéis personalizados que você cria por conta própria.

Por padrão, as principais exibições do relatório mostram:

- Custo ao longo do tempo mostrando uma exibição de gráfico de barras de uma semana de trabalho. Você pode alterar o **Intervalo de Datas** para alterar o gráfico de barras do intervalo de datas.
- Gastos por serviço, usando um gráfico de pizza.
- Categorização de recurso por marcas, usando um gráfico de pizza.
- Gastos por entidades de custo, usando um gráfico de pizza.
- Custo total por data em uma exibição de lista.

### <a name="cost-analysis-report"></a>Relatório de análise de custo

O relatório de análise de custo é um cálculo de recuperação e estorno, com base em sua política. Ele agrega o seu consumo de nuvem durante um período de tempo selecionado, após ter aplicado todas as regras de alocação ao seu custo. Por exemplo, ele calcula os custos por marcas, reatribui os custos dos recursos sem marcas e, opcionalmente, aloca a utilização das instâncias reservadas.

As políticas definidas em [Alocação de custos 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são usados no relatório de análise de custo e os resultados são, em seguida, combinados com as informações dos dados brutos de seu fornecedor de nuvem.

Como esse relatório é calculado? O serviço do Cloudyn garante a alocação mantenha a integridade de cada conta associada aplicando _afinidade de conta_. A afinidade faz com que uma conta que não usa um serviço específico não tenha custos desse serviço alocados a ela. Os custos acumulados na conta permanecem nessa conta e não são calculados pelas políticas de alocação. Por exemplo, você pode ter cinco contas associadas. Se apenas três delas usam serviços de armazenamento, o custo dos serviços de armazenamento só é alocado entre marcas nas três contas.

Use o relatório de análise de custo para:

- Calcular a recuperação/estorno da sua organização
- Categorizar todos os seus custos
- Ver uma exibição agregada da sua implantação completa em um período de tempo específico.
- Exibir os custos por categorias de marca com base em políticas criadas no modelo de custo.

Para usar o relatório de análise de custo:

1. Selecione um intervalo de datas.
2. Adicione marcas conforme a necessidade.
3. Adicione grupos.
4. Escolha um modelo de custo criado anteriormente.

### <a name="cost-over-time-report"></a>Relatório de Custo ao Longo do Tempo

O relatório de Custo ao Longo do Tempo exibe os resultados da alocação de custos como uma série temporal. Ele permite que você observe as tendências e detecte irregularidades em sua implantação. Essencialmente, ele mostra os custos distribuídos em um período definido. O relatório inclui suas principais causas de custo, incluindo custos contínuos e tarifas avulsas reservadas gastos durante o período de tempo definido. As políticas definidas na [Alocação de custos 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) podem ser usadas neste relatório.

Use o relatório ao longo do tempo para:

- Ver as alterações ao longo do tempo e quais influencias mudam de um dia (ou intervalo de datas) para outro.
- Analisar os custos ao longo do tempo de uma instância específica.
- Entender por que houve um aumento de custo em uma instância específica.

Para usar o relatório de custo ao longo do tempo:

1. Selecione um intervalo de datas.
2. Adicione marcas conforme a necessidade.
3. Adicione grupos.
4. Escolha um modelo de custo criado anteriormente.
5. Selecione os custos reais ou custos amortizados.
6. Escolha se deseja aplicar regras de alocação para visualizar a exibição de dados de cobrança brutos ou para exibição de custo recalculado.

### <a name="actual-cost-analysis-report"></a>Relatório Análise de Custo Real

O relatório de análise de custo real mostra os custos de provedor sem modificações. Ele mostra suas principais causas de custo, incluindo custos contínuos e tarifas avulsas.

Você pode usar o relatório para exibir as informações de custo para suas assinaturas. No relatório, as assinaturas do Azure são mostradas como **nome da conta** e **número da conta**. As **contas vinculadas** mostram as assinaturas do AWS. Para exibir os custos por assinatura, uma divisão para cada conta, em **Grupos**, selecione o tipo de assinatura que você tem.

Use o relatório de análise de custo real para:

- Analisar e monitorar os custos brutos do provedor gastos durante um período especificado.
- Agendar um limite de alerta.
- Analise os custos sem modificações incorridos por suas contas e entidades.

### <a name="actual-cost-over-time-report"></a>Relatório de custo real ao longo do tempo

O relatório de custo real ao longo do tempo é um relatório de análise de custo padrão que distribui os custos por um tempo definido. O relatório exibe os gastos ao longo do tempo para que você possa observar as tendências e detectar problemas nos gastos. Esse relatório mostra suas principais causas de custo, incluindo custos contínuos e tarifas avulsas reservadas gastos durante o período de tempo definido.

Use o relatório de custo real ao longo do tempo para:

- Consultar as tendências de custo ao longo do tempo.
- Localizar irregularidades nos custos.
- Localizar todas as questões relacionadas ao custo de provedores de nuvem.

### <a name="amortized-cost-reports"></a>Relatórios de custos amortizados

Esse conjunto de relatórios de custos amortizados mostram tarifas de serviço sem base no uso ou custos de pagamento único e distribui uniformemente os custos ao longo de sua vida útil. Por exemplo, as tarifas avulsas podem incluir:

- Taxas de suporte anual
- Taxas anuais de componente de segurança
- Taxas de compra de instâncias reservadas
- Alguns itens do Azure Marketplace

No arquivo de cobrança, as tarifas avulsas são caracterizadas quando as datas de início e término do consumo do serviço (carimbo de data/hora) têm valores iguais. O serviço Cloudyn reconhece as tarifas como episódios únicos que estão amortizados. Não são amortizados outros serviços baseados em consumo com custos de uso sob demanda.

Os relatórios de custos amortizados incluem:

- Análise de custo amortizada
- Custo amortizado ao longo do tempo

### <a name="cost-analysis-report"></a>Relatório de análise de custo

O relatório de análise de custo fornece informações sobre o consumo e gastos na nuvem durante um período de tempo selecionado. As políticas definidas na [Alocação de Custos360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são usadas no relatório de análise de custo.

Como o Cloudyn calcula esse relatório?

O Cloudyn faz com que a alocação mantenha a integridade de cada conta associada aplicando _afinidade de conta_. A afinidade faz com que uma conta que não usa um serviço específico também não tenha custos desse serviço alocados a ela. Os custos acumulados na conta permanecem nessa conta e não são calculados pelas políticas de alocação. Por exemplo, você pode ter cinco contas associadas. Se apenas três delas usam serviços de armazenamento, o custo dos serviços de armazenamento só é alocado entre marcas nas três contas.

Use o relatório de análise de custo para:

- Ver uma exibição agregada da sua implantação completa em um período de tempo específico.
- Exibir os custos por categorias de marca com base em políticas criadas no modelo de custo.

### <a name="cost-over-time-report"></a>Relatório de Custo ao Longo do Tempo

O relatório de custo ao longo do tempo exibe gastos ao longo do tempo para que você possa observar as tendências e detectar irregularidades em sua implantação. Essencialmente, ele mostra os custos distribuídos em um período definido. O relatório inclui suas principais causas de custo, incluindo custos contínuos e tarifas avulsas reservadas gastos durante o período de tempo definido. As políticas definidas na [Alocação de custos 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) podem ser usadas neste relatório.

Use o relatório ao longo do tempo para:

- Ver as alterações ao longo do tempo e quais influencias mudam de um dia (ou intervalo de datas) para outro.
- Analisar os custos ao longo do tempo de uma instância específica.
- Entender por que houve um aumento de custo em uma instância específica.

### <a name="custom-charges-report"></a>Relatório de encargos personalizados

Os usuários do Enterprise e CSP muitas vezes acabam fornecendo serviços adicionados a seus clientes internos ou externos, além de seu próprio consumo de recursos de nuvem. Você define encargos personalizados para serviços adicionais ou descontos que são adicionados aos relatórios de cobrança ou estorno do cliente como itens de linha personalizados.

Os encargos de serviço personalizados refletem os serviços que normalmente não são mostrados em uma fatura. Os encargos personalizados criados por você são mostrados em relatórios de custo.

*Encargos personalizados não são preços personalizados*. A lista de encargos personalizados não mostra as taxas diferentes que você pode cobrar. Por exemplo, os encargos de cobrança do AWS são exibidos exatamente como são cobrados.

Para criar uma cobrança personalizada:

1. Em **Encargos Personalizados**, clique em **Adicionar Novo**. A caixa de diálogo _Adicionar Novo Encargo Personalizado_ é exibida.
2. Em **Nome do Provedor**, insira o nome do provedor.
3. Em **Nome do Serviço**, insira o tipo de serviço.
4. Em **Descrição**, adicione uma descrição para o encargo personalizado.
5. Em **Tipo**, insira a **Porcentagem** selecionada e, em seguida, no menu suspenso Serviços, selecione os serviços para incluir como encargos personalizados nos relatórios de custo.
6. Em **Pagamento**, selecione se o encargo é uma taxa única ou recorrente. Se o encargo for uma taxa recorrente, selecione Amortizado se você quiser que o encargo seja amortizado e selecione o número de meses.
7. Em **Datas**, se uma taxa única é selecionada, em **Data de Efetivação**, insira a data em que o encargo é pago. Se Taxa Recorrente estiver selecionada, insira o intervalo de datas, incluindo a data de início e a data de término para o encargo.
8. Em **Árvore de Entidades**, selecione as entidades em que você deseja aplicar o encargo e selecione **Ativado**.

_Quando os encargos são atribuídos a uma entidade, os usuários não podem alterá-los. Os encargos que são adicionados por um administrador a uma entidade pai são somente leitura._

Para exibir encargos personalizados:

Os encargos personalizados são mostrados em relatórios de custo. Por exemplo, abra o relatório de análise de custo real, em seguida, em **Filtros Estendidos**, selecione **Autônomo**. Em seguida, filtre para mostrar **Encargos Personalizados**.

### <a name="cost-allocation-360"></a>Alocação de custos 360

Você pode usar Alocação de custos 360 para criar modelos personalizados de alocação de custos para atribuir os custos a recursos de nuvem consumidos. Muitos relatórios mostram informações de modelos personalizados de custo que você criou com modelos personalizados de custo. E alguns relatórios mostram apenas as informações depois de você ter criado um modelo personalizado de custos com alocação de custos.

Para obter mais informações sobre a criação de modelos de custo personalizados, consulte [Tutorial: Gerenciar custos usando Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Relatório de Custos versus  Orçamento ao Longo do Tempo

O Relatório de Custos versus Orçamento ao Longo do Tempo permite que você compare as principais causas de custo em relação ao seu orçamento. O orçamento atribuído aparece no relatório para que você possa exibir o consumo do orçamento (sobre/sob/igual) ao longo do tempo. Usando Exibir/Ocultar Campos na parte superior do relatório, você pode selecionar para visualizar o custo, orçamento, custo acumulado e orçamento total.

### <a name="current-month-projected-cost-report"></a>Relatório de custo projetado do mês atual

O relatório de custo projetado do mês atual que fornece informações sobre o resumo atualizado de custo acumulado no mês. Este relatório exibe os custos desde o início do mês, do mês anterior, e o custo total projetado para o mês atual. O custo projetado do mês atual é calculado como a soma do custo mensal atualizado e uma projeção com base no custo monitorado nos últimos 30 dias.

Use o relatório de custo projetado do mês atual para:

- Projetar custos mensais por serviço
- Projetar custos mensais por conta

### <a name="annual-projected-cost-report"></a>Relatório de custo projetado anual

O relatório de custo projetado anual permite exibir custos projetados anuais, com base nas tendências de gastos anteriores. Ele mostra os próximos 12 meses de custos projetados gerais. As projeções são feitas usando uma função de tendência extrapolada nos próximos 12 meses, com base nos custos associados com os últimos 30 dias de uso.

### <a name="budget-management-settings"></a>Configurações de gerenciamento de orçamento

O gerenciamento de orçamento permite que você defina um orçamento para o ano fiscal.

Para adicionar um orçamento a uma entidade:

1. Na página de Gerenciamento do Orçamento, em **Entidades**, selecione a entidade onde você quer criar o orçamento.
2. No ano do orçamento, selecione o ano onde você quer criar o orçamento.
3. Em cada mês, defina seu orçamento e clique em **Salvar**.

Para importar um arquivo para o orçamento anual:

1. Em **Ações**, selecione **Exportar** para realizar o download de um modelo de CSV vazio para usar como base para o seu orçamento.
2. Preencha o arquivo CSV com as suas entradas de orçamento e salve localmente.
3. Em **Ações**, selecione **Importar**.
4. Selecione seu arquivo salvo e clique em **OK**.

Para exportar o orçamento concluído como arquivo CSV em **Ações**, selecione **Exportar** para realizar o download do arquivo.

Ao concluir, seu orçamento será exibido nos relatórios de análise de custo e no relatório de orçamento versus o relatório de Custo ao Longo do Tempo Você também pode programar relatórios com base nos limites de orçamento.

### <a name="azure-resource-explorer-report"></a>Relatório do Azure Resource Explorer

O relatório do Azure Resource Explorer mostra uma lista em massa de todos os recursos do Azure disponíveis no Cloudyn. Para usar efetivamente o relatório, suas contas do Azure devem ter as métricas estendidas habilitadas. Métricas estendidas fornecem acesso Cloudyn às suas VMs do Azure. Para ter mais informações, consulte [Adicionar métricas estendidas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Relatório de recursos do Azure ao longo do tempo

O relatório de recursos do Azure ao longo do tempo mostra uma análise detalhada de todos os recursos em execução em um período específico. Para usar efetivamente o relatório, suas contas do Azure devem ter as métricas estendidas habilitadas. Métricas estendidas fornecem acesso Cloudyn às suas VMs do Azure. Para ter mais informações, consulte [Adicionar métricas estendidas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Relatório do Instance Explorer

O relatório do Instance Explorer é usado para exibir várias métricas para ativos de suas máquinas virtuais. Você pode analisar em instâncias específicas para exibir informações como:
- Intervalos de instâncias em execução
- Ciclo de vida no período selecionado
- Utilização da CPU
- Entrada de rede
- Tráfego de saída
- Discos ativos

O relatório do Instance Explorer coleta todos os intervalos em execução dentro do intervalo de datas definido e agrega os dados adequadamente. Para exibir cada um dos intervalos em execução durante o intervalo de datas, expanda a instância. O custo de cada instância é calculado para o intervalo de datas selecionado com base nos preços de lista do AWS e do Azure. Nenhum desconto é aplicável. Você pode adicionar outros campos ao relatório usando Mostrar/Ocultar Campos.

Use o relatório do Instance Explorer para:

- Calcular o custo estimado por máquina.
- Criar uma lista completa, incluindo as horas de execução agregadas de todas as máquinas que estavam ativas durante um intervalo de tempo.
- Criar uma lista por provedor de serviços de nuvem ou conta.
- Exibir as máquinas criadas ou encerradas durante um intervalo de tempo.
- Exibir todas as máquinas atualmente paradas.
- Exibir as marcas de cada máquina.

### <a name="instances-over-time-report"></a>Relatório de instâncias ao longo do tempo

Usando o relatório de instâncias ao longo do tempo, é possível ver o número máximo de máquinas que estavam ativas durante o intervalo de tempo selecionado. Se a resolução for definida por semana ou mês, os resultados serão o número máximo de computadores ativos em qualquer dia determinado durante esse mês. Selecione um intervalo de datas para selecionar os filtros que deseja exibir no relatório.

### <a name="instance-utilization-over-time-report"></a>Relatório de utilização de instâncias ao longo do tempo

Este relatório mostra uma análise do uso de CPU ou memória ao longo do tempo para todas as suas instâncias.

### <a name="compute-power-cost-over-time-report"></a>Relatório de custo da capacidade de computação ao longo do tempo

O relatório da capacidade de computação ao longo do tempo fornece uma análise de capacidade de computação em um intervalo de datas especificado. Embora outros relatórios mostrem o número de máquinas em execução ou o tempo de execução, esse relatório mostra as horas principais, horas de unidade de computação ou horas de GB de RAM.

Use o relatório para:

- Verificar a capacidade de computação em um intervalo de datas especificado.
- Exibir tempos de computação com base em modelos de alocação de custo.

Este relatório está vinculado a suas políticas de [Alocação de custo 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) de modo que os resultados são mostrados com base na marcação e nas políticas definidas de sua política de custo selecionada. Quando você não tiver uma política criada, os resultados não serão mostrados.

### <a name="compute-power-average-cost-over-time-report"></a>Relatório de custo médio da capacidade de computação ao longo do tempo

Você pode usar o relatório de custo médio da capacidade de computação ao longo do tempo para exibir mais do que apenas o custo de cada máquina em execução. O relatório mostra o custo médio por hora de instância, horas de núcleo, hora de unidade de computação e hora de GB de RAM. O relatório fornece informações sobre a eficiência da sua implantação.

Este relatório está vinculado a suas políticas de [Alocação de Custo 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) de modo que os resultados são exibidos com base na marcação e nas políticas definidas de sua política de custo selecionada. Quando você não tiver uma política criada, os resultados não serão mostrados.

### <a name="s3-cost-over-time-report"></a>Relatório de Custo ao Longo do Tempo do S3

O Relatório de Custo ao Longo do Tempo do S3 fornece uma análise de custos do Amazon Simple Storage Service (S3) por bucket ao longo do tempo para um intervalo de tempo especificado. O relatório ajuda a localizar os buckets que são os maiores responsáveis pelo aumento do custo principal e ele mostra as tendências em seu uso e os gastos no S3.

### <a name="s3-distribution-of-cost-report"></a>Relatório de distribuição de custo do S3

Use o relatório para analisar seu custo do S3 para o último mês por bucket e classe de armazenamento. Você pode usar o modo de exibição do gráfico de pizza para definir o limite de visibilidade. Ou você pode usar o modo de exibição de tabela para ver os subtotais.

### <a name="s3-bucket-properties-report"></a>Relatório de propriedades de bucket do S3

Use o relatório para exibir as propriedades de bucket do S3. Você pode usar o modo de exibição do gráfico de pizza para definir o limite de visibilidade. Ou você pode usar o modo de exibição de tabela para ver os subtotais.

### <a name="rds-instances-over-time-report"></a>Relatório de instâncias ao longo do tempo do RDS

Use o relatório para exibir uma análise de todas as instâncias do Amazon Relational Database Service (RDS) em execução durante o período especificado.

### <a name="rds-active-instances-report"></a>Relatório de instâncias ativas do RDS

Use o relatório para analisar instâncias ativas do RDS. No relatório, expanda o item de linha para exibir informações adicionais.

### <a name="azure-reserved-instances-report"></a>Relatório de instâncias reservadas do Azure

O relatório de instâncias reservadas do Azure fornece uma exibição única de todas as instâncias reservadas do Azure. Este relatório exibe cada compra como seu próprio item de linha. O relatório também mostra detalhes sobre essa compra, como a conta que a adquiriu, o tipo de compra e o tipo de instância, dias restantes e assim por diante. Você pode mostrar ou ocultar os dados de relatório usando Mostrar/Ocultar Campos.

Use o relatório de instâncias reservadas do Azure para exibir:

- Uma lista de todas as reservas por data de compra.
- Tempo restante até que a instância reservada expire.
- Valores únicos.
- A conta que comprou instâncias reservadas e quando.

### <a name="aws-reserved-instances-report"></a>Relatório de instâncias reservadas do AWS

O relatório de instâncias reservadas do AWS fornece uma exibição única de todas as instâncias reservadas do AWS. O relatório mostra cada compra como seu próprio item de linha e os detalhes sobre essa compra, como a conta que a adquiriu, o tipo de compra e o tipo de instância, dias restantes e assim por diante. Você pode mostrar ou ocultar os dados de relatório usando Mostrar/Ocultar Campos.

Use o relatório de instâncias reservadas do AWS para exibir:

- Uma lista de todas as reservas por data de compra.
- Tempo restante até que a instância reservada expire.
- Valores únicos.
- ID de compra original (ID de reserva).
- A conta que comprou instâncias reservadas e quando.

### <a name="ec2-ri-buying-recommendations-report"></a>Relatório de recomendações de compra de instância reservada do EC2

A base do consumo de recursos de nuvem é o modelo sob demanda, em que os recursos incorrem em custos somente quando são usados. Não há compromisso antecipado, você paga apenas pelo que usar e quando usar.

O AWS oferece um modelo alternativo de preços para seus serviços de Elastic Compute Cloud (EC2): a instância reservada (RI). Este modelo de preços garante aos usuários a capacidade sempre que for necessário para a duração da RI. A RI oferece descontos significativos em relação ao preço sob demanda. Em troca, os usuários assumem um compromisso antecipado pelo uso de uma instância virtual. O compromisso está associado a uma família específica, tamanho, zona de disponibilidade (AZ) e sistema operacional, durante o período do compromisso (um ou três anos). A RI permite que o AWS planeje com eficiência a capacidade futura, além de obter o compromisso do cliente de usar seus serviços.

Três opções de pagamento para RIs, que são todos antecipados:

- Soma em massa no dia 0, oferecendo o desconto mais alto
- Sem antecipação, em que o custo de RI é pago em prestações mensais ao longo da duração da RI, oferecendo o desconto mais baixo
- Pagamentos parciais antecipados, no qual ¼ a ½ do preço é pago com antecedência e o restante em prestações mensais, com uma taxa de desconto menor, mas próxima à taxa antecipada total

O Cloudyn avalia o tempo de atividade de cada máquina nos últimos 30 dias. A Cloudyn recomenda a compra de RIs quando for mais econômico executar a máquina com um RI no nível atual de uptime.

O relatório mostra a justificativa para essa recomendações para você poupar dinheiro ao longo do ano. As recomendações sugerem substituir as instâncias sob demanda com as RIs. Você pode comprar RIs diretamente do relatório.

Cada guia é aberta como um relatório completo. Seções importantes nas guias:

- **Impacto de compra de RI do EC2** - esta seção fornece uma simulação da diferença entre as instâncias reservadas versus sob demanda. Clique em **Ampliar** para ver o relatório completo de impacto de compra de RI do EC2 com os filtros já definidos para a sua recomendação. Este relatório mostra o impacto de compra de todas as possíveis compras de RI. Você pode ajustar o tempo de atividade médio esperado para ver o potencial de economia ao adquirir instâncias reservadas do EC2.

- **Saving Analysis** - Esta seção fornece as economias potenciais obtidas e o mês em que as economias são atualizadas ao seguir as recomendações da Cloudyn. A economia real e a porcentagem salva estão realçadas em vermelho.

- **Comparação do tipo EC2 RI**: esta seção enfatiza os destaques do ROI da implantação recomendada da Cloudyn, incluindo todas as opções relevantes. Os resultados desse relatório supõem que a máquina esteja em execução em 100% do tempo de atividade. Clique em **Ampliar** para abrir o relatório detalhado.

- **Instâncias ao longo do tempo** - esta seção exibe uma análise de todas as instâncias associadas com a recomendação, sob demanda, instâncias reservadas e à vista. Clique em **Ampliar** para abrir o relatório detalhado.
- **Pontos de equilíbrio** - esta seção exibe uma tabela de todas as possíveis implantações recomendadas e o ROI e o mês quando ocorre o ROI. Clique em **Ampliar** para abrir o relatório detalhado.

### <a name="ec2-reservations-over-time-report"></a>Relatório de reservas ao longo do tempo do EC2

O relatório de reservas ao longo do tempo do EC2 rastreia o status de seu uso de suas RIs compradas do EC2. Você pode definir a resolução do relatório, a hora, dia ou semana.

Use o relatório para:

- Exibir reservas compradas que são usadas e não usadas.
- Fazer uma busca de resolução por hora para ver o uso da RI por hora.

### <a name="savings-over-time-report"></a>Relatório de Economia ao Longo do Tempo

Use o relatório de economia ao longo do tempo para exibir a economia obtida usando instâncias reservadas, bem como instâncias à vista. O relatório mostra o ROI obtido ao longo do tempo resultante de compras de RI.

Para exibir a economia de RIs, agrupe os resultados por **Modelo de Preço** e selecione **Reserva**. Para exibir economias de RI obtidas com uma conta ou tipo de instância específica, adicione o agrupamento relevante e filtre para o tipo de conta ou instância.

Para ver a economia do uso de instância à vista, filtre o **Modelo de Preço** para **À vista**. O filtro padrão para este relatório é RI e instâncias à vista.

### <a name="rds-ri-buying-recommendations-report"></a>Relatório de recomendações de compra de instância reservada do RDS

O relatório de recomendações de compra de instância reservada do RDS recomenda quando usar o RIs do RDS em vez de instâncias sob demanda.

Cada guia é aberta como um relatório completo. Seções importantes nas guias:

- **Impacto de compra de RI do RDS** - esta seção fornece uma simulação da diferença entre as instâncias reservadas versus sob demanda. Clique em **Ampliar** para ver o relatório completo de impacto de compra de RI do RDS com os filtros já definidos para a sua recomendação. Este relatório permite ver o impacto de compra de todas as possíveis compras de RI.  Você pode ajustar o tempo de atividade médio esperado e ver o potencial de economia por RIs de compra.
- **Saving Analysis** - Esta seção fornece as economias potenciais obtidas e o mês em que as economias são atualizadas ao seguir as recomendações da Cloudyn. A economia real e a porcentagem salva estão realçadas em vermelho.

- **Comparação de tipo de RI do RDS** - esta seção enfatiza os destaques do ROI da implantação recomendada, incluindo todas as opções relevantes. Os resultados desse relatório supõem que a máquina esteja em execução em 100% do tempo de atividade. Clique em **Ampliar** para abrir o relatório detalhado para a máquina selecionada.
- **Instâncias ao longo do tempo** - esta seção exibe uma análise de todas as instâncias associadas com a recomendação, sob demanda, instâncias reservadas e à vista. Clique em **Ampliar** para abrir o relatório detalhado.

- **Pontos de equilíbrio** - esta seção exibe uma tabela de todas as possíveis implantações recomendadas e o ROI e o mês quando ocorre o ROI. Clique em **Ampliar** para abrir o relatório detalhado.

### <a name="rds-reservations-over-time-report"></a>Relatório de reservas ao longo do tempo do RDS

Use o relatório de reservas ao longo do tempo do RDS para exibir uma análise das suas reservas utilizadas e não utilizadas durante o período especificado.

### <a name="reserved-instance-purchase-impact-report"></a>Relatório de impacto de compra de instância reservada

O relatório de impacto de compra de RI do EC2 permite que você simule o custo da instância reservada em relação ao custo sob demanda ao longo do tempo. Ele pode ajudá-lo a tomar decisões de compra melhores. Ajuste os filtros, como o tempo de execução médio, prazo, plataforma e outras opções para tomar decisões informadas ao considerar as compras de RI.

### <a name="cost-effective-sizing-recommendations-report"></a>Relatório de recomendações de dimensionamento econômico

O relatório de recomendações de dimensionamento econômico fornece resultados para AWS e Azure. Para usuários do AWS, suas compras de RI são levadas em consideração e os resultados não incluem as máquinas em execução como da RI. Este relatório fornece uma lista de instâncias subutilizadas que são candidatas para diminuir de tamanho. As recomendações baseiam-se em seus dados de uso e o desempenho dos últimos 30 dias. Em cada recomendação, há uma lista de candidatos para diminuir o tamanho e a justificativa para redução, além de um link para exibir os detalhes completos e as métricas de desempenho da instância. E quando as recomendações relevantes aconselham a alterar para tipos de instância de gerações mais recentes.

Você não pode baixar a lista de IDs de instância que são recomendadas para diminuir o tamanho desse relatório. Para baixar as IDs de instância, use o Relatório de recomendações de todos os dimensionamentos.

Considere o exemplo de redução a seguir:

Você tem seis instâncias m3.xlarge em execução. A análise da Cloudyn mostra que cinco deles têm baixa utilização da CPU. Considere a possibilidade de fazer uma diminuição de tamanho delas.

No Impacto de custo, esse dado é calculado. Neste exemplo, ao expandir o item de linha, você pode ver que o preço atual para uma instância m3.xlarge (Linux/Unix) custa US$ 0,266 por hora e uma instância m3.large (Linux/Unix) custa US$ 0,133 por hora. Portanto, o custo anual é US$ 11.651 para cinco instâncias m3.xlarge em execução com 100% de utilização. O custo anual é US$ 5.825 para cinco instâncias m3.large em execução com 100% de utilização. O potencial de economia é US$ 5.825.

Para exibir as justificativas de dimensionamento mais econômicas, clique em + para expandir o item de linha. Em **Detalhes**:

- A seção **Justificativa de Recomendação** exibe a implantação atual e o número de instâncias recomendado para diminuir o tamanho.
- A seção **Impacto de custo** exibe o cálculo usado para determinar o potencial de economia.
- A seção **Potential Annual Savings** (Economia potencial anual) exibe a possível economia anual ao reduzir o tamanho por recomendação da Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Relatório de recomendações de todos os dimensionamentos

Este relatório fornece uma lista de instâncias subutilizadas que são candidatas para diminuir de tamanho. As recomendações baseiam-se em seus dados de uso e o desempenho dos últimos 30 dias. Em cada recomendação, você poderá exibir os detalhes completos e as métricas de desempenho da instância.

Se você tiver adquirido instâncias reservadas do AWS, este relatório contém os resultados de todas as instâncias em execução, incluindo as instâncias em execução como RIs.

Use o relatório de recomendações de todos os dimensionamentos para:

- Ver uma lista de todas as instâncias que são candidatas para diminuir o tamanho.
- Exportar uma lista de relatório que contém as IDs e os nomes de instâncias.

Para exibir detalhes da recomendação para uma instância específica, clique em **+** para expandir os detalhes. A seção Detalhes da recomendação fornece uma visão geral da recomendação.

A seção **Marcas** fornece a lista de chaves de marca e valores para a instância selecionada. Use Marcas no painel esquerdo para filtrar a seção.

A seção **Utilização da CPU** fornece a utilização da CPU para a instância no último mês, por dia.

Clique no gráfico para fazer uma análise e abrir o relatório de instância da CPU ao longo tempo para ver um detalhamento das instâncias.

- Use **Mostrar/ocultar campos** para adicionar ou remover campos: Carimbo de data/hora, Méd. de CPU, Mín. de CPU, Máx. de CPU.
- Use **Intervalo de Datas** para inserir uma data ou intervalo de datas e detalhar uma InstanceID específica.
- Use **Filtros Estendidos** para mostrar todos ou uma ID de instância específica
- Clique em **Ampliar** para abrir o relatório de utilização de CPU

Se a instância ainda não tiver sido monitorada por 30 dias, serão mostrados dados incompletos.

A seção **Utilização de Memória (GB)** fornece informações sobre a memória utilizada. Para usuários do AWS, as métricas de memória não ficam automaticamente disponíveis e precisam ser adicionadas por instância por meio do AWS. O AWS cobra para habilitar as métricas de memória para as instâncias do EC2.

A seção **Utilização de memória (%)** exibe a porcentagem de memória usada.

A seção **Tráfego de rede de entrada** exibe um instantâneo ao longo do tempo do tráfego de rede, média e máxima, para a instância selecionada. Passe o mouse sobre as linhas para ver a data e o tráfego máximo para esse horário. Clique em **Ampliar** para abrir o relatório de tráfego de entrada de rede.

A seção **Tráfego de rede de saída** exibe um instantâneo do tráfego de rede de saída para a instância selecionada. Passe o mouse sobre as linhas para ver a data e o tráfego máximo para esse horário. Clique em **Ampliar** para abrir o relatório de tráfego de saída de rede.

### <a name="instance-metrics-explorer-report"></a>Relatório da instância do Metrics Explorer

O relatório de instância do Metrics Explorer mostra as métricas de desempenho de nuvens por instância. Use o relatório para exibir instâncias que estão acima ou subutilizados com base nos limites de CPU, memória e métricas de rede.

Para exibir o desempenho de nuvens por instância:

1. Em **Intervalo de Datas**, selecione um intervalo de datas para o qual você deseja exibir o desempenho.
2. Em **Marcas**, selecione todas as marcas que você deseja exibir.
3. Em **Filtros**, selecione os filtros que você deseja exibir no relatório.
4. Em **Filtros Estendidos**, ajuste os limites do relatório para:
    - Média de CPU
    - CPU máxima
    - Memória média
    - Memória máxima
5. Em **Filtros Estendidos**, clique em **Mostrar** e selecione o tipo de instâncias para exibir.

Para exibir as métricas de uma instância específica ao longo do tempo:

- Vá para o relatório de instâncias do Metrics Explorer e clique em **+** para exibir os detalhes.

### <a name="rds-sizing-recommendations-report"></a>Relatório de recomendações de dimensionamento do RDS

O relatório de recomendações de dimensionamento do RDS fornece recomendações para otimizar o uso da nuvem. Ele fornece uma lista de instâncias subutilizadas que são candidatas para diminuir de tamanho. As recomendações do Cloudyn são baseadas nos dados de uso e desempenho dos últimos 30 dias. Você pode filtrar recomendações por Nome da conta, Região, Tipo de instância e Status.

### <a name="sizing-threshold-manager-report"></a>Relatório do gerenciador de limite de dimensionamento

As recomendações de dimensionamento internas da Cloudyn são calculadas usando um algoritmo complexo para fornecer sugestões precisas de dimensionamento. Você pode ajustar os limites para as recomendações de redução.

Para ajustar manualmente as recomendações de dimensionamento de limite:

1. No Gerenciador de limite de dimensionamento, ajuste os limites a seguir como desejar:
    - % de CPU Média
    - % de Utilização Máxima
    - % de Memória Média
    - % de Memória Máxima
3. Clique em **Aplicar** para salvar os detalhes.
4. As alterações serão aplicadas imediatamente para todas as suas recomendações.

Para restaurar os limites padrão:

- No Gerenciador de limite de dimensionamento, clique em **Restaurar Padrões**.

### <a name="compute-instance-types-report"></a>Relatório de tipo de instância de computação

Use o relatório de tipos de instância para:

- Exibir tipos de instância por Serviço, Família, Nome da API e Nome.
- Exibir detalhes como CPU, ECU, RAM e rede.

Você pode usar **Pesquisar** para localizar itens de linha específicos.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a usar relatórios, incluindo como personalizar ou salvar e agendar relatórios, consulte [Noções básicas sobre relatórios de custo](understanding-cost-reports.md).
- Saiba mais sobre os painéis incluídos no Cloudyn e sobre como criar seus próprios painéis personalizados. Consulte [Exibir as principais métricas de custo com painéis](dashboards.md).
