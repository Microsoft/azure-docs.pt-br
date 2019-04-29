---
title: Exibir principais métricas com painéis do Cloudyn no Azure | Microsoft Docs
description: Este artigo descreve como é possível exibir as principais métricas com painéis no Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7ea7cbf4526528eb481b6125b006d0c29d86deb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577411"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Exibir chave métricas com painéis de custo

Painéis no Cloudyn fornecem uma visão geral de relatórios. Painéis permitem exibir a métrica de custo de chave em uma única exibição. Eles também fornecem a tendência de negócios realça para ajudá-lo a tomar decisões de negócios importantes.

Painéis também são usados para criar exibições para pessoas com responsabilidades diferentes em sua organização, que pode incluir:

- Controlador financeiro
- Proprietário do aplicativo ou projeto
- Engenheiro de DevOps
- Executivos

Painéis são compostos de widgets e cada widget é essencialmente uma miniatura do relatório. Clique em um widget para abrir o relatório. Quando você personalizar relatórios, salvá-los em Meus relatórios e elas são adicionadas ao painel.

Versões do painel são diferentes para usuários de gerenciamento (MSP), Enterprise e Cloudyn Premium. As diferenças são determinadas pelos níveis de acesso de entidade. Para obter mais informações sobre níveis de acesso, consulte [níveis de acesso de entidade](tutorial-user-access.md#entity-access-levels).

Disponibilidade do painel depende do tipo de conta de provedor de serviço de nuvem que é usada ao exibir os painéis. O tipo de informação disponível e coletado pelo Cloudyn afeta relatórios em painéis. Por exemplo, se você não tiver uma conta AWS você não verá o painel do rastreador S3. Da mesma forma, se você não habilitar o acesso do Azure Resource Manager para Cloudyn, em seguida, você não verá todas as informações específicas do Azure em widgets do painel de otimizador.

Você pode usar qualquer um dos painéis pré-criados ou você pode criar seu próprio painel com relatórios personalizados. Se você não estiver familiarizado com os relatórios do Cloudyn, confira [Usar relatórios do Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Crie um painel personalizado

Para iniciar rapidamente com um painel personalizado, você pode duplicar uma existente para usar suas propriedades. Então você pode modificá-lo para atender às suas necessidades. No painel que você deseja copiar, clique em **Salvar como**. Você só pode duplicar os painéis personalizados — não é possível duplicar os painéis que estão incluídos com Cloudyn.

Para criar um painel personalizado:

1. Na home page, clique em **adicionar novo +**. A página de meu painel é exibida.  
    ![Minha página de painel na qual você adiciona novos relatórios](./media/dashboards/my-dashboard.png)
2. Clique em **Adicionar nova regra**. A caixa Adicionar relatório é exibida.
3. Selecione o relatório que você deseja adicionar ao widget de painel. O widget é adicionado ao painel.
4. Repita as etapas anteriores até que o painel seja concluído.
5. Para alterar o nome do painel, clique no nome do painel na home page do Dashboard e digite o novo nome.

## <a name="modify-a-custom-dashboard"></a>Modificar um painel personalizado

Como criar um painel personalizado, você não pode modificar os painéis incluídos com Cloudyn. Para modificar um relatório do painel personalizado:

1. No painel, localize o relatório que você deseja modificar e clique em **editar**. O relatório é exibido.
2. Faça as alterações que você deseja para o relatório e clique em **salvar**. O relatório é atualizado e exibe as alterações.

## <a name="share-a-custom-dashboard"></a>Compartilhar um painel personalizado

Você pode compartilhar um painel personalizado com outras pessoas _pública_ ou _minha entidade_. Quando você compartilha a Public, todos os usuários podem exibir o painel. Somente os usuários com acesso à entidade atual podem exibir o painel de controle quando você compartilha a minha entidade. As etapas para compartilhar um painel personalizado com público e minha entidade são semelhantes.

Para compartilhar um painel personalizado ao Público:

1. No painel de controle, clique em **configurações do Dashboard**. A caixa de configurações do painel é exibida.  
    ![configurações do painel para um painel personalizado](./media/dashboards/dashboard-options.png)
2. Na caixa de configurações do Dashboard, clique no símbolo de seta e, em seguida, clique em **público**. A caixa de diálogo de confirmação de painel público é exibida.
3. Clique em **Sim**. O painel de controle agora está disponível para outras pessoas.

## <a name="delete-a-custom-dashboard-report"></a>Excluir um relatório de painel personalizado

Você pode excluir um componente de relatório personalizado no painel. A exclusão do relatório do painel não exclui o relatório da lista de relatórios. Em vez disso, a exclusão do relatório remove apenas o painel.

Para excluir um componente de painel, o componente do painel, clique em **excluir**. Clicando em **excluir** imediatamente exclui o componente do painel.

## <a name="share-a-dashboard-enterprise"></a>Compartilhar um painel (Enterprise)

Você pode compartilhar painéis personalizados para todos os usuários em sua organização ou com os usuários da entidade atual. Compartilhar um dashboard pode conceder a outros usuários uma exibição rápida de alto nível do KPI. Quando você compartilha um painel, replica automaticamente o painel de controle para todos os seus Cloudyn entidades e os clientes. As alterações para o painel compartilhado são atualizadas automaticamente.

Para compartilhar um painel com todos os usuários, incluindo subentidades:

1. Na home page do painel, clique em **editar**.
2. Clique em **compartilhar** e, em seguida, selecione **público**.
3. A caixa de confirmação do painel público global é exibida.
4. Clique em **Sim** para definir o painel como um painel público global.

Para compartilhar um painel com todos os usuários de uma entidade atual:

1. Na página inicial do Dashboard, clique em **Editar**.
2. Clique em **Compartilhar** e selecione **Minha Entidade**.
3. Clique em **Sim** para definir o painel como um painel público.

## <a name="duplicate-a-custom-dashboard"></a>Duplicar um painel personalizado

Quando você cria um novo painel, você talvez queira usar propriedades semelhantes de um painel existente. Você pode duplicar o painel para criar um novo.

Você só pode duplicar os painéis personalizados. Não é possível duplicar painéis padrão.

Para duplicar o painel (clone) um personalizado:

1. No painel de controle que você deseja duplicar, clique em **Salvar como**. Um novo painel é aberta com o mesmo nome e um número.
2. Renomeie o painel duplicado e modificá-lo como desejar.

-Ou-

1. No painel configurações, clique em **Salvar como** na linha do painel que você deseja duplicar.
2. Abre o painel de controle duplicado.
3. Renomeie o painel e modificá-lo como desejar.

## <a name="set-a-default-dashboard"></a>Definir um painel padrão

Você pode definir qualquer painel como padrão. Definindo-a como seu padrão torna aparecem como a guia mais à esquerda na lista de guia do painel. O painel padrão é exibida quando abrir o portal de Cloudyn.

- Clique na guia Painel que você deseja definir como padrão e clique em **padrão** à direita.

-Ou-

1. Clique em **configurações do painel** para ver a lista de painéis disponíveis e selecione o painel que você deseja definir como padrão.  
    ![opções do painel para um painel padrão](./media/dashboards/dashboard-options.png)
2. Clique em **padrão** na linha do painel. A caixa de confirmação de painel padrão é exibida.
3. Clique em **Sim**. O painel está definido como padrão.

## <a name="management-dashboard"></a>Painel de gerenciamento
O gerenciamento (ou painel MSP para usuários do MSP) painel inclui destaques dos tipos de relatório principal.  
![Painel de gerenciamento mostrando vários relatórios](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Resumo de custo de entidade (Enterprise somente)
Esse widget resume as entidades de custo gerenciados, incluindo o número de entidades e o número de contas.
- Clique o widget para abrir o relatório de detalhes da empresa.

### <a name="cost-over-time"></a>Custo ao longo do tempo
Este widget pode ajudá-lo a identificar tendências de custo. Ela realça o custo para o último dia, com base em tendências dos últimos 30 dias.
- Clique o widget para abrir o relatório de custo por hora real para visualizar e filtrar detalhes adicionais.

### <a name="asset-controller"></a>Controlador ativo
Esse widget realça o número de instâncias em execução do dia anterior, acima a tendência de uso nos últimos 30 dias.
- Clique o widget para abrir o painel do controlador ativo.

### <a name="unused-ri-detector"></a>Detector de RI não utilizado
Esse widget realça o número de EC2 Amazon reservas não utilizadas.
- Clique no widget para abrir o relatório no momento não usadas reservas onde você pode exibir o não utilizados reservas, você pode modificar.

### <a name="cost-by-service"></a>Custo por serviço
Esse widget realça os custos amortizados pelo serviço para os últimos 30 dias. Passe o mouse sobre o gráfico de pizza para ver os custos por serviço.
- Clique o widget para abrir o relatório de análise de custo real.

### <a name="potential-savings"></a>Potencial de economia
Esse widget mostra o tipo de instância preços recomendações para EC2 Amazon e RDS Amazon.
- Clique em abrir o widget o relatório de análise de economia. Ela lista os custos por tipos de instância com potencial de economia.

### <a name="compute-instances---daily-trend"></a>Computação instâncias - tendência diária
Esse widget exibe as instâncias ativas por tipo, nos últimos 30 dias.
- Clique o widget para abrir o relatório de instâncias ao longo do tempo, onde você pode exibir uma análise de todas as instâncias em execução durante os últimos 30 dias.

### <a name="storage-by-department"></a>Armazenamento por departamento
Esse widget exibe os serviços de armazenamento usados por departamentos. Passe o mouse sobre o gráfico de pizza para ver seu consumo de armazenamento por departamento.
- Clique o widget para abrir o painel do rastreador S3.

## <a name="cost-controller-dashboard"></a>Painel de controle de custos
O painel do controlador de custo mostra realces de alocação de custo previamente configurado.  
![Painel do Controlador de Custo mostrando vários relatórios](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Custo ao longo do tempo
Esse widget ajuda você a identificar as tendências de custo. Ela realça o custo para o último dia, com base em tendências dos últimos 30 dias.
- Clique o widget para abrir o relatório de custo por hora real para visualizar e filtrar detalhes adicionais.

### <a name="monthly-cost-trends"></a>Tendências de custo mensal
Esse widget realça gastos amortizados projetadas e gastar seu real desde o início do mês.
- Clique o widget para abrir o relatório de custo projetado de mês atual, que fornece um resumo de custo acumulado no mês.

Este relatório mostra o custo do início do mês, o custo do mês anterior e o custo do mês atual projetado. O mês atual custo projetado é calculado adicionando o custo mensal atualizado e projeção. A projeção é baseada no custo monitorado nos últimos 30 dias.

### <a name="12-month-planner"></a>Planejador de 12 meses
Este widget destaca os custos projetados nos próximos 12 meses e as economias potenciais.
- Clique no widget para abrir o relatório Custo Anual Projetado.

### <a name="cost-by-service"></a>Custo por serviço
Esse widget realça os custos amortizados pelo serviço para os últimos 30 dias.
- Passe o mouse sobre o gráfico de pizza para ver os custos por serviço.
- Clique o widget para abrir o relatório de análise de custo real.

### <a name="cost-by-account"></a>Custo por conta
Este widget destaca os custos amortizados por conta nos últimos 30 dias.
- Passe o mouse sobre o gráfico de pizza para ver os custos por conta.
- Clique o widget para abrir o relatório de análise de custo real.

### <a name="cost-trend-by-day"></a>Tendência de custo por dia
Esse widget destaques gastam nos últimos 30 dias.
- Passe o mouse sobre o gráfico de barras para ver os custos por dia.
- Clique no widget para abrir o relatório Custo Real no Tempo.

### <a name="cost-trend-by-month---last-6-months"></a>Tendência de custo por mês - últimos 6 meses

Esse widget destaques gastam nos últimos seis meses.
- Passe o mouse sobre o gráfico de barras para ver os custos por mês.
- Clique no widget para abrir o relatório Custo Real no Tempo.

## <a name="asset-controller-dashboard"></a>Painel Controlador de Ativos

Esse painel exibe o número de instâncias de discos disponíveis e em uso, distribuição de tipos de instância e as informações de armazenamento.  
![Painel do Controlador de Ativos mostrando vários relatórios](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Instâncias de computação
Esse widget exibe o número de instâncias com base na tendência de uso nos últimos 30 dias.
- Clique no widget para abrir o relatório Instances Over Time.

### <a name="disks"></a>Discos
Esse widget realça o número total e o volume de discos, que estão em uso e disponível.
- Clique no widget para abrir o relatório Active Disks.

### <a name="instance-type-distribution"></a>Distribuição de tipo de instância
Esse widget realça os tipos de instância em um gráfico de pizza.
- Clique no widget para abrir o relatório distribuição da instância, que fornece uma análise das instâncias ativas por agregação selecionada.

### <a name="compute-instances---daily-trend"></a>Computação instâncias - tendência diária
Esse widget realça as instâncias de computação (especiais reservadas e sob demanda) por dia nos últimos 30 dias.
- Passe o mouse sobre o gráfico para exibir o número de instâncias de computação, por tipo por dia.
- Clique no widget para abrir o relatório Instances Over Time.

### <a name="all-buckets-s3"></a>Todos os Buckets (S3)
Esse widget realça o armazenamento S3 total e o número de objetos armazenados.
- Clique o widget para abrir o painel de rastreador S3. O painel Ajuda você a encontrar, analisar e exibir a utilização de armazenamento atual e tendências.

### <a name="sql-db-instances-rds"></a>Instâncias de banco de dados SQL (RDS)
Esse widget realça o número de instâncias de RDS Amazon com base em tendências dos últimos 30 dias.
- Clique no widget para abrir o relatório de Instância do RDS ao longo do tempo.

## <a name="optimizer-dashboard"></a>Painel do otimizador
Esse painel exibe o potencial de economia de recomendações de downsizing e recursos não utilizados.  
![Painel do Otimizador mostrando vários relatórios](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Calculadora RI
Este widget exibe o número de recomendações de compra do RI e destaca a possível economia anual.
- Clique o widget para abrir a Calculadora de instância reservado onde você pode determinar quando usar sob demanda versus reservado planos de preços.

### <a name="sizing"></a>Dimensionamento
Esse widget realça o potencial de economia e dimensionamento recomendado se implementado.
- Clique o widget para abrir o relatório de recomendações de dimensionamento do EC2 econômico.

### <a name="unused-ri-detector"></a>Detector de RI não utilizado
Esse widget realça o número de EC2 Amazon reservas não utilizadas.
- Clique o widget para abrir o relatório no momento não usadas reservas onde você pode exibir as reservas não utilizadas que você pode modificar.

###  <a name="available-disks"></a>Discos disponíveis
Esse widget realça o número de discos não fixados na sua implantação.
- Clique o widget para abrir o relatório de discos desanexada.

### <a name="rds-ri-calculator"></a>Calculadora RDS RI
Esse widget realça o número de recomendações de reserva para suas instâncias de RDS do Amazon e o potencial de economia.
- Clique o widget para abrir o relatório de recomendações de compra do RDS RI onde você pode ver Cloudyn recomendações para usar instâncias reservadas em vez de instâncias de sob demanda.

### <a name="rds-sizing"></a>Dimensionamento de RDS
Esse widget mostra o número de recomendações de dimensionamento e o potencial de economia.
- Clique no widget para abrir o relatório Recomendações de dimensionamento do RDS, que exibe recomendações detalhadas de dimensionamento do Amazon RDS.

As recomendações de otimização são baseadas nos dados de uso e desempenho monitorados no último mês.

## <a name="s3-tracker-dashboard"></a>Painel do rastreador S3
O painel do rastreador S3 ajuda você a encontrar, analisar e exibir a utilização de armazenamento atual e tendências.  
![Painel do rastreador S3 mostrando vários relatórios](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Todos os Buckets
Esse widget realça o tamanho total de todos os seus buckets, em GB e o número total de objetos no seu buckets.
- Clique o widget para abrir o relatório de tamanho de distribuição de S3. O relatório ajuda a analisar seu tamanho S3 bucket, pasta de nível superior, classe de armazenamento e estado de controle de versão.

### <a name="bucket-properties"></a>Propriedades de bucket
Esse widget realça o número total de blocos de armazenamento.
- Clique o widget para exibir o relatório de propriedades de Bucket S3.

### <a name="scan-status"></a>Verificar Status
Esse widget realça quando a última verificação S3 foi feita e quando o outro será iniciado.
- Clique no widget para abrir o relatório S3 Scan Status.

### <a name="storage-by-bucket"></a>Armazenamento de Bucket
Esse widget realça o percentual de cada classe de armazenamento de bucket está usando.
- Clique o widget para abrir o relatório de tamanho de distribuição de S3. O relatório ajuda a analisar seu tamanho S3 bucket, pasta de nível superior, classe de armazenamento e estado de controle de versão.

### <a name="number-of-objects-by-bucket"></a>Número de objetos por Bucket
Esse widget realça o número de objetos por bucket em número real e porcentagem. Passe o mouse sobre o bucket para ver o total de objetos.
- Clique o widget para abrir o relatório de tamanho de distribuição de S3 (verificação com base).

## <a name="cloud-comparison-dashboard"></a>Painel de comparação de nuvem
O painel de comparação de nuvem ajuda você a comparar os custos de provedores de nuvem diferentes com base no preço, o tipo de CPU e o tamanho da RAM.  
![Painel de Comparação de Nuvem mostrando vários relatórios](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Custo no Azure com o tipo de instância
Esse widget realça os últimos 30 dias de uso em taxas de sob demanda. Ele compara o custo com o custo atual do EC2 Amazon com o custo potencial no Azure.
- Passe o mouse sobre as barras para comparar os custos por tipo de instância.
- Clique o widget para abrir a portabilidade sua implantação – relatório de análise de custo.

### <a name="ec2-cost-in-azure"></a>EC2 Custo no Azure
Esse widget mostra seus custos atuais do Amazon EC2 e compara-as para o Azure. A comparação é baseada nos últimos 30 dias de uso em taxas sob demanda.
- Clique no widget para abrir o relatório Portar sua implantação - Análise de custo.

### <a name="ec2azure-instance-type-mapping"></a>Mapeamento de tipo de instância EC2/do Azure
Esse widget realça o mapeamento de melhor de unidades de computação elástica entre EC2 Amazon e o Azure.
- Clique no widget para abrir o relatório Instances Over Time.

## <a name="next-steps"></a>Próximas etapas
- Leia o artigo [Usar relatórios do Cloudyn](use-reports.md) para saber mais sobre relatórios.
