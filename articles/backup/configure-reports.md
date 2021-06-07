---
title: Configurar relatórios de Backup do Azure
description: Configurar e exibir relatórios para o Backup do Azure usando o Log Analytics e as pastas de trabalho do Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0720af0848aa8263587dfd9573d205abf73303d4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562317"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

Um requisito comum para administradores de backup é obter insights sobre backups com base em dados que abrangem um longo período. Os casos de uso para essa solução incluem:

- Alocação e previsão do armazenamento em nuvem consumido.
- Auditoria de backups e restaurações.
- Identificação das principais tendências em diferentes níveis de granularidade.

Hoje, o Backup do Azure fornece uma solução de relatório que usa os [logs do Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md) e as [pastas de trabalho do Azure](../azure-monitor/visualize/workbooks-overview.md). Esses recursos ajudam você a obter informações detalhadas sobre os backups em toda a sua propriedade de backups. Este artigo explica como configurar e ver Relatórios de Backup do Azure.

## <a name="supported-scenarios"></a>Cenários com suporte

- Há suporte para Relatórios de Backup em VMs do Azure, no SQL em VMs do Azure, no SAP HANA em VMs do Azure, no agente do MARS (Serviços de Recuperação do Microsoft Azure), no MABS (Servidor de Backup do Microsoft Azure) e no System Center DPM (Data Protection Manager). Para o backup do compartilhamento de arquivos do Azure, os dados são exibidos para os registros criados em ou após 1º de junho de 2020.
- Para backup de compartilhamento de arquivos do Azure, os dados em instâncias protegidas são exibidos para registros criados após 1º de fevereiro de 2021 (o padrão é zero para registros mais antigos).
- Para cargas de trabalho do DPM, há suporte para Relatórios de Backup no DPM versão 5.1.363.0 e superior e no Agente versão 2.0.9127.0 e superior.
- Para cargas de trabalho do MABS, há suporte para Relatórios de Backup no MABS versão 13.0.415.0 e superior e no Agente versão 2.0.9170.0 e superior.
- Os Relatórios de Backup podem ser exibidos em todos os itens de backup, cofres, assinaturas e regiões, desde que os dados estejam sendo enviados para um workspace do Log Analytics ao qual o usuário tenha acesso. Para exibir os relatórios de um conjunto de cofres, você só precisa ter acesso de leitor ao workspace do Log Analytics ao qual os cofres estão enviando os dados. Você não precisa ter acesso aos cofres individuais.
- Se você for um usuário do [Azure Lighthouse](../lighthouse/index.yml) com acesso delegado às assinaturas dos clientes, poderá usar esses relatórios com o Azure Lighthouse para ver os relatórios em todos os seus locatários.
- Atualmente, os dados podem ser exibidos nos Relatórios de Backup em um máximo de 100 workspaces do Log Analytics (entre locatários).
- Atualmente, os dados para trabalhos de backup de log não são exibidos nos relatórios.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Introdução

Siga estas etapas para começar a usar os relatórios.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Criar um workspace do Log Analytics ou usar um existente

Configure um ou mais workspaces do Log Analytics para armazenar seus dados de relatório de backup. A localização e a assinatura em que esse workspace do Log Analytics pode ser criado são independentes da localização e da assinatura em que os cofres estão.

