---
title: Executar consultas de análise
description: Consultas de análise entre locatários usando dados extraídos de vários bancos de dados do Banco de Dados SQL do Azure em um único aplicativo multilocatário.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96447294"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Análise entre locatários usando dados extraídos – Aplicativo multilocatário
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Neste tutorial, você percorrerá um cenário completo de análise para a implementação de multilocatários. O cenário demonstra como a análise pode habilitar as empresas a tomar decisões inteligentes. Usando dados extraídos do banco de dados fragmentado, use a análise para aprofundar-se no comportamento de locatários, incluindo o uso do aplicativo SaaS Wingtip Tickets de exemplo. Este cenário envolve três etapas: 

1.  **Extraia dados** de cada banco de dados de locatário em um repositório analítico.
2.  **Otimize os dados extraídos** para processamento de análise.
3.  Use ferramentas de **Business Intelligence** para obter percepções úteis, que podem orientar a tomada de decisões. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Crie o repositório de análise de locatário para extrair os dados.
> - Use trabalhos elásticos para extrair dados de cada banco de dados de locatário para o repositório da análise.
> - Otimize os dados extraídos (reorganize em um esquema de estrela).
> - Consulte o banco de dados de análise.
> - Use o Power BI para visualização de dados para realçar as tendências dos dados de locatário e fazer recomendações para melhorias.

