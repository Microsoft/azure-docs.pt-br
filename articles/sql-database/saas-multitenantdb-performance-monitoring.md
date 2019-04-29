---
title: Monitorar o desempenho de um banco de dados SQL do Azure multilocatário fragmentado em um aplicativo SaaS multilocatário | Microsoft Docs
description: Monitorar e gerenciar o desempenho do banco de dados SQL do Azure multilocatário fragmentado em um aplicativo SaaS multilocatário
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
ms.openlocfilehash: be7dbe35800bbe911bc56d1883462534a16499a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485321"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorar e gerenciar o desempenho do banco de dados SQL do Azure multilocatário fragmentado em um aplicativo SaaS multilocatário

Neste tutorial, vários dos principais cenários de gerenciamento de desempenho usados em aplicativos SaaS são explorados. São demonstrados o uso de um gerador de carga para simular a atividade em bancos de dados multilocatário fragmentados, os recursos internos de monitoramento e os alertas do Banco de Dados SQL.

O aplicativo de banco de dados multilocatário SaaS Wingtip Tickets usa um modelo de dados multilocatário fragmentado, em que os dados de local (locatário) são distribuídos por ID de locatário potencialmente em vários bancos de dados. Como muitos aplicativos SaaS, o padrão esperado de carga de trabalho do locatário é imprevisível e esporádico. Em outras palavras, as vendas de ingressos podem ocorrer a qualquer momento. Para tirar vantagem desse padrão de uso típico de banco de dados, os bancos de dados podem ser escalados e reduzidos verticalmente para otimizar o custo de uma solução. Com esse tipo de padrão, é importante monitorar o uso de recursos de banco de dados para garantir que as cargas são razoavelmente balanceadas em potencialmente vários bancos de dados. Você também precisa garantir que os bancos de dados individuais tenham recursos adequados e não atinjam seus limites de [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Este tutorial explora maneiras para monitorar e gerenciar bancos de dados e como realizar uma ação corretiva em resposta às variações na carga de trabalho.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> 
> * Simular o uso em um banco de dados multilocatário fragmentado executando um gerador de carga fornecido
> * Monitorar o banco de dados à medida que ele responde ao aumento de carga
> * Dimensionar o banco de dados em resposta ao aumento de carga do banco de dados
> * Provisionar um locatário em um banco de dados de locatário único

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado. Para implantá-lo em menos de cinco minutos, confira [Implantar e explorar o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gerenciamento de desempenho de SaaS

O gerenciamento de desempenho do banco de dados consiste na compilação e análise de dados de desempenho e, em seguida, reagir a esses dados, ajustando parâmetros para manter um tempo de resposta aceitável para o seu aplicativo. 

### <a name="performance-management-strategies"></a>Estratégias de gerenciamento de desempenho

* Para evitar precisar monitorar o desempenho manualmente, é mais eficaz **definir alertas que são disparados quando os bancos de dados se desviam dos intervalos normais**.
* Para responder a flutuações de curto prazo no tamanho da computação de um banco de dados, o **nível de DTU pode ser escalado ou reduzido verticalmente**. Se essa flutuação acontecer de maneira regular ou previsível, **o dimensionamento do banco de dados poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduzir verticalmente quando você souber que sua carga de trabalho é leve, talvez durante a noite ou durante os finais de semana.
* Para responder a flutuações de longo prazo ou a alterações nos locatários, **os locatários individuais podem ser movidos para outro banco de dados**.
* Para responder a aumentos de curto prazo em cargas de locatários *individuais*, **os locatários individuais podem ser retirados de um banco de dados e atribuídos a um tamanho da computação individual**. Depois que a carga é reduzida, o locatário pode ser devolvido ao banco de dados multilocatário. Quando isso é conhecido com antecedência, os locatários podem ser movidos preventivamente para garantir que o banco de dados sempre tenha os recursos necessários e para evitar o impacto em outros locatários no banco de dados multilocatário. Se esse requisito for previsível, como um local com um crescimento súbito nas vendas de ingressos para um evento popular, esse comportamento de gerenciamento poderá ser integrado ao aplicativo.

O [Portal do Azure](https://portal.azure.com) fornece monitoramento e alertas internos sobre a maioria dos recursos. Para o Banco de Dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados. Esse monitoramento e alertas internos são específicos ao recurso e, portanto, é conveniente usá-los para pequenas quantidades de recursos, mas não são convenientes ao trabalhar com muitos recursos.

Para cenários de alto volume, em que você está trabalhando com muitos recursos, [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) pode ser usado. Isso é um serviço do Azure separado que fornece análise sobre logs de diagnóstico emitidos e telemetria coletados em um espaço de trabalho do Log Analytics. Os logs do Azure Monitor podem coletar a telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Provisionar locatários adicionais

Para obter um bom entendimento de como funciona o monitoramento de desempenho e gerenciamento em escala, este tutorial requer que você tenha vários locatários em um banco de dados multilocatário fragmentado.

Se você já provisionou um lote de locatários em um tutorial anterior, vá para a seção [Simular o uso em todos os bancos de dados de locatário](#simulate-usage-on-all-tenant-databases).

1. No **ISE do PowerShell**, abra... \\Módulos de Aprendizado\\Monitoramento e Gerenciamento de Desempenho\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto pois você executará vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, _Provisionar um lote de locatários_
1. Pressione **F5** para executar o script.

O script implanta 17 locatários no banco de dados multilocatário em poucos minutos. 

O script *New-TenantBatch* cria novos locatários com chaves de locatário exclusivas no banco de dados multilocatário fragmentado e os inicializa com o nome do locatário e o tipo de local. Isso é consistente com a maneira como o aplicativo WTP provisiona um locatário novo. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular o uso em todos os bancos de dados de locatário

É fornecido o script *Demo-PerformanceMonitoringAndManagement.ps1*, que simula uma carga de trabalho em execução no banco de dados de locatário. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 30 DTU) |
| 3 | Gerar carga com intermitências mais longas por locatário|
| 4 | Gerar carga com picos maiores de DTU por locatário (aproximadamente 70 DTUS)|
| 5 | Gerar uma alta intensidade (aproximadamente 90 DTUS) em um único locatário e uma carga de intensidade normal em todos os outros locatários |

O gerador de carga aplica uma carga *sintética* somente da CPU em cada banco de dados de locatário. O gerador inicia um trabalho para cada banco de dados de locatário, que chama um procedimento armazenado que gera a carga periodicamente. Os níveis de carga (em DTUs), a duração e os intervalos variam em todos os bancos de dados, simulando uma atividade de locatário imprevisível.

1. No **ISE do PowerShell**, abra... \\Módulos de Aprendizado\\Monitoramento e Gerenciamento de Desempenho\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto pois você executará vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **2**, _Gerar uma carga de intensidade normal_
1. Pressione **F5** para aplicar uma carga a todos os locatários.

O aplicativo de banco de dados multilocatário SaaS Wingtip Tickets é um aplicativo SaaS, e a carga real em um aplicativo SaaS normalmente é esporádica e imprevisível. Para simular isso, o gerador de carga gera uma carga aleatória distribuída entre todos os locatários. São necessários vários minutos para que o padrão de carga surja. Portanto execute o gerador de carga durante 3 a 5 minutos antes de tentar monitorar a carga nas próximas seções.

> [!IMPORTANT]
> O gerador de carga está sendo executado como uma série de trabalhos em uma nova janela do PowerShell. Se você fechar a sessão, o gerador de carga será interrompido. O gerador de carga permanece em um estado de *invocação do trabalho*, no qual gera a carga em todos novos locatários que são provisionados depois que ele é iniciado. Use *Ctrl-C* para interromper a invocação de novos trabalhos e sair do script. O gerador de carga continuará em execução, mas apenas nos locatários existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorar o uso de recursos usando o portal do Azure

Para monitorar o uso de recursos que resulta da carga que está sendo aplicada, abra o portal para o banco de dados multilocatário, **tenants1**, que contém os locatários:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até o servidor *tenants1-mt-&lt;USER&gt;*.
1. Role para baixo, localize os bancos de dados e clique em **tenants1**. Esse banco de dados multilocatário fragmentado contém todos os locatários criados até o momento.

![gráfico de banco de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observe o gráfico de **DTU**.

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho no banco de dados

Defina um alerta no banco de dados que é disparado após \>75% de utilização, da seguinte maneira:

1. Abra o banco de dados *tenants1* (no servidor *tenants1-mt-&lt;USER&gt;*) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta** e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, como **Alto DTU**,
1. Defina os seguintes valores:
   * **Métrica = porcentagem de DTU**
   * **Condição = maior que**
   * **Limite = 75**.
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de email à caixa *Emails de administrador adicionais* e clique em **OK**.

   ![definir alerta](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Escalar verticalmente um banco de dados ocupado

Se o nível de carga aumentar em um banco de dados até o ponto em que maximizar o banco de dados e atingir 100% de utilização de DTU, o desempenho do banco de dados será afetado, potencialmente reduzindo os tempos de resposta de consulta.

Em **curto prazo**, considere escalar verticalmente o banco de dados para fornecer recursos adicionais ou remover locatários do banco de dados multilocatário (movê-los do banco de dados multilocatário para um banco de dados autônomo).

A **longo prazo**, considere a possibilidade de otimizar o uso de consultas ou do índice para melhorar o desempenho do banco de dados. Dependendo da sensibilidade do aplicativo a problemas de desempenho, é uma melhor prática escalar verticalmente um banco de dados antes que ele alcance 100% de uso de DTU. Use um alerta para avisá-lo com antecedência.

Você pode simular um banco de dados ocupado aumentando a carga produzida pelo gerador. Fazendo com que os locatários fiquem intermitentes com mais frequência e por mais tempo, aumentando a carga no banco de dados multilocatário sem alterar os requisitos de locatários individuais. O portal ou o PowerShell podem ser usados para escalar verticalmente o banco de dados facilmente. Este exercício usa o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais longos e mais frequentes por banco de dados_, para aumentar a intensidade da carga agregada no banco de dados sem alterar a carga de pico necessária a cada locatário.
1. Pressione **F5** para aplicar uma carga em todos os seus bancos de dados de locatário.
1. Acesse o banco de dados **tenants1** no portal do Azure.

Monitore o maior uso de DTU de banco de dados no gráfico superior. São necessários alguns minutos para que a nova carga mais alta seja exibida, mas você deverá ver rapidamente o banco de dados começar a atingir a utilização máxima e, conforme a carga se estabiliza no novo padrão, ela rapidamente sobrecarrega o banco de dados.

1. Para escalar verticalmente o banco de dados, clique em **Camada de preços (dimensionar DTUs)** na folha de configurações.
1. Ajuste o **DTU** definindo como **100**. 
1. Clique em **Aplicar** para enviar a solicitação para dimensionar o banco de dados.

Volte para **tenants1** > **Visão geral** para exibir os gráficos de monitoramento. Monitore o efeito do fornecimento de mais recursos ao banco de dados (embora com poucos locatários e uma carga aleatória não seja sempre fácil observar conclusivamente, até a execução durante um período). Ao observar os gráficos, lembre-se de que 100% no gráfico superior agora representa 100 DTUs, enquanto no gráfico inferior 100% ainda corresponde a 50 DTUs.

Os bancos de dados permanecem online e totalmente disponíveis durante todo o processo. O código do aplicativo sempre deve ser escrito para repetir conexões interrompidas e, assim, reconectar ao banco de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisionar um novo locatário em seu próprio banco de dados 

O modelo multilocatário fragmentado permite que você escolha se deseja provisionar um novo locatário em um banco de dados multilocatário junto com outros locatários ou provisionar o locatário em um banco de dados separado. Com o provisionamento de um locatário em seu próprio banco de dados, ele se beneficia do isolamento inerente ao banco de dados separado, o que permite gerenciar o desempenho do locatário independentemente de outros, restaurá-lo independentemente de outros etc. Por exemplo, você pode colocar clientes regulares ou de avaliação gratuita em um banco de dados multilocatário e clientes premium em bancos de dados individuais.  Se bancos de dados de locatário único isolados forem criados, ainda poderão ser gerenciados coletivamente em um pool elástico para otimizar os custos de recursos.

Se você já provisionou um novo locatário em seu próprio banco de dados, ignore as próximas etapas.

1. No **ISE do PowerShell**, abra... \\Módulos de Aprendizado\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Modifique **$TenantName = "Salix Salsa"** e **$VenueType  = "dance"**
1. Defina **$Scenario** = **2**, _Provisionar um locatário em um novo banco de dados de locatário único_
1. Pressione **F5** para executar o script.

O script provisionará esse locatário em um banco de dados separado, registrará o banco de dados e o locatário no catálogo e abrirá a página Eventos do locatário no navegador. Atualize a página Hub de Eventos e você verá que "Salix Salsa" foi adicionado como um local.

## <a name="manage-performance-of-an-individual-database"></a>Gerenciar o desempenho de um banco de dados individual

Se um único locatário em um banco de dados multilocatário enfrentar uma alta carga contínua, ele poderá tender a dominar os recursos de banco de dados e afetar outros locatários no mesmo banco de dados. Se for provável que a atividade continue por algum tempo, o locatário poderá ser movido temporariamente para fora do banco de dados e para seu próprio banco de dados de locatário único. Isso permite que o locatário tenha os recursos extras necessárias e o isola completamente dos outros locatários.

Este exercício simula o efeito em que Salix Salsa enfrenta uma alta carga quando tíquetes são colocados à venda para um evento popular.

1. Abra o script …\\*Demo-PerformanceMonitoringAndManagement.ps1*.
1. Definir **$DemoScenario = 5**, _gerar uma carga normal e uma carga alta em um único locatário (aproximadamente 90 DTUS)._
1. Defina **$SingleTenantName = Salix Salsa**
1. Execute o script usando **F5**.

Acesse o portal e navegue até **salixsalsa** > **Visão geral** para exibir os gráficos de monitoramento. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gerenciamento de desempenho

**Dimensionamento de autoatendimento de locatário**

Como o dimensionamento é uma tarefa facilmente chamada por meio da API de gerenciamento, você pode criar a capacidade de dimensionar bancos de dados de locatário em seu aplicativo voltado para o locatário e oferecê-lo como um recurso do seu serviço SaaS. Por exemplo, permita que os locatários autoadministrem o escalonamento e a redução vertical, talvez vinculado diretamente à cobrança deles!

**Como escalar e reduzir verticalmente um banco de dados de acordo com um agendamento para corresponder aos padrões de uso**

Quando o uso agregado do locatário segue padrões de uso previsíveis, você pode usar a Automação do Azure para escalar e reduzir um banco de dados verticalmente em um agendamento. Por exemplo, reduzir um banco de dados verticalmente após as 18h e escalar verticalmente novamente antes das 6h em dias da semana nos quais você sabe que há uma queda nos requisitos de recursos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Simular o uso em um banco de dados multilocatário fragmentado executando um gerador de carga fornecido
> * Monitorar o banco de dados à medida que ele responde ao aumento de carga
> * Dimensionar o banco de dados em resposta ao aumento de carga do banco de dados
> * Provisionar um locatário em um banco de dados de locatário único

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automação do Azure](../automation/automation-intro.md)