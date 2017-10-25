---
title: Otimizar seu ambiente do System Center Operations Manager com o Azure Log Analytics | Microsoft Docs
description: "Você pode usar a solução System Center Operations Manager Assessment para avaliar o risco e a integridade de seus ambientes do servidor em intervalos regulares."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4992d98397da409f7c1cfbdeb40fdb0cdd0d2f19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>Otimizar seu ambiente com a solução System Center Operations Manager Assessment (Prévia)

![Símbolo do Avaliação do System Center Operations Manager](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Você pode usar a solução System Center Operations Manager Assessment para avaliar o risco e a integridade de seus ambientes do servidor System Center Operations Manager em intervalos regulares. Este artigo ajuda você a instalar, configurar e usar a solução para que possa tomar ações corretivas para os problemas em potencial.

Esta solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco, que ajudam você a entender rapidamente o risco e a tomar uma ação corretiva.

As recomendações baseiam-se no conhecimento e na experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. Cada recomendação fornece diretrizes sobre por que um problema pode ser relevante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso no sentido de executar um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma avaliação ser concluída, as informações de resumo das áreas de foco são mostradas no painel **System Center Operations Manager Assessment** de sua infraestrutura. As seções a seguir descrevem como usar as informações no painel **System Center Operations Manager Assessment**, onde você pode exibir e executar as ações recomendadas de sua infraestrutura do SCOM.

![Bloco de solução do System Center Operations Manager](./media/log-analytics-scom-assessment/scom-tile.png)

![Painel do System Center Operations Manager Assessment](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

A solução funciona com o Microsoft System Operations Manager 2012 R2 e 2012 SP1.

Use as informações a seguir para instalar e configurar a solução.

 - Antes de usar uma solução de avaliação no OMS, é necessário ter a solução instalada. Instale a solução do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) ou seguindo as instruções em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).

 - Após adicionar a solução ao espaço de trabalho, o bloco do System Center Operations Manager Assessment no painel exibirá a mensagem de configuração adicional necessária. Clique no bloco e siga as etapas de configuração mencionadas na página

 ![Bloco do painel do System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 A configuração do System Center Operations Manager pode ser feita por meio do script, seguindo as etapas mencionadas na página de configuração da solução no OMS.

 Como alternativa, para configurar a avaliação por meio do Console do SCOM, siga as etapas abaixo na mesma ordem
1. [Definir como conta Executar como para o System Center Operations Manager Assessment](#operations-manager-run-as-accounts-for-oms)  
2. [Configurar a regra do System Center Operations Manager Assessment](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Detalhes da coleta de dados de avaliação do System Center Operations Manager

A avaliação do System Center Operations Manager coleta dados do WMI, dados de Registro, dados do Log de Eventos, dados do Operations Manager por meio do Windows PowerShell, Consultas SQL e coletor de Informações do Arquivo usando o servidor que você habilitou.

A tabela a seguir mostra os métodos da coleta de dados do System Center Operations Manager Assessment e a frequência com a qual os dados são coletados por um agente.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | | | | &#8226; | | sete dias |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Contas Executar como do Operations Manager para OMS

O OMS é criado com base nos pacotes de gerenciamento das cargas de trabalho para fornecer serviços com valor agregado. Cada carga de trabalho exige privilégios específicos da carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de domínio. Configure uma conta Executar Como do Operations Manager para fornecer as informações de credenciais.

Use as informações a seguir para definir a conta executar como do Operations Manager para o System Center Operations Manager Assessment.

### <a name="set-the-run-as-account"></a>Definir a conta Executar Como

1. No Console do Operations Manager, vá para a guia **Administração**.
2. Em **Configuração para Executar Como**, clique em **Contas**.
3. Crie a Conta Executar Como, seguindo o Assistente, criando uma conta do Windows. A conta a usar é a identificada e tem todos os pré-requisitos abaixo:

    >[!NOTE]
    A conta Executar Como deve atender aos seguintes requisitos:
    - Um membro da conta de domínio do grupo local de Administradores em todos os servidores no ambiente (todas as funções do Operations Manager – Servidor de Gerenciamento, Banco de Dados do OpsMgr, Data Warehouse, Relatórios, Console Web, Gateway)
    - Função de Administrador do Operation Manager para o grupo de gerenciamento que está sendo avaliado
    - Execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares à conta na instância do SQL usada pelo Operations Manager.
      Observação: se essa conta já tiver direitos sysadmin, ignore a execução do script.

4. Em **Segurança da Distribuição**, selecione **Mais seguro**.
5. Especifique o servidor de gerenciamento no qual a conta é distribuída.
3. Volte para a Configuração para Executar Como e clique em **Perfis**.
4. Pesquise o *Perfil de Avaliação do SCOM*.
5. O nome do perfil deve ser: *Perfil para Executar Como do Microsoft System Center Advisor SCOM Assessment*.
6. Clique com botão direito, atualize suas propriedades e adicione a Conta para Executar Como criada recentemente na etapa 3.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL para conceder permissões granulares à conta Executar Como

Execute o seguinte script SQL para conceder as permissões necessárias para a conta Executar Como na instância do SQL usada pelo Operations Manager.

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


### <a name="configure-the-assessment-rule"></a>Configurar a regra de avaliação

O pacote de gerenciamento da solução System Center Operations Manager Assessment inclui uma regra denominada *Regra de Avaliação da Execução do Microsoft System Center Advisor SCOM Assessment*. Essa regra é responsável por executar a avaliação. Para habilitar a regra e configurar a frequência, use os procedimentos a seguir.

Por padrão, a Regra de Avaliação da Execução do Microsoft System Center Advisor SCOM Assessment está desabilitada. Para executar a avaliação, você deve habilitar a regra em um servidor de gerenciamento. Use as etapas a seguir.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Habilitar a regra para um servidor de gerenciamento específico

1. No espaço de trabalho **Criação** do console do Operations Manager, pesquise a regra *Regra de Avaliação da Execução do Microsoft System Center Advisor SCOM Assessment* no painel **Regras**.
2. Nos resultados da pesquisa, selecione aquela que inclui o texto *Tipo: Servidor de Gerenciamento*.
3. Clique com o botão direito na regra, em seguida, clique em **Substituições** > **Para um objeto específico da classe: Servidor de Gerenciamento**.
4.  Na lista de servidores de gerenciamento disponíveis, selecione o servidor de gerenciamento no qual a regra deve ser executada.
5.  Verifique se você alterou o valor de substituição para **True** para o valor do parâmetro **Habilitado**.  
    ![parâmetro de substituição](./media/log-analytics-scom-assessment/rule.png)

Ainda nessa janela, configure a frequência da execução usando o procedimento a seguir.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência da execução

A avaliação está configurada para ser executada a cada 10.080 minutos (ou sete dias), o intervalo padrão. Você pode substituir o valor por um valor mínimo de 1.440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo necessário entre as execuções sucessivas da avaliação. Para substituir o intervalo, use as etapas a seguir.

1. No espaço de trabalho **Criação** do console do Operations Manager, pesquise a regra *Regra de Avaliação da Execução do Microsoft System Center Advisor SCOM Assessment* no painel **Regras**.
2. Nos resultados da pesquisa, selecione aquela que inclui o texto *Tipo: Servidor de Gerenciamento*.
3. Clique com o botão direito na regra, em seguida, clique em **Substituir a Regra** > **Para todos os objetos da classe: Servidor de Gerenciamento**.
4. Altere o valor do parâmetro **Intervalo** para o valor de intervalo desejado. No exemplo a seguir, o valor é definido para 1.440 minutos (um dia).  
    ![parâmetro do intervalo](./media/log-analytics-scom-assessment/interval.png)  
    Se o valor for definido para menos de 1.440 minutos, a regra será executada em um intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e é executada com uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de ponderação que identifica a importância relativa da recomendação. Somente as 10 recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados

Os pesos são valores agregados com base em três fatores principais:

- A *probabilidade* de que um problema identificado cause problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
- O *impacto* da questão na sua organização se ela causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço maior é igual a uma pontuação geral menor para a recomendação.

A importância de cada recomendação é expressa como um percentual da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco Disponibilidade e Continuidade de Negócios tiver uma pontuação de 5%, implementar essa recomendação aumentará a pontuação geral da Disponibilidade e Continuidade de Negócios em 5%.

### <a name="focus-areas"></a>Áreas de foco

**Disponibilidade e continuidade dos negócios** - essa área de foco mostra as recomendações para a disponibilidade de serviço, resiliência de sua infraestrutura e proteção dos negócios.

**Desempenho e escalabilidade** - essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de TI de sua organização, garantir que seu ambiente de TI atende aos requisitos de desempenho atuais e estar apta a responder às necessidades de infraestrutura em constante mudança.

**Atualização, Migração e Implantação** - essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar o SQL Server em sua infraestrutura existente.

**Operações e monitoramento** - essa área de foco mostra recomendações que ajudam a simplificar as operações de TI, implementar manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar à pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações baseiam-se no conhecimento e nas experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, nenhuma infraestrutura de servidor é igual à outra, assim, recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Problemas importantes para empresas maduras podem ser menos importantes para uma empresa start-up. Você pode preferir identificar quais áreas de foco são suas prioridades e depois examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre sua importância. Você deve usar essas diretrizes para avaliar se é adequado implementar a recomendação, considerando a natureza de seus serviços de TI e as necessidades comerciais de sua organização.

## <a name="use-assessment-focus-area-recommendations"></a>Use as recomendações de área de foco de avaliação

Antes de usar uma solução de avaliação no OMS, é necessário ter a solução instalada. Para ler mais sobre como instalar as soluções, confira [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Após a instalação, você pode exibir o resumo das recomendações usando o bloco System Center Operations Manager Assessment na página Visão Geral do OMS.

Veja as avaliações de conformidade resumidas para sua infraestrutura e faça uma busca detalhada das recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva

1. Na página **Visão Geral**, clique no bloco **System Center Operations Manager Assessment**.
2. Na página **System Center Operations Manager Assessment**, analise as informações de resumo em uma das folhas da área de foco e clique em uma para exibir as recomendações dessa área de foco.
3. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas para seu ambiente. Clique em uma recomendação sob **Objetos Afetados** para exibir detalhes sobre o motivo pelo qual a recomendação foi feita.  
    ![área de foco](./media/log-analytics-scom-assessment/focus-area.png)
4. É possível executar as ações corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravarão que essas ações recomendadas foram executadas e sua pontuação de conformidade aumentará. Os itens corrigido aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações

Se houver recomendações que você deseja ignorar, poderá criar um arquivo de texto que será usado pelo OMS para impedir que as recomendações sejam exibidas nos resultados da avaliação.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar as recomendações que você deseja ignorar

1. Entre em seu espaço de trabalho e abra a Pesquisa de Log. Use a consulta a seguir para listar as recomendações que falharam para os computadores em seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Esta é uma captura de tela mostrando a consulta da Pesquisa de Log:   
    ![pesquisa do log](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Escolha as recomendações que você deseja ignorar. Você usará os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Criar e usar um arquivo de texto IgnoreRecommendations.txt

1. Crie um arquivo chamado IgnoreRecommendations.txt.
2. Cole ou digite cada RecommendationId para cada recomendação que você deseja que o OMS ignore em uma linha separada, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador no qual você deseja que o OMS ignore as recomendações.
4. No servidor de gerenciamento do Operations Manager - *SystemDrive*:\Arquivos de Programa\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Após a execução da próxima avaliação agendada, por padrão, a cada sete dias, as recomendações especificadas são marcadas como Ignoradas e não aparecerão no painel de avaliação.
2. É possível usar as consultas da Pesquisa de Log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations.txt ou remova as RecommendationIDs deles.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>Perguntas frequentes da solução System Center Operations Manager Assessment

*Adicionei a solução de avaliação ao meu espaço de trabalho do OMS. Contudo, não estou vendo as recomendações. Por que não?* Depois de adicionar a solução, use as etapas a seguir para exibir as recomendações no painel do OMS.  

- [Definir como conta Executar como para o System Center Operations Manager Assessment](#operations-manager-run-as-accounts-for-oms)  
- [Configurar a regra do System Center Operations Manager Assessment](#configure-the-assessment-rule)


*Há uma maneira de configurar a frequência com que a avaliação é executada?* Sim. Consulte [Configurar a frequência de execução](#configure-the-run-frequency).

*Se outro servidor for descoberto após eu ter adicionado uma solução System Center Operations Manager Assessment, ele será avaliado?* Sim, após a descoberta, ele será avaliado deste ponto em diante – por padrão, a cada sete dias.

*Qual é o nome do processo que faz a coleta de dados?* AdvisorAssessment.exe

*Onde é executado o processo do AdvisorAssessment.exe?* O AdvisorAssessment.exe é executado no Serviço de Integridade do servidor de gerenciamento onde a regra de avaliação está habilitada. Usando esse processo, a descoberta de todo o ambiente é obtida por meio da coleta de dados remota.

*Quanto tempo leva para a coleta dos dados?* A coleta dos dados no servidor leva cerca de uma hora. Pode levar mais tempo nos ambientes com muitas instâncias do Operations Manager ou bancos de dados.

*E se eu definir o intervalo da avaliação para menos de 1.440 minutos?* A avaliação será pré-configurada para executar no máximo uma vez por dia. Se você substituir o valor do intervalo por um valor inferior a 1.440 minutos, a avaliação usará 1.440 minutos como o valor do intervalo.

*Como saber se há falhas de pré-requisito?* Se a avaliação foi executada e você não vir os resultados, é provável que alguns dos pré-requisitos da avaliação falharam. Você pode executar as consultas: `Type=Operation Solution=SCOMAssessment` e `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` na Pesquisa do Log para ver os pré-requisitos com falha.

*Há uma `Failed to connect to the SQL Instance (….).` mensagem nas falhas do pré-requisito. Qual é o problema?* O AdvisorAssessment.exe, o processo que coleta dados, é executado no Serviço de Integridade do servidor de gerenciamento. Como parte da avaliação, o processo tenta conectar o SQL Server onde o banco de dados do Operations Manager está presente. Esse erro pode ocorrer quando as regras de firewall bloqueiam a conexão com a instância do SQL Server.

*Que tipo de dados é coletado?* Os seguintes tipos de dados são coletados: - dados WMI - Dados de registro - dados do Log de Eventos - dados do Operations Manager por meio do Windows PowerShell, Consultas SQL e Coletor de informações do arquivo.

*Por que é necessário configurar uma conta Executar como?* Para um servidor do Operations Manager, são executadas várias consultas SQL. Para executá-las, você deve usar uma Conta para Executar Como com as permissões necessárias. Além disso, as credenciais do administrador local são necessárias para a consulta WMI.

*Por que apenas as 10 principais recomendações são exibidas?* Em vez de apresentar uma lista enorme e cansativa de tarefas, é recomendável que você se concentre em endereçar as recomendações priorizadas primeiro. Depois de tratar dessas recomendações, recomendações adicionais serão disponibilizadas. Se você preferir ver a lista detalhada, poderá ver todas as recomendações usando a Pesquisa de Log.

*É possível ignorar uma recomendação?* Sim, consulte [Ignorar recomendações](#Ignore-recommendations).


## <a name="next-steps"></a>Próximas etapas

- [Pesquise os logs](log-analytics-log-searches.md) para exibir dados detalhados e recomendações do System Center Operations Manager Assessment.