![O diagrama mostra uma visão geral da arquitetura usada neste artigo.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise de locatário offline

Os aplicativos SaaS que você desenvolve têm acesso a uma grande quantidade de dados de locatário armazenados na nuvem. Os dados fornecem uma fonte avançada de informações sobre a operação e o uso do aplicativo e o comportamento dos locatários. Essas percepções podem guiar o desenvolvimento de recursos, aprimoramentos de usabilidade e outros investimentos no aplicativo e na plataforma.

Acessar os dados para todos os locatários é simples quando todos os dados estão em apenas um banco de dados multilocatário. Porém, o acesso é mais complexo quando distribuído em grande escala em milhares de bancos de dados. Uma maneira de controlar a complexidade é extrair os dados para um banco de dados de análise ou um data warehouse. Em seguida, consulte o data warehouse para coletar informações dos dados de tíquetes de todos os locatários.

Este tutorial apresenta um cenário completo de análise para este aplicativo SaaS de exemplo. Primeiro, trabalhos elásticos são usados para agendar a extração de dados de cada banco de dados de locatário. Os dados são enviados para um repositório analítico. O repositório de análise pode ser um Banco de Dados SQL ou um Azure Synapse Analytics. Para extração de dados em grande escala, o [Azure Data Factory](../../data-factory/introduction.md) é recomendado.

Em seguida, os dados agregados são fragmentados em um conjunto de tabelas de [esquema em estrela](https://www.wikipedia.org/wiki/Star_schema). As tabelas consistem em uma tabela de fatos central, mais tabelas de dimensões relacionadas:

- A tabela de fatos central no esquema de estrela contém dados de tíquetes.
- As tabelas de dimensões contêm dados sobre locais, eventos, clientes e datas de compra.

Juntas, as tabelas central e de dimensão habilitam o processamento analítico eficiente. O esquema em estrela usado neste tutorial é exibido na seguinte imagem:
 
![Diagrama de banco de dados mostra quatro objetos de banco de dados conectados a um objeto de banco de dados central.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Por fim, as tabelas de esquema em estrela são consultadas. Os resultados da consulta são exibidos visualmente para realçar as informações sobre o comportamento de locatário e seu uso do aplicativo. Com esse esquema em estrela, você pode executar consultas que ajudam a descobrir itens como estes:

- Quem está comprando ingressos e de qual local.
- Padrões e tendências ocultos nas seguintes áreas:
    - As vendas de tíquetes.
    - A popularidade relativa de cada local.

Ao entender a consistência com que cada locatário está usando o serviço, você tem a oportunidade de criar planos de serviço para atender às necessidades deles. Este tutorial fornece exemplos básicos de informações que podem ser obtidas por meio dos dados de locatário.

## <a name="setup"></a>Instalação

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de atender a todos os seguintes pré-requisitos:

- O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado. Para implantá-lo em menos de cinco minutos, confira [Implantar e explorar o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets](./saas-multitenantdb-get-started-deploy.md)
- Os scripts SaaS do Wingtip e o [código-fonte](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) do aplicativo são baixadas do GitHub. Não se esqueça de *desbloquear o arquivo zip* antes de extrair seu conteúdo. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.
- O Power BI Desktop está instalado. [Baixe o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- O lote de locatários adicionais foi provisionado. Confira o [**Tutorial de provisionamento de locatários**](./saas-multitenantdb-provision-and-catalog.md).
- Um agente de trabalho e o banco de dados do agente de trabalho foram criados. Veja as etapas apropriadas no [**Tutorial de gerenciamento de esquema**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Criar dados para a demonstração

Neste tutorial, a análise é executada em relação aos dados de vendas de tíquetes. Na etapa atual, você pode gerar dados de tíquete para todos os locatários.  Posteriormente, esses dados são extraídos para análise. *Verifique se você provisionou o lote de locatários conforme descrito anteriormente, para que tenha uma quantidade significativa de dados*. Uma quantidade suficientemente grande de dados pode expor um intervalo de diferentes padrões de compra de tíquetes.

1. No **ISE do PowerShell**, abra *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* e defina o seguinte valor:
    - **$DemoScenario** = **1** Comprar ingressos para eventos em todos os locais
2. Pressione **F5** para executar o script e criar o histórico de compra de tíquetes de todos os eventos em cada local.  O script é executado por vários minutos para gerar dezenas de milhares de tíquetes.

### <a name="deploy-the-analytics-store"></a>Implantar o repositório de análise
Geralmente, há vários bancos de dados fragmentados transacionais que, juntos, contêm todos os dados de locatário. É necessário agregar os dados de locatário do banco de dados fragmentado em um repositório de análise. A agregação habilita a consulta eficiente dos dados. Neste tutorial, um banco de dados do Banco de Dados SQL do Azure é usado para armazenar os dados agregados.

Nas etapas a seguir, você implanta o armazenamento da análise, que é chamado de **tenantanalytics**. Você também pode implantar tabelas predefinidas que são populadas posteriormente no tutorial:
1. No ISE do PowerShell, abra *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Defina a variável $DemoScenario no script para corresponder à sua escolha de repositório de análise. Para fins de aprendizado, é recomendável usar o banco de dados sem columnstore.
    - Para usar o Banco de Dados SQL sem columnstore, configure **$DemoScenario** = **2**
    - Para usar o Banco de Dados SQL com columnstore, configure **$DemoScenario** = **3**  
3. Pressione **F5** para executar o script de demonstração (que chama o script *Deploy-TenantAnalytics\<XX>.ps1*) que cria o repositório de análise de locatário. 

Agora que você implantou o aplicativo e o preencheu com informações de locatário interessantes, use o [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) para conectar os servidores **tenants1-mt-\<User\>** e **catalog-mt-\<User\>** usando Logon = *developer*, Senha = *P\@ssword1*.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

No Pesquisador de Objetos, execute as seguintes etapas:

1. Expanda o servidor *tenants1-mt-\<User\>* .
2. Expanda o nó Bancos de Dados e veja o banco de dados *tenants1* que contém vários locatários.
3. Expanda o servidor *catalog-mt-\<User\>* .
4. Verifique se você vê o repositório de análise e o banco de dados jobaccount.

Veja os seguintes itens de banco de dados no Pesquisador de Objetos do SSMS expandindo o nó de armazenamento de análise:

- As tabelas **TicketsRawData** e **EventsRawData** contêm dados brutos extraídos dos bancos de dados de locatário.
- As tabelas de esquema em estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates**.
- O procedimento armazenado **sp_ShredRawExtractedData** é usado para popular as tabelas de esquema em estrela das tabelas de dados brutos.

![Captura de tela mostra o Pesquisador de Objetos do S S M S do nó do repositório de análise, incluindo tabelas, exibições e nós.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos de destino 

Antes de prosseguir, verifique se implantou o banco de dados de conta de trabalho e jobaccount. No próximo conjunto de etapas, Trabalhos Elásticos são usados para extrair dados do banco de dados de locatários fragmentados e para armazenar os dados no repositório de análise. Em seguida, a segunda tarefa destrói os dados e os armazena em tabelas no esquema de estrela. Esses dois trabalhos são executados em relação a dois grupos de destino diferentes, ou seja, **TenantGroup** e **AnalyticsGroup**. O trabalho de extração é executado em relação a TenantGroup, que contém todos os bancos de dados do locatário. O trabalho de destruição é executado em relação a AnalyticsGroup, que contém o repositório de análise. Crie os grupos de destino usando as seguintes etapas:

1. No SSMS, conecte-se ao banco de dados **jobaccount** em catalog-mt-\<User\>.
2. No SSMS, abra *…\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modifique a variável @User na parte superior do script, substituindo `<User>` pelo valor de usuário usado quando você implantou o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets.
4. Pressione **F5** para executar o script que cria os dois grupos de destino.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados brutos de todos os locatários

Transações podem ocorrer com mais frequência para dados de *tíquetes e de clientes* do que para dados de *eventos e de locais*. Portanto, considere a extração de dados de tíquetes e clientes separadamente e com mais frequência do que você extrai dados de eventos e locais. Nesta seção, você define e agenda dois trabalhos separados:

- Extraia dados de tíquete e de cliente.
- Extrai dados de evento e local.

Cada trabalho extrai os dados e os envia para o repositório de análise. Existe um trabalho separado que destrói os dados extraídos para o esquema de estrela de análise.

1. No SSMS, conecte-se ao banco de dados **jobaccount** no servidor catalog-mt-\<User\>.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modifique @User na parte superior do script e substitua `<User>` pelo nome de usuário usado quando você implantou o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets. 
4. Pressione **F5** para executar o script que cria e executa o trabalho que extrai dados de tíquetes e clientes de cada banco de dados de locatário. O trabalho salva os dados para o repositório de análise.
5. Consulte a tabela TicketsRawData no banco de dados tenantanalytics para verificar se a tabela foi populada com informações de tíquetes de todos os locatários.

![A captura de tela mostra o banco de dados ExtractTickets com TicketsRawData d b o selecionado no Pesquisador de Objetos.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Repita as etapas acima, mas desta vez substitua **\ExtractTickets.sql** por **\ExtractVenuesEvents.sql** na etapa 2.

A execução com êxito do trabalho popula a tabela de EventsRawData no repositório de análise com novos eventos e informações de locais de todos os locatários. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Destruir dados extraídos para popular tabelas de esquema em estrela

A próxima etapa é fragmentar os dados brutos extraídos em um conjunto de tabelas que são otimizadas para consultas de análise. Um esquema em estrela é usado. Uma tabela de fatos central mantém registros de vendas de tíquetes individuais. As tabelas de dimensões são populadas usando dados sobre locais, eventos, clientes e datas de compra. 

Nesta seção do tutorial, você define e executa um trabalho que mescla os dados brutos extraídos aos dados nas tabelas do esquema em estrela. Depois que o trabalho de mesclagem for concluído, os dados brutos serão excluídos, deixando as tabelas prontas para serem populadas pelo próximo trabalho de extração de dados de locatário.

1. No SSMS, conecte-se ao banco de dados **jobaccount** em catalog-mt-\<User\>.
2. No SSMS, abra *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Pressione **F5** para executar o script e definir um trabalho que chama o procedimento armazenado sp_ShredRawExtractedData no repositório de análise.
4. Reserve tempo suficiente para que o trabalho seja executado com êxito.
    - Verifique a coluna **Ciclo de vida** da tabela jobs.jobs_execution para obter o status do trabalho. Verifique se o trabalho teve **Êxito** antes de continuar. Uma execução bem-sucedida exibe dados semelhantes ao seguinte gráfico:

![Captura de tela mostra o resultado bem-sucedido de executar o procedimento sp_ShredRawExtractedData.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de locatário

Os dados na tabela de esquema em estrela fornecem todos os dados de vendas de tíquetes necessários para a análise. Para que seja mais fácil ver as tendências em grandes conjuntos de dados, você precisa visualizá-las graficamente.  Nesta seção, você aprenderá a usar o **Power BI** para manipular e visualizar os dados de locatário extraídos e organizados.

Use as seguintes etapas para se conectar ao Power BI e importar os modos de exibição que você criou anteriormente:

1. Inicie o Power BI desktop.
2. Na faixa de opções Página Inicial, selecione **Obter Dados** e **Mais...**  no menu.
3. Na janela **Obter Dados**, selecione Banco de Dados SQL do Azure.
4. Na janela de logon do banco de dados, insira o nome do servidor (catalog-mt-\<User\>.database.windows.net). Selecione **Importar** para **Modo de Conectividade de Dados** e clique em OK. 

    ![Captura de tela mostra a caixa de diálogo do banco de dados do SQL Server em que é possível inserir o Servidor e o Banco de Dados.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Selecione **Banco de dados** no painel esquerdo e insira nome de usuário = *developer* e senha = *P\@ssword1*. Clique em **Conectar**.  

    ![A captura de tela mostra a caixa de diálogo do banco de dados do SQL Server em que você pode inserir um nome de usuário e uma senha.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. No painel **Navegador**, no banco de dados de análise, selecione as tabelas de esquema em estrela: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **Carregar**. 

Parabéns! Você carregou com êxito os dados no Power BI. Agora você pode começar a explorar visualizações interessantes para ajudar a obter ideias sobre os locatários. Em seguida, você vê como a análise pode permitir o fornecimento de recomendações controladas por dados para a equipe de negócios de Wingtip Tickets. As recomendações podem ajudar a otimizar a experiência de atendimento ao cliente e o modelo de negócios.

Comece analisando dados de vendas de tíquetes para ver a variação no uso entre os locais. Selecione as opções a seguir no Power BI para plotar um gráfico de barras do número total de tíquetes vendidos por cada local. Devido à variação aleatória no gerador de tíquetes, os resultados podem ser diferentes.
 
![A captura de tela mostra uma visualização do Power BI e os controles da visualização de dados no lado direito.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

A plotagem anterior confirma que o número de tíquetes vendidos por cada local varia. Locais que vendem mais tíquetes estão usando mais o serviço do que locais que vendem menos tíquetes. Pode haver uma oportunidade para ajustar a alocação de recursos de acordo com as necessidades de diferentes locatários.

Você poderá analisar melhor os dados para ver como as vendas de tíquetes variam ao longo do tempo. Selecione as opções a seguir no Power BI para plotar o número total de tíquetes vendidos por dia em um período de 60 dias.
 
![A captura de tela mostra a visualização do Power BI com título Distribuição da Venda de Ingressos vs. Dia da Venda.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior exibe esse pico de vendas de tíquetes para alguns locais. Esses picos reforçam a ideia de que alguns locais talvez estejam consumindo recursos do sistema desproporcionalmente. Até o momento, não há um padrão óbvio para indicar quando ocorrem os picos.

Em seguida, convém investigar ainda mais a importância desses dias de vendas de pico. Quando esses picos ocorrem depois que os tíquetes começam a ser vendidos? Para plotar tíquetes vendidos por dia, selecione as opções a seguir no Power BI.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

A plotagem acima mostra que alguns locais vendem muitos tíquetes no primeiro dia de venda. Assim que tíquetes estão à venda nesses locais, parece haver grande demanda. Esse aumento de atividade em alguns locais pode afetar o serviço para outros locatários.

Você pode analisar os dados novamente para ver se essa grande demanda ocorre para todos os eventos hospedados por esses locais. Em gráficos anteriores, você observou que a Contoso Concert Hall vende muitos tíquetes e que a Contoso também tem um aumento nas vendas de tíquetes em determinados dias. Experimente as opções do Power BI para plotar vendas cumulativas de tíquetes para a Contoso Concert Hall, concentrando-se nas tendências de vendas de cada um dos eventos. Todos os eventos seguem o mesmo padrão de venda?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

A plotagem anterior da Contoso Concert Hall mostra que a grande demanda não ocorre para todos os eventos. Familiarize-se com as opções de filtro para ver as tendências de vendas de outros locais.

As percepções de padrões de vendas de tíquetes podem levar o Wingtip Tickets a otimizar seu modelo de negócios. Em vez de recarregar todos os locatários igualmente, talvez Wingtip possa introduzir as camadas de serviço com diferentes tamanhos da computação. Locais maiores que precisam vender mais tíquetes por dia podem receber a oferta de uma camada superior com um SLA (contrato de nível de serviço) superior. Esses locais podem ter seus bancos de dados colocados em pool com limites de recursos maiores por banco de dados. Cada camada de serviço pode ter uma alocação de vendas por hora, com valores adicionais cobrados por exceder a alocação. Locais maiores que têm picos de vendas periódicos pode se beneficiar dos níveis mais altos e Wingtip Tickets podem monetizar seus serviços com mais eficiência.

Enquanto isso, alguns clientes de Wingtip Tickets reclamam que se esforçam para vender tíquetes suficientes para justificar o custo do serviço. Talvez essas informações ofereçam uma oportunidade de aumentar as vendas de tíquetes para locais com baixo desempenho. Vendas mais altas aumentariam o valor percebido do serviço. Clique com o botão direito do mouse em fact_Tickets e selecione **Nova medida**. Digite a seguinte expressão para a nova medida chamada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as opções de visualização a seguir para plotar os tíquetes de porcentagem vendidos em cada local para determinar o sucesso relativo.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

A plotagem acima mostra que, embora a maioria das instalações venda mais de 80% de seus tíquetes, algumas têm dificuldade para preencher mais da metade dos lugares. Familiarize-se com Values Well para selecionar a porcentagem máxima ou mínima de tíquetes vendidos para cada local.

Anteriormente, você aprofundava a análise para descobrir que as vendas de tíquetes tendem a seguir padrões previsíveis. Essa descoberta pode permitir que o Wingtip Tickets ajude locais com baixo desempenho de vendas de tíquetes a aumentar as vendas por meio de sugestões de preço dinâmicas. Essa descoberta pode revelar uma oportunidade de empregar técnicas de aprendizado de máquina para prever as vendas de tíquetes de cada evento. Previsões também podem ser feitas para o impacto sobre a receita da oferta de descontos para vendas de tíquetes. O Power BI Embedded pode ser integrado a um aplicativo de gerenciamento de eventos. A integração pode ajudar a visualizar as vendas previstas e o efeito de diferentes descontos. O aplicativo pode ajudar a planejar um ótimo desconto a ser aplicado diretamente da exibição da análise.

Você observou tendências nos dados de locatário do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets. Você pode pensar em outras maneiras pelas quais o aplicativo pode embasar decisões de negócios para fornecedores de aplicativos SaaS. Os fornecedores podem atender melhor às necessidades de seus locatários. Esperamos que este tutorial tenha equipado você com as ferramentas necessárias para executar uma análise de dados de locatário e capacitar seus negócios para a tomada de decisões orientadas a dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> - Foi implantado um banco de dados de análise de locatário com tabelas de esquema em estrela predefinidas
> - Trabalhos elásticos foram usados para extrair dados de todo o banco de dados de locatário
> - Mesclar os dados extraídos em tabelas em um esquema em estrela projetado para análise
> - Consultar um banco de dados de análise 
> - Usar o Power BI para visualização de dados para observar as tendências nos dados de locatário 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

[Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Trabalhos elásticos](./elastic-jobs-overview.md).
- [Análise entre locatários usando dados extraídos – Aplicativo de locatário único](saas-tenancy-tenant-analytics.md)