Para configurar um workspace do Log Analytics, confira [Criar um workspace do Log Analytics no portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

Por padrão, os dados de um workspace do Log Analytics são mantidos por 30 dias. Para ver os dados em um horizonte de tempo maior, altere o período de retenção do workspace do Log Analytics. Para alterar o período de retenção, confira [Gerenciar o uso e os custos com os logs do Azure Monitor](../azure-monitor/logs/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Definir as configurações de diagnóstico para os cofres

Os recursos do Azure Resource Manager, como os cofres dos Serviços de Recuperação, registram informações sobre as operações agendadas e as operações disparadas pelo usuário como dados de diagnóstico.

Na seção de monitoramento do cofre dos Serviços de Recuperação, selecione **Configurações de diagnóstico** e especifique o destino dos dados de diagnóstico do cofre dos Serviços de Recuperação. Para saber mais sobre como usar eventos de diagnóstico, confira [Usar configurações de diagnóstico para os cofres dos Serviços de Recuperação](./backup-azure-diagnostic-events.md).

![Painel Configurações de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Backup do Azure também fornece uma definição interna do Azure Policy, que automatiza a definição de configurações de diagnóstico para todos os cofres em determinado escopo. Para saber como usar essa política, confira [Definir as configurações de diagnóstico do cofre em escala](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Depois que você configurar o diagnóstico, poderá levar até 24 horas para que o push de dados inicial seja concluído. Depois que os dados começam a fluir para o workspace do Log Analytics, talvez você não veja os dados nos relatórios imediatamente, pois os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, confira [Convenções usadas em Relatórios de Backup](#conventions-used-in-backup-reports). Recomendamos que você comece a ver os relatórios dois dias depois de configurar os cofres para enviar dados para o Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Ver os relatórios no portal do Azure

Depois de configurar os cofres para enviar dados ao Log Analytics, veja os Relatórios de Backup acessando qualquer painel do cofre e selecionando **Relatórios de Backup**.

![Painel do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir a pasta de trabalho de relatório de Backup.

> [!NOTE]
>
> - Atualmente, a carga inicial do relatório pode levar até 1 minuto.
> - O cofre dos Serviços de Recuperação é meramente um ponto de entrada para os Relatórios de Backup. Depois que a pasta de trabalho do relatório de Backup for aberta no painel de um cofre, selecione o conjunto apropriado de workspaces do Log Analytics para ver os dados agregados em todos os cofres.

O relatório contém várias guias:

##### <a name="summary"></a>Resumo

Use esta guia para obter uma visão geral de alto nível do seu espaço de backup. Obtenha uma visão rápida do número total de itens de backup, do armazenamento em nuvem total consumido, do número de instâncias protegidas e da taxa de sucesso do trabalho por tipo de carga de trabalho. Para obter informações mais detalhadas sobre um tipo de artefato de backup específico, acesse as respectivas guias.

   ![Guia Resumo](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Itens de Backup

Use essa guia para ver informações e tendências sobre o armazenamento em nuvem consumidos em um nível de item de backup. Por exemplo, se você usar o SQL em um backup de VM do Azure, poderá ver o armazenamento em nuvem consumido para cada Banco de Dados SQL que está sendo copiado em backup. Você também pode optar por ver os dados de itens de backup de um status de proteção específico. Por exemplo, a seleção do bloco **Proteção Interrompida** na parte superior da guia filtra todos os widgets abaixo para mostrar os dados somente dos itens de backup no estado Proteção Interrompida.

   ![Guia Itens de Backup](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Uso

Use essa guia para exibir os principais parâmetros de cobrança para seus backups. As informações mostradas nessa guia estão em um nível de entidade de cobrança (contêiner protegido). Por exemplo, se um servidor DPM estiver sendo submetido a backup no Azure, você poderá exibir a tendência de instâncias protegidas e o armazenamento em nuvem consumidos para o servidor DPM. Da mesma forma, se você usar o SQL ou o SAP HANA no Backup do Azure, essa guia fornecerá informações relacionadas ao uso no nível da máquina virtual na qual esses bancos de dados estão contidos.

   ![Guia Uso](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Para cargas de trabalho do DPM, os usuários podem ver uma pequena diferença (da ordem de 20 MB por servidor DPM) entre os valores de uso mostrados nos relatórios em comparação com o valor de uso agregado, conforme mostrado na guia **visão geral** do cofre dos serviços de recuperação. Essa diferença é contabilizada pelo fato de que cada servidor DPM que está sendo registrado para backup tem uma fonte de fontes de "metadados" associada que não é exibido como um artefato para relatórios.

##### <a name="jobs"></a>Trabalhos

Use essa guia para exibir tendências de longa execução em trabalhos, como o número de trabalhos com falha por dia e as principais causas de falha do trabalho. Veja essas informações em um nível de agregação e de item de Backup. Selecione um item de Backup específico em uma grade para ver informações detalhadas sobre cada trabalho disparado nesse item de Backup no intervalo de tempo selecionado.

   ![Guia Trabalhos](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Políticas

Use essa guia para exibir informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento em nuvem total consumido por itens de backup em uma determinada política. Selecione uma política específica para ver informações sobre cada um dos itens de backup associados.

   ![Guia Políticas](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Otimizar

Use essa guia para obter visibilidade de possíveis oportunidades de otimização de custos para seus backups. Veja a seguir os cenários para os quais a guia otimizar atualmente fornece informações:

###### <a name="inactive-resources"></a>Recursos inativos

Usando essa exibição, você pode identificar os itens de backup que não tiveram um backup bem-sucedido por uma duração significativa. Isso pode significar que a máquina subjacente que está sendo submetida a backup não existe mais (e isso resulta em backups com falha) ou há algum problema com o computador que está impedindo que os backups sejam feitos de forma confiável.

Para exibir recursos inativos, navegue até a guia **otimizar** e selecione o bloco **recursos inativos** . Selecionar este bloco exibe uma grade que contém detalhes de todos os recursos inativos existentes no escopo selecionado. Por padrão, a grade mostra os itens que não têm um ponto de recuperação nos últimos sete dias. Para localizar recursos inativos para um intervalo de tempo diferente, você pode ajustar o filtro de **intervalo de tempo** na parte superior da guia.

Depois de identificar um recurso inativo, você pode investigar ainda mais o problema navegando até o painel do item de backup ou o painel de recursos do Azure para esse recurso (sempre que aplicável). Dependendo do seu cenário, você pode optar por interromper o backup do computador (se ele não existir mais) e excluir backups desnecessários, o que economiza custos, ou você pode corrigir problemas no computador para garantir que os backups sejam feitos de forma confiável.

![Otimizar recursos inativos de guia](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Itens de backup com grande duração da retenção

Usando essa exibição, você pode identificar os itens que têm backups retidos por uma duração maior do que o exigido pela sua organização.

Selecionar o bloco **otimizações de política** seguido pelo bloco **otimizações de retenção** exibe uma grade que contém todos os itens de backup para os quais a retenção do RP (ponto de retenção) diário, semanal, mensal ou anual é maior do que um valor especificado. Por padrão, a grade exibe todos os itens de backup no escopo selecionado. Você pode usar os filtros para a retenção de RP diária, semanal, mensal e anual para filtrar a grade mais detalhadamente e identificar os itens para os quais a retenção pode ser reduzida para economizar em custos de armazenamento de backup.

Para cargas de trabalho de banco de dados como SQL e SAP HANA, os períodos de retenção mostrados na grade correspondem aos períodos de retenção dos pontos de backup completos e não aos pontos de backup diferenciais. O mesmo se aplica aos filtros de retenção também.  

![Otimizar otimizações de retenção de guia](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Bancos de dados configurados para backup completo diário 

Usando essa exibição, você pode identificar cargas de trabalho de banco de dados que foram configuradas para backup completo diário. Geralmente, o uso do backup diferencial diário, juntamente com o backup completo semanal, é mais econômico.

Selecionar o bloco **otimizações de política** seguido pelo bloco **otimizações de agendamento de backup** exibe uma grade que contém todos os bancos de dados com uma política de backup completo diário. Você pode optar por navegar até um item de backup específico e modificar a política para usar o backup diferencial diário com o backup completo semanal.

O filtro de **tipo de gerenciamento de backup** na parte superior da guia deve ter os itens **SQL na VM do Azure** e **SAP Hana na VM do Azure** selecionada, para que a grade seja capaz de exibir cargas de trabalho de banco de dados conforme o esperado.

![Otimizar otimizações de agendamento de backup de guia](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Adesão à política

Usando essa guia, você pode identificar se todas as instâncias de backup tiveram pelo menos um backup bem-sucedido todos os dias. Para itens com a política de backup semanal, você pode usar essa guia para determinar se todas as instâncias de backup tiveram pelo menos um backup bem-sucedido de uma semana.

Há dois tipos de exibições de adesão de política disponíveis:

* **Adesão de política por período de tempo**: usando essa exibição, você pode identificar quantos itens tiveram pelo menos um backup bem-sucedido em um determinado dia e quantos não tiveram um backup bem-sucedido nesse dia. Você pode clicar em uma linha para ver os detalhes de todos os trabalhos de backup que foram disparados no dia selecionado. Observe que se você aumentar o intervalo de tempo para um valor maior, como os últimos 60 dias, a grade será renderizada no modo de exibição semanal e exibirá a contagem de todos os itens que tiveram pelo menos um backup bem-sucedido em cada dia da semana determinada. Da mesma forma, há uma exibição mensal para intervalos de tempo maiores.

No caso de itens com backup semanal, essa grade ajuda a identificar todos os itens que tiveram pelo menos um backup bem-sucedido na semana determinada. Para um intervalo de tempo maior, como os últimos 120 dias, a grade é renderizada na exibição mensal e exibe a contagem de todos os itens que tiveram pelo menos um backup bem-sucedido em cada semana no mês determinado. Consulte [convenções usadas em relatórios de backup](#conventions-used-in-backup-reports) para obter mais detalhes sobre os modos de exibição diário, semanal e mensal.

![Adesão de política por período de tempo](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Adesão de política por instância de backup**: usando essa exibição, você pode obter detalhes de adesão de política em um nível de instância de backup. Uma célula que é verde indica que a instância de backup tinha pelo menos um backup bem-sucedido no dia determinado. Uma célula que é vermelha indica que a instância de backup não tinha até um backup bem-sucedido no dia determinado. As agregações diária, semanal e mensal seguem o mesmo comportamento que a política de adesão por período de tempo. Você pode clicar em qualquer linha para exibir todos os trabalhos de backup na instância de backup específica no intervalo de tempo selecionado.

![Adesão de política por instância de backup](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>Enviar relatórios de Backup do Azure por email

Usando o recurso de **relatório de email** disponível em relatórios de backup, você pode criar tarefas automatizadas para receber relatórios periódicos por email. Esse recurso funciona implantando um aplicativo lógico em seu ambiente do Azure que consulta dados de seus espaços de trabalho selecionados de Log Analytics (LA), com base nas entradas que você fornecer.

Depois que o aplicativo lógico for criado, você precisará autorizar conexões com Azure Monitor logs e o Office 365. Para fazer isso, navegue até **aplicativos lógicos** na portal do Azure e procure o nome da tarefa que você criou. A seleção do item de menu **conexões de API** abre a lista de conexões de API que você precisa autorizar. [Saiba mais sobre como configurar emails e solucionar problemas](backup-reports-email.md).

###### <a name="customize-azure-backup-reports"></a>Personalizar relatórios de backup do Azure

Os relatórios de backup usam [funções de sistema em logs de Azure monitor](backup-reports-system-functions.md). Essas funções operam em dados nas tabelas brutas de backup do Azure em LA e retornam dados formatados que ajudam a recuperar facilmente as informações de todas as suas entidades relacionadas ao backup, usando consultas simples. 

Para criar suas próprias pastas de trabalho de relatório usando relatórios de backup como base, você pode navegar até relatórios de backup, clicar em **Editar** na parte superior do relatório e Exibir/editar as consultas que estão sendo usadas nos relatórios. Consulte a [documentação de pastas de trabalho do Azure](../azure-monitor/visualize/workbooks-overview.md) para saber mais sobre como criar relatórios personalizados. 

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo no canto superior direito de qualquer widget, como uma tabela ou um gráfico, para exportar o conteúdo desse widget como uma planilha do Excel no estado em que se encontra com os filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, aumente o número de linhas exibidas na página usando a seta suspensa **Linhas por Página** na parte superior de cada grade.

## <a name="pin-to-dashboard"></a>Fixar no painel

Selecione o botão Fixar na parte superior de cada widget para fixar o widget ao painel do portal do Azure. Esse recurso ajuda você a criar painéis personalizados adaptados para exibir as informações mais importantes de que você precisa.

## <a name="cross-tenant-reports"></a>Relatórios entre locatários

Se você usar o [Azure Lighthouse](../lighthouse/index.yml) com acesso delegado a assinaturas em vários ambientes de locatário, poderá usar o filtro de assinatura padrão. Selecione o botão Filtro no canto superior direito do portal do Azure para escolher todas as assinaturas para as quais deseja ver os dados. Isso permite que você selecione workspaces do Log Analytics nos seus locatários para ver relatórios de multilocatário.

## <a name="conventions-used-in-backup-reports"></a>Convenções usadas em Relatórios de Backup

- Os filtros funcionam da esquerda para a direita e de cima para baixo em cada guia. Ou seja, qualquer filtro só se aplica aos widgets posicionados à direita desse filtro ou abaixo dele.
- A seleção de um bloco colorido filtra os widgets abaixo do bloco para os registros que pertencem ao valor desse bloco. Por exemplo, a seleção do bloco **Proteção Interrompida** na guia **Itens de Backup** filtra as grades e os gráficos abaixo para mostrar os dados de itens de backup no estado Proteção Interrompida.
- Blocos que não são coloridos não são selecionáveis.
- Os dados do dia parcial atual não são mostrados nos relatórios. Portanto, quando o valor selecionado de **Intervalo de Tempo** for **Últimos 7 dias**, o relatório mostrará os registros dos últimos sete dias concluídos. O dia atual não está incluído.
- O relatório mostra os detalhes dos trabalhos (além dos trabalhos de log) que foram *disparados* no intervalo de tempo selecionado.
- Os valores mostrados para **Armazenamento em Nuvem** e **Instâncias Protegidas** estão no *final* do intervalo de tempo selecionado.
- Os itens de Backup exibidos nos relatórios são aqueles que estão no *final* do intervalo de tempo selecionado. Os itens de Backup que foram excluídos no meio do intervalo de tempo selecionado não são exibidos. A mesma convenção também se aplica às políticas de Backup.
- Se o intervalo de tempo selecionado abrange um período de 30 dias de menos, os gráficos são renderizados na exibição diária, em que há um ponto de dados para todos os dias. Se o intervalo de tempo abranger um período maior que 30 dias e menor que (ou igual a) 90 dias, os gráficos serão renderizados no modo de exibição semanal. Para intervalos de tempo maiores, os gráficos são renderizados no modo de exibição mensal. A agregação de dados semanalmente ou mensal ajuda a melhorar o desempenho de consultas e facilitar a legibilidade dos dados em gráficos.
- As grades de adesão de política também seguem uma lógica de agregação semelhante, conforme descrito acima. No entanto, há algumas pequenas diferenças. A primeira diferença é que, para itens com política de backup semanal, não há nenhuma exibição diária (somente exibições semanais e mensais estão disponíveis). Além disso, nas grades de itens com a política de backup semanal, um ' mês ' é considerado um período de 4 semanas (28 dias) e não 30 dias, para eliminar semanas parciais de consideração.

## <a name="query-load-times"></a>Tempos de carregamento de consulta

Os widgets do relatório de Backup são ativados por consultas Kusto, que são executadas nos workspaces do Log Analytics do usuário. Essas consultas normalmente envolvem o processamento de grandes quantidades de dados, com várias junções para proporcionar insights mais sofisticados. Como resultado, os widgets podem não ser carregados instantaneamente quando o usuário vê os relatórios em uma propriedade de backup grande. Esta tabela fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para serem carregados, com base no número de itens de Backup e no intervalo de tempo no qual o relatório está sendo exibido.

| **Nº de fontes de dados**                         | **Horizonte de tempo** | **Tempos de carregamento aproximados**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 mil                       | 1 mês          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros no nível do relatório: 5-10 segundos|
| ~5 mil                       | 3 meses          | Blocos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros no nível do relatório: 5-10 segundos|
| ~10 mil                       | 3 meses          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 1 a 2 minutos <br> Filtros no nível do relatório: 25-30 segundos|
| ~15 mil                       | 1 mês          | Blocos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 50-60 segundos <br> Filtros no nível do relatório: 20-25 segundos|
| ~15 mil                       | 3 meses          | Blocos: 20-30 segundos <br> Grades: 20-30 segundos <br> Gráficos: 2 a 3 minutos <br> Filtros no nível do relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a>O que aconteceu com os relatórios do Power BI?

- O aplicativo de modelo anterior do Power BI para relatórios, que obtinha dados de uma conta de armazenamento do Azure, está em um caminho de substituição. Recomendamos que você comece a enviar os dados de diagnóstico do cofre ao Log Analytics para ver os relatórios.

- Além disso, o [esquema V1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) de envio dos dados de diagnóstico para uma conta de armazenamento ou um workspace do LA também está em um caminho de substituição. Isso significa que, se você tiver escrito qualquer consulta ou automação personalizada com base no esquema v1, será aconselhável atualizar essas consultas para usar o esquema V2 com suporte no momento.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre monitoramento e relatórios com o Backup do Azure](./backup-azure-monitor-alert-faq.md)