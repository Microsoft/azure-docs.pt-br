---
title: 'Aplicativo SaaS: Monitorar o desempenho de muitos bancos de dados SQL do Azure | Microsoft Docs'
description: Monitore e gerencie o desempenho dos pools e dos bancos de dados SQL do Azure em um aplicativo SaaS multilocatário
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 075d0e2471457e1a585f7fdea9b523b1d13499c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388537"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitore e gerencie o desempenho dos pools e dos bancos de dados SQL do Azure em um aplicativo SaaS multilocatário

Neste tutorial, vários dos principais cenários de gerenciamento de desempenho usados em aplicativos SaaS são explorados. São demonstrados o uso de um gerador de carga para simular a atividade em todos os bancos de dados de locatário, os recursos internos de monitoramento e alertas do Banco de Dados SQL e os pools elásticos.

O aplicativo Wingtip Tickets SaaS Banco de dados Por Locatário usa um modelo de dados de único locatário, em que cada local (locatário) tem seu próprio banco de dados. Como muitos aplicativos SaaS, o padrão esperado de carga de trabalho do locatário é imprevisível e esporádico. Em outras palavras, as vendas de ingressos podem ocorrer a qualquer momento. Para aproveitar esse padrão de uso típico do banco de dados, os bancos de dados de locatário são implantados em pools de banco de dados elásticos. Os pools elásticos otimizam o custo de uma solução ao compartilhar recursos entre vários bancos de dados. Com esse tipo de padrão, é importante monitorar o uso de recursos do banco de dados e do pool para garantir que as cargas sejam razoavelmente balanceadas entre os pools. Você também precisa garantir que os bancos de dados individuais tenham recursos adequados e que os pools não atinjam seus limites de [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Este tutorial explora maneiras para monitorar e gerenciar bancos de dados e pools e como realizar uma ação corretiva em resposta às variações na carga de trabalho.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> 
> * Simular o uso em bancos de dados de locatário executando um gerador de carga fornecido
> * Monitorar os bancos de dados de locatário conforme eles respondem ao aumento de carga
> * Escalar verticalmente o pool elástico em resposta ao aumento da carga do banco de dados
> * Provisionar um segundo pool elástico para realizar o balanceamento de carga da atividade do banco de dados


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gerenciamento de desempenho de SaaS

O gerenciamento de desempenho do banco de dados consiste na compilação e análise de dados de desempenho e, em seguida, reagir a esses dados, ajustando parâmetros para manter um tempo de resposta aceitável para o seu aplicativo. Ao hospedar vários locatários, os pools elásticos são uma maneira econômica de fornecer e gerenciar os recursos de um grupo de bancos de dados com cargas de trabalho imprevisíveis. Com determinados padrões carga de trabalho, não mais que dois bancos de dados S3 poderiam se beneficiar sendo gerenciados em um pool.

![diagrama de aplicativo](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Os pools e os bancos de dados nos pools, devem ser monitorados para garantir que permaneçam dentro dos intervalos de desempenho aceitáveis. Ajuste a configuração do pool para atender às necessidades da carga de trabalho agregada de todos os bancos de dados, garantindo que os eDTUs do pool são adequados à carga de trabalho geral. Ajuste os valores de eDTU mínimo por banco de dados e máximo por banco de dados com valores apropriados para os requisitos específicos do seu aplicativo.

### <a name="performance-management-strategies"></a>Estratégias de gerenciamento de desempenho

* Para evitar precisar monitorar o desempenho manualmente, é mais eficaz **definir alertas que são disparados quando os bancos de dados ou os pools se desviam dos intervalos normais**.
* Para responder às flutuações de curto prazo no tamanho da computação agregado de um pool, o **nível de eDTU do pool pode ser aumentado ou reduzido**. Se esse flutuação ocorre de maneira regular ou previsível, **o dimensionamento do pool pode ser agendado para ocorrer automaticamente**. Por exemplo, reduzir verticalmente quando você souber que sua carga de trabalho é leve, talvez durante a noite ou durante os finais de semana.
* Para responder a flutuações de longo prazo ou alterações no número de bancos de dados, **os bancos de dados individuais podem ser movidos para outros pools**.
* Para responder a aumentos de curto prazo na carga de um banco de dados *individual*, **bancos de dados individuais podem ser retirados de um pool e serem atribuídos a um tamanho da computação individual**. Depois que a carga for reduzida, o banco de dados poderá ser retornado ao pool. Quando isso é conhecido com antecedência, os bancos de dados podem ser movidos preventivamente para garantir que o banco de dados sempre tenha os recursos necessários e para evitar o impacto em outros bancos de dados no pool. Se esse requisito for previsível, como um local com um crescimento súbito nas vendas de ingressos para um evento popular, esse comportamento de gerenciamento poderá ser integrado ao aplicativo.

O [Portal do Azure](https://portal.azure.com) fornece monitoramento e alertas internos sobre a maioria dos recursos. Para o Banco de Dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados e pools. Esse monitoramento e alertas internos são específicos ao recurso e, portanto, é conveniente usá-los para pequenas quantidades de recursos, mas não são muito convenientes ao trabalhar com muitos recursos.

Para cenários de alto volume, em que você está trabalhando com muitos recursos, [registra em log do Azure Monitor](saas-dbpertenant-log-analytics.md) pode ser usado. Isso é um serviço do Azure separado que fornece análise sobre logs de diagnóstico emitidos e telemetria coletados em um espaço de trabalho do Log Analytics. Os logs do Azure Monitor podem coletar a telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Provisionar locatários adicionais

Embora os pools possam ser econômicos com apenas dois bancos de dados S3, quanto mais bancos de dados estiverem no pool, mais econômico será o efeito de média. Para uma boa compreensão do funcionamento do monitoramento de desempenho e do gerenciamento em grande escala, este tutorial requer que você tenha pelo menos 20 bancos de dados implantados.

Se você já provisionou um lote de locatários em um tutorial anterior, vá para a seção [Simular o uso em todos os bancos de dados de locatário](#simulate-usage-on-all-tenant-databases).

1. No **ISE do PowerShell**, abra... \\Módulos de Aprendizado\\Monitoramento e Gerenciamento de Desempenho\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto pois você executará vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Provisionar um lote de locatários**
1. Pressione **F5** para executar o script.

O script implantará 17 locatários em menos de cinco minutos.

O script *New-TenantBatch* usa um conjunto aninhado ou vinculado de modelos do [Resource Manager](../azure-resource-manager/index.yml) que cria um lote de locatários, que, por padrão, copia o banco de dados **basetenantdb** no servidor de catálogo para criar os novos bancos de dados de locatário. Em seguida, ele os registra no catálogo e, por fim, inicializa-os com o nome de locatário e o tipo de local. Isso é consistente com a maneira como o aplicativo WTP provisiona um locatário novo. As alterações feitas no *basetenantdb* são aplicadas a todos os novos locatários provisionados daí em diante. Consulte o [tutorial Gerenciamento do Esquema](saas-tenancy-schema-management.md) para saber como fazer alterações de esquema em bancos de dados de locatário *existentes* (incluindo o banco de dados *basetenantdb*).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular o uso em todos os bancos de dados de locatário

É fornecido o script *Demo-PerformanceMonitoringAndManagement.ps1*, que simula uma carga de trabalho em execução em todos os bancos de dados de locatário. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTU) |
| 3 | Gerar carga com picos mais longos e mais frequentes por banco de dados|
| 4 | Gerar carga com picos maiores de DTU por banco de dados (aproximadamente 80 DTU)|
| 5 | Gerar uma carga normal e uma carga alta em um único locatário (aproximadamente 95 DTU)|
| 6 | Gerar carga desbalanceada entre vários pools|

O gerador de carga aplica uma carga *sintética* somente da CPU em cada banco de dados de locatário. O gerador inicia um trabalho para cada banco de dados de locatário, que chama um procedimento armazenado que gera a carga periodicamente. Os níveis de carga (em eDTUs), a duração e os intervalos variam em todos os bancos de dados, simulando uma atividade de locatário imprevisível.

1. No **ISE do PowerShell**, abra... \\Módulos de Aprendizado\\Monitoramento e Gerenciamento de Desempenho\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto pois você executará vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **2**, *Gerar uma carga de intensidade normal*.
1. Pressione **F5** para aplicar uma carga em todos os seus bancos de dados de locatário.

O aplicativo de banco de dados multilocatário SaaS Wingtip Tickets é um aplicativo SaaS, e a carga real em um aplicativo SaaS normalmente é esporádica e imprevisível. Para simular isso, o gerador de carga gera uma carga aleatória distribuída entre todos os locatários. São necessários vários minutos para que o padrão de carga surja. Portanto execute o gerador de carga durante 3 a 5 minutos antes de tentar monitorar a carga nas próximas seções.

> [!IMPORTANT]
> O gerador de carga está sendo executado como uma série de trabalhos em sua sessão local do PowerShell. Mantenha a guia *Demo-PerformanceMonitoringAndManagement.ps1* aberta! Se você fechar a guia ou suspender seu computador, o gerador de carga será interrompido. O gerador de carga permanece em um estado de *invocação do trabalho*, no qual gera a carga em todos novos locatários que são provisionados depois que ele é iniciado. Use *Ctrl-C* para interromper a invocação de novos trabalhos e sair do script. O gerador de carga continuará em execução, mas apenas nos locatários existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorar o uso de recursos usando o portal do Azure

Para monitorar o uso de recursos que resulta da carga que está sendo aplicada, abra o portal no pool que contém os bancos de dados de locatário:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o servidor *tenants1-&lt;USER&gt;*.
1. Role para baixo e localize os pools elásticos e clique em **Pool1**. Este pool contém todos os bancos de dados de locatário criados até agora.

Observe os gráficos **Monitoramento do pool elástico** e **Monitoramento do banco de dados elástico**.

A utilização de recursos do pool é a utilização de agregação de banco de dados para todos os bancos de dados no pool. O gráfico de banco de dados mostra os cinco bancos de dados mais ativos:

![gráfico de banco de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Como há outros bancos de dados no pool além dos cinco mais utilizados, a utilização do pool mostra a atividade que não está refletida no gráfico dos cinco bancos de dados mais utilizados. Para obter mais detalhes, clique em **Utilização de Recursos do Banco de Dados**:

![utilização de recursos do banco de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho para o pool

Defina um alerta no pool que é disparado após \>75% de utilização, da seguinte maneira:

1. Abra *Pool1* (no servidor *tenants1-\<usuário\>*) no [Portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta** e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, como **Alto DTU**,
1. Defina os seguintes valores:
   * **Métrica = percentual de eDTU**
   * **Condição = maior que**
   * **Limite = 75**
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de email à caixa *Emails de administrador adicionais* e clique em **OK**.

   ![definir alerta](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Escalar verticalmente um pool ocupado

Se o nível de carga agregado aumentar em um pool até o seu limite e alcançar 100% de uso de eDTU, o desempenho individual do banco de dados será afetado, reduzindo potencialmente os tempos de resposta de consulta de todos os bancos de dados no pool.

A **Curto prazo**, considere a possibilidade de escalar verticalmente o pool para fornecer recursos adicionais ou remover bancos de dados do pool (movê-los para outros pools ou para fora do pool, para uma camada de serviço autônoma).

A **longo prazo**, considere a possibilidade de otimizar o uso de consultas ou do índice para melhorar o desempenho do banco de dados. Dependendo da sensibilidade do aplicativo a problemas de desempenho, é uma melhor prática escalar verticalmente um pool antes que ele alcance 100% de uso de eDTU. Use um alerta para avisá-lo com antecedência.

Você pode simular um pool ocupado aumentando a carga produzida pelo gerador. Fazendo com que os bancos de dados fiquem intermitentes com mais frequência e por mais tempo, aumentando a carga agregada no pool sem alterar os requisitos dos bancos de dados individuais. O portal ou o PowerShell podem ser usados para escalar verticalmente o pool facilmente. Este exercício usa o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais longos e mais frequentes por banco de dados_, para aumentar a intensidade da carga agregada no pool sem alterar a carga de pico necessária a cada banco de dados.
1. Pressione **F5** para aplicar uma carga em todos os seus bancos de dados de locatário.

1. Acesse **Pool1** no Portal do Azure.

Monitore o aumento no uso de eDTUs do pool no gráfico superior. São necessários alguns minutos para que a nova carga mais alta seja exibida, mas você deverá ver rapidamente o pool começar a atingir a utilização máxima e, conforme a carga se estabiliza no novo padrão, ela rapidamente sobrecarrega o pool.

1. Para escalar verticalmente o pool, clique em **Configurar pool** na parte superior da página **Pool1**.
1. Ajuste a configuração **eDTU do Pool** para **100**. Alterar o eDTU do pool não altera as configurações por banco de dados (que ainda é, no máximo, de 50 eDTU por banco de dados). Veja as configurações por banco de dados no lado direito da página **Configurar pool**.
1. Clique em **Salvar** para enviar a solicitação para dimensionar o pool.

Volte para **Pool1** > **Visão Geral** para exibir os gráficos de monitoramento. Monitore o efeito do fornecimento de mais recursos ao pool (embora com poucos bancos de dados e uma carga aleatória não seja sempre fácil observar conclusivamente, até a execução durante um período). Enquanto estiver examinando os gráficos lembre-se que 100% no gráfico superior agora representa 100 eDTUs, enquanto que no gráfico inferior, 100% ainda representa 50 eDTUs, pois o máximo por banco de dados ainda é de 50 eDTUs.

Os bancos de dados permanecem online e totalmente disponíveis durante todo o processo. No último momento, como cada banco de dados está pronto para ser habilitado com a nova eDTU de pool, as conexões ativas serão interrompidas. O código do aplicativo sempre deve ser escrito para repetir conexões interrompidas e, assim, ser reconectado ao banco de dados no pool escalonado verticalmente.

## <a name="load-balance-between-pools"></a>Balancear carga entre pools

Como alternativa a escalar verticalmente o pool, crie um segundo pool e mova os bancos de dados para balancear a carga entre os dois pools. Para fazer isso, o novo pool deve ser criado no mesmo servidor que o primeiro.

1. No [portal do Azure](https://portal.azure.com), abra o servidor **tenants1-dpt&lt;USER&gt;**.
1. Clique em **+ Novo pool** para criar um pool no servidor atual.
1. No modelo **Pool elástico**:

   1. Defina **Nome** como *Pool2*.
   1. Deixe o tipo de preço como **Pool Standard**.
   1. Clique em **Configurar pool**,
   1. Defina **eDTU do Pool** como *50 eDTUs*.
   1. Clique em **Adicionar bancos de dados** para ver uma lista de bancos de dados no servidor que podem ser adicionados ao *Pool2*.
   1. Selecione 10 bancos de dados aleatórios para movê-los para o novo pool e, em seguida, clique em **Selecionar**. Se você estiver executando o gerador de carga, o serviço já reconhecerá que o perfil de desempenho exige um pool maior que o tamanho padrão de 50 eDTUs e recomendará que você comece com uma configuração de 100 eDTUs.

      ![recomendação](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Para este tutorial, deixe o padrão em 50 eDTUs e clique em **Selecionar** novamente.
   1. Selecione **OK** para criar um novo pool e para mover os bancos de dados selecionados para ele.

A criação do pool e a movimentação dos bancos de dados leva apenas alguns minutos. Conforme os bancos de dados são movidos, eles permanecem online e totalmente acessíveis até o último momento, quando todas as conexões abertas são fechadas. Desde que você tenha uma lógica de repetição, os clientes se conectarão ao banco de dados no novo pool.

Procure **Pool2** (no servidor *tenants1-dpt-\<user\>*) para abrir o pool e monitorar seu desempenho. Se ele não estiver visível, aguarde até que o provisionamento do novo pool seja concluído.

Agora você vê que o uso de recursos do *Pool1* caiu e que o *Pool2* é carregado de maneira semelhante.

## <a name="manage-performance-of-an-individual-database"></a>Gerenciar o desempenho de um banco de dados individual

Se um banco de dados individual em um pool apresentar uma alta carga constante, dependendo da configuração do pool, ele poderá ter a tendência de dominar os recursos do pool e afetar outros bancos de dados. Se for provável que a atividade continuará por algum tempo, o banco de dados poderá ser movido temporariamente para fora do pool. Isso permite que o banco de dados tenha os recursos extras necessários e seja isolado dos outros bancos de dados.

Este exercício simula o efeito do Contoso Concert Hall experimentando uma alta carga quando os ingressos de um concerto popular são disponibilizados para a venda.

1. No **PowerShell ISE**, abra o script \\*Demo-PerformanceMonitoringAndManagement.ps1*.
1. Definir **$DemoScenario = 5, gerar uma carga normal e uma carga alta em um único locatário (aproximadamente 95 DTU).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script usando **F5**.


1. No [Portal do Azure](https://portal.azure.com) navegue até a lista de bancos de dados no servidor *tenants1-dpt-\<user\>*. 
1. Clique no banco de dados **contosoconcerthall**.
1. Clique no pool em que **contosoconcerthall** se encontra. Encontre o pool na seção **Pool elástico**.

1. Inspecione o gráfico **Monitoramento do pool elástico** e procure o aumento do uso de eDTUs do pool. Após um ou dois minutos, a carga maior deverá começar a fazer efeito e rapidamente você verá que o pool atinge 100% de utilização.
2. Inspecione a exibição **Monitoramento do banco de dados elástico** que mostra os bancos de dados mais ativos na última hora. O banco de dados *contosoconcerthall* logo deve aparecer como um dos cinco bancos de dados mais ativos.
3. **Clique no** **gráfico** Monitoramento do banco de dados elástico e ele abrirá a página **Utilização de Recursos do Banco de Dados**, em que você pode monitorar um dos bancos de dados. Isso permite isolar a exibição do banco de dados *contosoconcerthall*.
4. Na lista de bancos de dados, clique em **contosoconcerthall**.
5. Clique em **Tipo de Preço (DTUs em escala)** para abrir a página **Configurar desempenho**, na qual é possível definir um tamanho da computação para o banco de dados.
6. Clique na guia **Standard** para abrir as opções de escala na camada Standard.
7. Deslize o **controle deslizante de DTU** para a direita para selecionar **100** DTUs. Observe que isso corresponde ao objetivo de serviço **S3**.
8. Clique em **Aplicar** para mover o banco de dados para fora do pool e torná-lo um banco de dados *Standard S3*.
9. Depois que o dimensionamento for concluído, monitore o efeito no banco de dados contosoconcerthall e no Pool1 nas folhas do pool elástico e do banco de dados.

Depois que a carga alta no banco de dados contosoconcerthall diminuir, você deverá devolva-o imediatamente ao pool para reduzir seu custo. Se não estiver claro quando isso acontecerá, você poderá definir um alerta no banco de dados que será disparado quando o uso de DTU cair para abaixo do valor máximo por banco de dados no pool. Mover um banco de dados para um pool é descrito no exercício 5.

## <a name="other-performance-management-patterns"></a>Outros padrões de gerenciamento de desempenho

**Dimensionamento preemptivo** No exercício acima, em que você explorou como dimensionar um banco de dados isolado, você sabia qual banco de dados procurar. Se o gerenciamento do Contoso Concert Hall tivesse informado o Wingtips sobre a venda iminente de ingressos, o banco de dados poderia ter sido movido para fora do pool preventivamente. Caso contrário, ele provavelmente exigiria um alerta no pool ou no banco de dados para observar o que estava acontecendo. Você não gostaria de saber sobre isso por meio dos outros locatários do pool reclamando a respeito de degradação do desempenho. E se o locatário puder prever quanto tempo ele precisará de recursos adicionais você poderá configurar um runbook de Automação do Azure para mover o banco de dados para fora do pool e, em seguida, retorná-lo em um agendamento definido.

**Dimensionamento de autoatendimento de locatário** Como o dimensionamento é uma tarefa facilmente chamada por meio da API de gerenciamento, você pode criar a capacidade de dimensionar bancos de dados de locatário em seu aplicativo voltado para o locatário e oferecê-lo como um recurso do seu serviço SaaS. Por exemplo, permita que os locatários autoadministrem o escalonamento e a redução vertical, talvez vinculado diretamente à cobrança deles!

**Escalando e reduzindo verticalmente um pool de acordo com um agendamento para corresponder aos padrões de uso**

Quando o uso agregado do locatário segue padrões de uso previsíveis, você pode usar a Automação do Azure para escalar e reduzir um pool verticalmente em um agendamento. Por exemplo, reduzir um pool verticalmente após as 18h e escalar verticalmente novamente antes das 6h em dias da semana nos quais você sabe que há uma queda nos requisitos de recursos.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Simular o uso em bancos de dados de locatário executando um gerador de carga fornecido
> * Monitorar os bancos de dados de locatário conforme eles respondem ao aumento de carga
> * Escalar verticalmente o pool elástico em resposta ao aumento da carga do banco de dados
> * Provisionar um segundo pool elástico para realizar o balanceamento de carga da atividade do banco de dados

[Tutorial para restaurar um locatário único](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais que compilam a implantação do aplicativo Banco de Dados por Locatário SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) adicionais
* [Pools elásticos do SQL](sql-database-elastic-pool.md)
* [Automação do Azure](../automation/automation-intro.md)
* [Os logs do Azure Monitor](saas-dbpertenant-log-analytics.md) – configuração e uso tutorial de logs do Azure Monitor
