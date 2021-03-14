---
title: Otimize seu ambiente de SQL Server com Azure Monitor | Microsoft Docs
description: Com o Azure Monitor, você pode usar a solução de verificação de integridade do SQL para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723224"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Otimize seu ambiente SQL com a solução de verificação de integridade SQL Server no Azure Monitor

![Símbolo de Verificação da Integridade do SQL](./media/sql-assessment/sql-assessment-symbol.png)

Você pode usar a solução de Verificação da Integridade do SQL para avaliar o risco e a integridade de seus ambientes do servidor em intervalos regulares. Este artigo ajudará você a instalar a solução para que você possa tomar ações corretivas para potenciais problemas.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em seis áreas de foco que ajudarão a entender o risco e tomar uma ação corretiva.

As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Após ter adicionado a solução e a avaliação ser concluída, as informações resumidas para as áreas de foco são mostradas no painel **Verificação da Integridade do SQL** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel **Verificação da Integridade do SQL**, onde é possível exibir e tomar as ações recomendadas para sua infraestrutura do SQL Server.

![imagem do bloco Verificação da Integridade do SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do painel Verificação da Integridade do SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de integridade do SQL requer uma versão com suporte do .NET Framework 4.6.2 instalada em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é usado pelo System Center 2016 – Operations Manager, pelo Operations Manager 2012 R2 e pelo Azure Monitor.  
* A solução dá suporte a SQL Server versão 2012, 2014, 2016, 2017 e 2019.
* Um espaço de trabalho do Log Analytics para adicionar a solução de Verificação da Integridade do SQL no Azure marketplace, no portal do Azure. Para instalar a solução, o usuário deve ser um administrador ou colaborador na assinatura do Azure.

  > [!NOTE]
  > Depois de adicionar a solução, o arquivo AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e, em seguida, enviados para o Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.
  >
  >

Para executar a verificação de integridade em seus servidores de SQL Server, eles exigem um agente e conectividade para Azure Monitor usando um dos seguintes métodos com suporte:

1. Instale o [MMA (Microsoft Monitoring Agent)](../agents/agent-windows.md) se o servidor ainda não for monitorado pelo System Center 2016 - Operations Manager ou Operations Manager 2012 R2.
2. Se ele for monitorado com o System Center 2016-Operations Manager ou Operations Manager 2012 R2 e o grupo de gerenciamento não estiver integrado com o Azure Monitor, o servidor poderá ser multihomed com Log Analytics para coletar dados e encaminhar para o serviço e ainda ser monitorado pelo Operations Manager.  
3. Caso contrário, se seu grupo de gerenciamento Operations Manager for integrado com o serviço, você precisará adicionar controladores de domínio para a coleção de dados pelo serviço seguindo as etapas em [adicionar computadores gerenciados por agente](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de habilitar a solução em seu workspace.  

O agente no SQL Server que relata para um grupo de gerenciamento de Operations Manager, coleta dados, encaminha para seu servidor de gerenciamento atribuído e é enviado diretamente de um servidor de gerenciamento para Azure Monitor.  Os dados não são gravados nos bancos de dados do Operations Manager.  

Se o SQL Server for monitorado pelo Operations Manager, você precisará configurar uma conta Executar Como do Operations Manager. Consulte [Operations Manager contas Executar como para Azure monitor](#operations-manager-run-as-accounts-for-log-analytics) abaixo para obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes da coleção de dados da Verificação da Integridade do SQL
A Verificação da Integridade do SQL coleta dados das seguintes fontes usando o agente habilitado:

* WMI (Instrumentação de Gerenciamento do Windows)
* Registro
* Contadores de desempenho
* Resultados da exibição do gerenciamento dinâmico do SQL Server

Os dados são coletados no SQL Server e encaminhados para o Log Analytics a cada sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Executar como do Operations Manager para Log Analytics
O Log Analytics usa o agente Operations Manager e o grupo de gerenciamento para coletar e enviar dados para o serviço Log Analytics. O Log Analytics utiliza os pacotes de gerenciamento das cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de usuário do domínio. Você precisa fornecer informações de credenciais configurando uma conta Executar como do Operations Manager.

Use as informações a seguir para definir a conta Executar como do Operations Manager para a Verificação da Integridade do SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Executar como para a Verificação da Integridade do SQL
 Se você já estiver usando o pacote de gerenciamento SQL Server, deverá usar a configuração Executar Como.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a conta Executar como do SQL no console Operações
> [!NOTE]
> Por padrão, os fluxos de trabalho no pacote de gerenciamento é executado no contexto de segurança da conta Sistema Local. Se você estiver usando o Microsoft Monitoring Agent conectado diretamente ao serviço, em vez de informar diretamente a um grupo de gerenciamento Operations Manager, ignore as etapas 1 a 5 abaixo e execute o T-SQL ou PowerShell de exemplo, especificando NT AUTHORITY\SYSTEM como o nome de usuário.
>
>

1. No Operations Manager, abra o console de operações e, em seguida, clique em **Administração**.
2. Em **Configuração Executar como**, clique em **Perfis** e abra **Perfil Executar como da Avaliação do SQL**.
3. Na página **Contas executar como**, clique em **Adicionar**.
4. Selecione uma conta Executar como do Windows que contém as credenciais necessárias para o SQL Server ou clique em **Nova** para criar uma.

   > [!NOTE]
   > O tipo de conta Executar Como deve ser Windows. A conta Executar como também deve fazer parte do grupo de administradores locais em todos os servidores Windows que hospedam instâncias do SQL Server.
   >
   >
5. Clique em **Save** (Salvar).
6. Modifique e execute o seguinte T-SQL de exemplo em cada instância do SQL Server para conceder as permissões mínimas necessárias para a conta Executar como realizar a verificação da integridade. No entanto, você não precisará fazer isso se uma conta Executar como já fizer parte da função do servidor sysadmin nas instâncias do SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a conta Executar como do SQL usando o Windows PowerShell
Abra uma janela do PowerShell e execute o script a seguir depois de atualizá-lo com as suas informações:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreendendo como as recomendações são priorizadas
Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as dez recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados
Os pesos são valores agregados com base em três fatores principais:

* A *probabilidade* que um problema identificado causar problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
* O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará sua pontuação geral de segurança e conformidade em 5%.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** - essa área de foco mostra recomendações para possíveis ameaças de segurança e violações, políticas corporativas, bem como os requisitos de conformidade técnica, legal e regulatória.

**Disponibilidade e continuidade dos negócios** - essa área de foco mostra as recomendações para a disponibilidade de serviço, resiliência de sua infraestrutura e proteção dos negócios.

**Desempenho e escalabilidade** - essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de TI de sua organização, garantir que seu ambiente de TI atende aos requisitos de desempenho atuais e estar apta a responder às necessidades de infraestrutura em constante mudança.

**Atualização, implantação e migração** - essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar o SQL Server em sua infraestrutura existente.

**Operações e monitoramento** - essa área de foco mostra recomendações que ajudam a simplificar as operações de TI, implementar manutenção preventiva e maximizar o desempenho.

**Gerenciamento de alterações e configuração** - essa área de foco mostra as recomendações para ajudar a proteger as operações diárias, garantir que as alterações não afetem sua infraestrutura negativamente, estabelecer procedimentos de controle de alterações, bem como para controlar e auditar as configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar à pontuação de 100% em cada área de foco?
Não necessariamente. As recomendações baseiam-se no conhecimento e nas experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre sua importância. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação considerando a natureza de seus serviços de TI e as necessidades comerciais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar as recomendações da área de foco da Verificação da Integridade
Para poder usar uma solução de avaliação no Azure Monitor, você deve ter a solução instalada.  Após a instalação, você pode exibir o resumo das recomendações usando o bloco verificação de integridade do SQL na página **visão geral** para Azure Monitor no portal do Azure.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Monitor**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Monitor**.
3. Na seção **insights** do menu, selecione **mais**.  
4. Na página **Visão Geral**, clique no bloco **Verificação da Integridade do SQL**.
5. Na página **Verificação da Integridade**, revise as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações dessa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.<br><br> ![imagem das recomendações de Verificação da Integridade do SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações
Se houver recomendações que deseja ignorar, você poderá criar um arquivo de texto que será usado pelo Azure Monitor para impedir que as recomendações sejam exibidas nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que serão ignoradas
1. No menu Azure Monitor, clique em **logs**.
2. Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Aqui está uma captura de tela mostrando a consulta de log:<br><br> ![recomendações com falha](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt
1. Crie um arquivo chamado IgnoreRecommendations.txt.
2. Cole ou digite a RecommendationId de cada recomendação que você deseja que o Azure Monitor ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o Azure Monitor ignore as recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio do Operations Manager) - *SystemDrive*:\Arquivos de Programas\Microsoft Monitoring Agent\Agent
   * Na Operations Manager servidor de gerenciamento- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gerenciamento do Operations Manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
1. Após a execução da próxima avaliação agendada, por padrão a cada 7 dias, as recomendações especificadas são marcadas como Ignoradas e não aparecerão no painel de avaliação.
2. É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## <a name="sql-health-check-solution-faq"></a>Perguntas frequentes da solução de Verificação da Integridade do SQL

*Quais verificações são executadas pela solução de Avaliação do SQL?*

* A consulta a seguir mostra uma descrição de todas as verificações executadas no momento:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem então ser exportados para o Excel para análise adicional.


*Quantas vezes uma verificação da integridade é executada?*

* A verificação é executada a cada sete dias.

*Existe alguma maneira de configurar a frequência de execução da verificação?*

* Não no momento.

*Se outro servidor for descoberto após eu ter adicionado a solução de Verificação da Integridade do SQL, ele será verificado?*

* Sim, assim que for descoberto, ele será verificado a partir de então a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da verificação de integridade?*

* Se um servidor não enviar dados por três semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*

* AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*

* A coleta de dados real no servidor leva aproximadamente 1 hora. Pode levar mais tempo em servidores que têm um grande número de instâncias ou bancos de dados SQL.

*Que tipo de dados é coletado?*

* Os seguintes tipos de dados são coletados:
  * WMI
  * Registro
  * Contadores de desempenho
  * Exibições de gerenciamento dinâmico SQL (DMV).

*Há uma maneira de configurar quando os dados são coletados?*

* Não no momento.

*Por que é necessário configurar uma conta Executar como?*

* Para o SQL Server, um pequeno número de consultas SQL é executado. Para elas serem executadas, uma conta Executar como com permissões VIEW SERVER STATE devem ser usadas para SQL.  Além disso, para consultar WMI, são necessárias credenciais de administrador local.

*Por que apenas as 10 principais recomendações são exibidas?*

* Em vez de apresentar uma lista exaustiva de tarefas, é recomendável que você se concentre em tratar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se preferir ver a lista detalhada, exiba todas as recomendações usando a pesquisa de logs do Log Analytics.

*É possível ignorar uma recomendação?*

* Sim, confira a seção [Ignorar recomendações](#ignore-recommendations) acima.

## <a name="next-steps"></a>Próximas etapas
* [Registre consultas](../logs/log-query-overview.md) para saber como analisar dados e recomendações de verificação de integridade do SQL detalhados.

