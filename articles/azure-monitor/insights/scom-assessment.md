---
title: Avaliar System Center Operations Manager com Azure Monitor
description: Você pode usar a solução System Center Operations Manager Health Check para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 35ae1e09fd0a06014a747cef99631a7bfe2dee1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731401"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Otimizar seu ambiente com a solução System Center Operations Manager Health Check (Versão prévia)

![Símbolo do System Center Operations Manager Health Check](./media/scom-assessment/scom-assessment-symbol.png)

Você pode usar a solução System Center Operations Health Check para avaliar o risco e a integridade de seu grupo de gerenciamento do System Center Operations Manager em intervalos regulares. Este artigo ajuda você a instalar, configurar e usar a solução para que possa tomar ações corretivas para os problemas em potencial.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco, que ajudam você a entender rapidamente o risco e a tomar uma ação corretiva.

As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma avaliação ser executada, as informações de resumo das áreas de foco são mostradas no painel **System Center Operations Manager Health Check** de sua infraestrutura. As seções a seguir descrevem como usar as informações no painel **System Center Operations Health Check**, onde você pode exibir e executar as ações recomendadas para seu ambiente do Operations Manager.

![Bloco de solução do System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Painel do System Center Operations Manager Health Check](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

A solução funciona com o Microsoft System Center 2012 Operations Manager Service Pack 1, o Microsoft System Center 2012 R2 Operations Manager, o Microsoft System Center 2016 Operations Manager, o Microsoft System Center 2016 Operations Manager e o Microsoft System Center Operations Manager 1807. Uma versão com suporte do .NET Framework 4.6.2 deve ser instalada em cada servidor de gerenciamento.

Use as informações a seguir para instalar e configurar a solução.

- Antes de usar a solução Health Check no Log Analytics, é necessário tê-la instalada. Instale a solução do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Após adicionar a solução ao workspace, o bloco **Verificação de Integridade do System Center Operations Manager** no painel exibirá uma mensagem de configuração adicional necessária. Clique no bloco e siga as etapas de configuração mencionadas na página

  ![Bloco do painel do System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A configuração do System Center Operations Manager pode ser feita usando um script, seguindo as etapas mencionadas na página de configuração da solução no Log Analytics.

 Para configurar a avaliação pelo console de operações do Operations Manager, execute as etapas abaixo na seguinte ordem:
1. [Definir como conta Executar como para o System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Configurar a regra do System Center Operations Manager Health Check

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Detalhes da coleta de dados da Verificação de Integridade do System Center Operations Manager

A solução Verificação de Integridade do System Center Operations Manager coleta dados das seguintes fontes:

* Registro
* WMI (Instrumentação de Gerenciamento do Windows)
* Log de eventos
* Dados de arquivo
* Diretamente do Operations Manager usando o PowerShell e as consultas SQL, de um servidor de gerenciamento que você especificou.  

Os dados são coletados no servidor de gerenciamento e encaminhados para o Log Analytics a cada sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Executar como do Operations Manager para Log Analytics

O Log Analytics utiliza os pacotes de gerenciamento das cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de usuário do domínio. Configure uma conta Executar como do Operations Manager com credenciais com privilégios. Para saber mais, confira [Como criar uma conta Executar como](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) na documentação do Operations Manager.

Use as informações a seguir para definir a conta Executar como do Operations Manager para o System Center Operations Health Check.

### <a name="set-the-run-as-account"></a>Definir a conta Executar Como

Antes de tudo, a conta Executar como deve atender aos seguintes requisitos:

* Uma conta de usuário de domínio que seja membro do grupo Administradores local em todos os servidores que deem suporte a funções do Operations Manager: servidor de gerenciamento, SQL Server que hospede servidores operacionais, data warehouse e banco de dados ACS, relatórios, console da Web e servidor de gateway.
* Função de Administrador do Operation Manager para o grupo de gerenciamento que está sendo avaliado
* Se a conta não tem direitos de sysadmin do SQL, execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões detalhadas para a conta em cada instância do SQL Server que hospeda um ou todos os bancos de dados do Operations Manager.

1. Abra o console do Operations Manager e selecione o botão de navegação **Administração**.
2. Em **Configuração para Executar Como**, clique em **Contas**.
3. No assistente **Criar Conta Executar Como**, na página **Introdução**, clique em **Avançar**.
4. Na página **Propriedades Gerais**, selecione **Windows** na lista **Tipo de Conta Executar Como:**.
5. Digite um nome para exibição na caixa de texto **Nome de Exibição**; opcionalmente, digite uma descrição na caixa **Descrição** e clique em **Avançar**.
6. Na página **Segurança da Distribuição**, selecione **Mais seguro**.
7. Clique em **Criar**.  

Agora que a conta Executar como foi criada, ela precisa ser direcionada para servidores de gerenciamento no grupo de gerenciamento e ser associada a um perfil Executar como predefinido para que os fluxos de trabalho sejam executados usando as credenciais.  

1. Em **Configuração Executar Como**, **Contas**, no painel de resultados, clique duas vezes na conta criada anteriormente.
2. Na guia **Distribuição**, clique em **Adicionar** na caixa **Computadores selecionados** e adicione o servidor de gerenciamento onde a conta deve ser distribuída.  Clique em **OK** duas vezes para salvar as alterações.
3. Em **Configuração Executar Como**, clique em **Perfis**.
4. Pesquise o *Perfil de Avaliação do SCOM*.
5. O nome do perfil deve ser: *Microsoft verificação de integridade do System Center Operations Manager perfil executar como*.
6. Clique com botão direito, atualize suas propriedades e adicione a Conta Executar como criada recentemente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL para conceder permissões granulares à conta Executar Como

Execute o seguinte script SQL para conceder as permissões necessárias para a conta Executar como na instância do SQL Server usada pelo Operations Manager que hospeda o banco de dados operacional, o banco de dados ACS e o data warehouse.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Configurar a regra de verificação de integridade

O pacote de gerenciamento da solução Verificação de Integridade do System Center Operations Manager inclui uma regra chamada *Microsoft System Center Operations Manager executar regra de verificação de integridade*. Essa regra é responsável por executar a verificação de integridade. Para habilitar a regra e configurar a frequência, use os procedimentos a seguir.

Por padrão, a regra de verificação de integridade de execução do Microsoft System Center Operations Manager está desabilitada. Para executar a verificação de integridade, você deve habilitar a regra em um servidor de gerenciamento. Use as etapas a seguir.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Habilitar a regra para um servidor de gerenciamento específico

1. No espaço de trabalho **criação** do console de operações Operations Manager, procure a regra *Microsoft System Center Operations Manager executar regra de verificação de integridade* no painel **regras** .
2. Nos resultados da pesquisa, selecione aquela que inclui o texto *Tipo: Servidor de Gerenciamento*.
3. Clique com o botão direito do mouse na regra e clique em **substituições**  >  **para um objeto específico da classe: servidor de gerenciamento**.
4.  Na lista de servidores de gerenciamento disponíveis, selecione o servidor de gerenciamento no qual a regra deve ser executada.  Ele deve ser o mesmo servidor de gerenciamento configurado anteriormente para associar a conta Executar como.
5.  Verifique se você alterou o valor de substituição para **True** para o valor do parâmetro **Habilitado**.<br><br> ![parâmetro de substituição](./media/scom-assessment/rule.png)

    Ainda nessa janela, configure a frequência da execução usando o procedimento a seguir.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência da execução

A avaliação está configurada para ser executada a cada 10.080 minutos (ou sete dias) por padrão. Você pode substituir o valor por um valor mínimo de 1.440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo necessário entre as execuções sucessivas da avaliação. Para substituir o intervalo, use as etapas a seguir.

1. No espaço de trabalho **criação** do console do Operations Manager, procure a regra *Microsoft System Center Operations Manager executar a regra de verificação de integridade* na seção **regras** .
2. Nos resultados da pesquisa, selecione aquela que inclui o texto *Tipo: Servidor de Gerenciamento*.
3. Clique com o botão direito do mouse na regra e clique em **substituir a regra**  >  **para todos os objetos da classe: servidor de gerenciamento**.
4. Altere o valor do parâmetro **Intervalo** para o valor de intervalo desejado. No exemplo a seguir, o valor é definido para 1.440 minutos (um dia).<br><br> ![parâmetro do intervalo](./media/scom-assessment/interval.png)<br>  

    Se o valor for definido para menos de 1440 minutos, a regra será executada em um intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e é executada com uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as 10 recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados

Os pesos são valores agregados com base em três fatores principais:

- A *probabilidade* que um problema identificado causar problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
- O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco Disponibilidade e Continuidade de Negócios tiver uma pontuação de 5%, implementar essa recomendação aumentará a pontuação geral da Disponibilidade e Continuidade de Negócios em 5%.

### <a name="focus-areas"></a>Áreas de foco

**Disponibilidade e continuidade dos negócios** - essa área de foco mostra as recomendações para a disponibilidade de serviço, resiliência de sua infraestrutura e proteção dos negócios.

**Desempenho e escalabilidade** - essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de TI de sua organização, garantir que seu ambiente de TI atende aos requisitos de desempenho atuais e estar apta a responder às necessidades de infraestrutura em constante mudança.

**Atualização, migração e implantação** – essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar SQL Server em sua infraestrutura existente.

**Operações e monitoramento** - essa área de foco mostra recomendações que ajudam a simplificar as operações de TI, implementar manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar à pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações baseiam-se no conhecimento e nas experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre sua importância. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação, considerando a natureza de seus serviços de TI e as necessidades comerciais de sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar as recomendações da área de foco da Verificação da Integridade

Antes de usar a solução Health Check no Log Analytics, é necessário tê-la instalada. Para saber mais sobre soluções de instalação, consulte [Instalar uma solução de gerenciamento](./solutions.md). Após a instalação, você pode exibir o resumo das recomendações usando o bloco Verificação de Integridade do System Center Operations Manager na página **Visão Geral** do workspace no portal do Azure.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
1. Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione o **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um workspace e clique no item de menu **resumo de Workspace**.  
4. Na página **Visão Geral**, clique no bloco **System Center Operations Manager Health Check**.
5. Na página **System Center Operations Manager Health Check**, analise as informações de resumo em uma das folhas da área de foco e clique em uma para exibir as recomendações dessa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.<br><br> ![área de foco](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações

Se houver recomendações que você deseja ignorar, poderá criar um arquivo de texto que é usado pelo Log Analytics para impedir que as recomendações sejam exibidas nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar as recomendações que você deseja ignorar
1. No portal do Azure na página do espaço de trabalho do Log Analytics de seu espaço de trabalho selecionado, clique no item de menu **Pesquisa de Logs**.
2. Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se o seu workspace fosse atualizado para a [nova linguagem de consulta do Log Analytics](../logs/log-query-overview.md), a consulta acima seria alterada para o demonstrado a seguir.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Aqui está uma captura de tela mostrando a consulta de pesquisa de log:<br><br> ![pesquisa do log](./media/scom-assessment/scom-log-search.png)<br>

3. Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt

1. Crie um arquivo chamado IgnoreRecommendations.txt.
2. Cole ou digite a RecommendationId de cada recomendação que você deseja que o Log Analytics ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o Log Analytics ignore as recomendações.
4. No servidor de gerenciamento do Operations Manager - *SystemDrive*:\Arquivos de Programa\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Após a execução da próxima avaliação agendada, por padrão, a cada sete dias, as recomendações especificadas são marcadas como Ignoradas e não aparecerão no painel de verificação de integridade.
2. É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se o seu workspace fosse atualizado para a [nova linguagem de consulta do Log Analytics](../logs/log-query-overview.md), a consulta acima seria alterada para o demonstrado a seguir.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Perguntas frequentes sobre a solução System Center Operations Manager Health Check

*Adicionei a solução de verificação de integridade ao meu espaço de trabalho do Log Analytics. Mas não vejo as recomendações. Por que não?* Depois de adicionar a solução, use as etapas a seguir para exibir as recomendações no painel do Log Analytics.  

- [Definir como conta Executar como para o System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurar a regra do System Center Operations Manager Health Check](#configure-the-health-check-rule)


*Existe alguma maneira de configurar a frequência de execução da verificação?* Sim. Consulte [Configurar a frequência de execução](#configure-the-run-frequency).

*Se outro servidor for descoberto após ter adicionado a solução de Verificação de Integridade do System Center Operations Manager, ele será verificado?* Sim, após a descoberta, ele será verificado deste ponto em diante; por padrão, a cada sete dias.

*Qual é o nome do processo que faz a coleta de dados?* AdvisorAssessment.exe

*Onde é executado o processo do AdvisorAssessment.exe?* O AdvisorAssessment.exe é executado no processo HealthService do servidor de gerenciamento onde a regra de verificação de integridade está habilitada. Usando esse processo, a descoberta de todo o ambiente é obtida por meio da coleta de dados remota.

*Quanto tempo leva para a coleta dos dados?* A coleta dos dados no servidor leva cerca de uma hora. Pode levar mais tempo nos ambientes com muitas instâncias do Operations Manager ou bancos de dados.

*E se eu definir o intervalo da avaliação para menos de 1.440 minutos?* A avaliação será pré-configurada para executar no máximo uma vez por dia. Se você substituir o valor do intervalo por um valor inferior a 1.440 minutos, a avaliação usará 1.440 minutos como o valor do intervalo.

*Como saber se há falhas de pré-requisito?* Se a verificação de integridade foi executada e você não vê os resultados, é provável que alguns dos pré-requisitos para a verificação tenham falhado. Você pode executar as consultas: `Operation Solution=SCOMAssessment` e `SCOMAssessmentRecommendation FocusArea=Prerequisites` na Pesquisa de Logs para ver os pré-requisitos com falha.

*Há uma `Failed to connect to the SQL Instance (….).` mensagem em falhas de pré-requisito. Qual é o problema?* O AdvisorAssessment.exe, o processo que coleta dados, é executado no processo HealthService do servidor de gerenciamento. Como parte da verificação de integridade, o processo tenta conectar o SQL Server onde o banco de dados do Operations Manager está presente. Esse erro pode ocorrer quando as regras de firewall bloqueiam a conexão com a instância do SQL Server.

*Que tipo de dados é coletado?* Os seguintes tipos de dados são coletados: - dados WMI - Dados de registro - dados do Log de Eventos - dados do Operations Manager por meio do Windows PowerShell, Consultas SQL e Coletor de informações do arquivo.

*Por que é necessário configurar uma conta Executar como?* Com o Operations Manager, várias consultas SQL são executadas. Para executá-las, você deve usar uma Conta para Executar Como com as permissões necessárias. Além disso, as credenciais do administrador local são necessárias para a consulta WMI.

*Por que apenas as 10 principais recomendações são exibidas?* Em vez de apresentar uma lista enorme e cansativa de tarefas, é recomendável que você se concentre em endereçar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se você preferir ver a lista detalhada, poderá ver todas as recomendações usando a Pesquisa de Log.

*É possível ignorar uma recomendação?* Sim, consulte [Ignorar recomendações](#ignore-recommendations).


## <a name="next-steps"></a>Próximas etapas

- [Pesquisar logs](../logs/log-query-overview.md) para aprender como analisar dados detalhados e recomendações do System Center Operations Manager Health Check.

