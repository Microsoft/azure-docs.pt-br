---
title: "Comparação de produtos de monitoramento da Microsoft | Microsoft Docs"
description: "O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece links para o conteúdo detalhado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>Comparação de produtos de monitoramento da Microsoft
Este artigo fornece uma comparação entre o SCOM (System Center Operations Manager) e o Log Analytics no OMS (Operations Management Suite) em relação à arquitetura, a lógica usada para monitorar os recursos e a forma como realizam a análise dos dados coletados.  O objetivo é fornecer um entendimento básico de suas diferenças e seus pontos fortes relativos.  

## <a name="basic-architecture"></a>Arquitetura básica
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Todos os componentes do SCOM estão instalados em seu data center.  [agentes são instalados](http://technet.microsoft.com/library/hh551142.aspx) em computadores com Windows e Linux que são gerenciados pelo SCOM.  Os agentes se conectam ao [Servidores de Gerenciamento](https://technet.microsoft.com/library/hh301922.aspx) que se comunicam com o banco de dados e o data warehouse do SCOM.  Os agentes dependem da autenticação de domínio para se conectarem aos servidores de gerenciamento.  Aqueles que estão fora de um domínio confiável podem realizar uma autenticação de certificado ou se conectarem a um [Servidor de Gateway](https://technet.microsoft.com/library/hh212823.aspx).

O SCOM requer dois bancos de dados SQL, um para os dados operacionais e outro data warehouse para dar suporte aos relatórios e à análise de dados.  Um [Reporting Server](https://technet.microsoft.com/library/hh298611.aspx) executa o SQL Reporting Services para gerar relatórios sobre os dados do data warehouse. 

O SCOM pode monitorar recursos de nuvem usando pacotes de gerenciamento para produtos como [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708) e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Esses pacotes de gerenciamento usam um ou mais agentes locais como proxies para a descoberta de recursos de nuvem e fluxos de trabalho em execução para medir seu desempenho e sua disponibilidade.  Os agentes proxy também são usados para [monitorar dispositivos de rede](https://technet.microsoft.com/library/hh212935.aspx) e outros recursos externos.

O Console de Operações é um aplicativo do Windows que se conecta a um dos servidores de gerenciamento e permite ao administrador exibir e analisar os dados coletados e configurar o ambiente do SCOM.  Um console baseado na Web pode ser hospedado em qualquer servidor do IIS e fornece a análise de dados por meio de um navegador.

![Arquitetura do SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
A maioria dos componentes do OMS está na nuvem do Azure e, portanto, é possível implantá-los e gerenciá-los com um mínimo custo e esforço administrativo.  Todos os dados coletados pelo Log Analytics são armazenados no repositório do OMS.

O Log Analytics pode coletar dados de uma dessas três fontes:

* Máquinas virtuais e físicas que executam o Windows e o [MMA (Microsoft Monitoring Agent)](https://technet.microsoft.com/library/mt484108.aspx) ou o Linux e o [Operations Management Suite Agent para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Esses computadores podem ser locais ou máquinas virtuais do Azure ou de outra nuvem.
* Uma conta do Armazenamento do Azure com os dados do [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) coletados por uma função de trabalho, função web ou máquina virtual do Azure.
* [Conexão a um grupo de gerenciamento do SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Nessa configuração, os agentes se comunicam com os servidores de gerenciamento do SCOM que fornecem os dados ao banco de dados do SCOM, em que são entregues ao armazenamento de dados do OMS.
  Os administradores analisam os dados coletados e configuram o Log Analytics com o portal do OMS, que está hospedado no Azure e pode ser acessado por meio de qualquer navegador.  Os aplicativos móveis para acesso desses dados estão disponíveis para as plataformas padrão.

![Arquitetura do Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integração do SCOM e do Log Analytics
Quando o SCOM é usado como uma fonte de dados para o Log Analytics, é possível aproveitar os recursos de ambos os produtos em um ambiente de monitoramento híbrido.  Você pode configurar os agentes do SCOM existentes por meio do Console de Operações a serem gerenciados pelo OMS, além de continuar executando pacotes de gerenciamento do SCOM.  
Os dados de um grupo de gerenciamento do SCOM conectado são entregues para o Log Analytics usando um dos quatro métodos:

* Os eventos e os dados de desempenho são coletados pelo agente e entregues ao SCOM.  Em seguida, os servidores de gerenciamento no SCOM entregam os dados para o Log Analytics.
* Alguns eventos, tais como logs do IIS e eventos de segurança, continuam sendo entregues diretamente para o Log Analytics do agente.
* Algumas soluções fornecerão software adicional para o agente ou exigirão a instalação de software para a coleta de dados adicionais.  Normalmente, esses dados serão enviados diretamente para o Log Analytics.
* Algumas soluções coletarão dados diretamente dos servidores de gerenciamento do SCOM que não se originam do agente.  Por exemplo, a [solução de Gerenciamento de Alertas](https://technet.microsoft.com/library/mt484092.aspx) coleta alertas do SCOM após sua criação.

## <a name="monitoring-logic"></a>Lógica de monitoramento
O SCOM e o Log Analytics trabalham com dados semelhantes aos dados coletados dos agentes, mas têm diferenças básicas no modo de definição e implementação de sua lógica de coleta de dados e em como analisam os dados coletados.

### <a name="operations-manager"></a>Operations Manager
A lógica de monitoramento do SCOM é implementada em [pacotes de gerenciamento](https://technet.microsoft.com/library/hh457558.aspx) , que contêm a lógica para a descoberta de componentes para monitoramento, medição da integridade desses componentes e coleta de dados para análise.  Os dados de monitoramento podem ser tão simples quanto coletar um evento ou contador de desempenho, ou podem usar uma lógica complexa implementada em um script.  Os pacotes de gerenciamento que incluem o monitoramento completo estão disponíveis para uma variedade de [aplicativos da Microsoft e de terceiros](http://go.microsoft.com/fwlink/?LinkId=82105) , além do hardware e de dispositivos de rede.  Você pode [criar seus próprios pacotes de gerenciamento](http://aka.ms/mpauthor) para aplicativos personalizados.

Os pacotes de gerenciamento contêm vários fluxos de trabalho que, individualmente, executam uma função de monitoramento diferente, como a amostragem de um contador de desempenho, verificação do estado de um serviço ou execução de um script.  Cada fluxo de trabalho é executado de forma independente e define seus próprios resultados, por exemplo, qual banco de dados será usado para a gravação e se um alerta será gerado. 

Você pode substituir os detalhes do fluxo de trabalho, como a frequência em que é executado, o limite em que eles consideram um erro e a severidade do alerta gerado.  Também é possível fornecer mais funcionalidade, adicionando seus próprios fluxos de trabalho.

![Substituições](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Os pacotes de gerenciamento são instalados no banco de dados do Operations Manager e são distribuídos automaticamente para os agentes pelos servidores de gerenciamento.  Cada agente baixará automaticamente os pacotes de gerenciamento e carregará fluxos de trabalho relevantes para os aplicativos que eles instalaram.  Os dados coletados pelo agente são entregues novamente para o servidor de gerenciamento para inserção no banco de dados e no data warehouse do SCOM.  O Console de Operações permite exibir e analisar esses dados por meio de exibições personalizadas, painéis e relatórios incluídos no pacote de gerenciamento.

A distribuição de pacotes de gerenciamento é ilustrada no diagrama a seguir.

![Fluxo de pacote de gerenciamento](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Coleta de eventos e de desempenho
O Log Analytics coleta eventos e contadores de desempenho de sistemas de agente usando fontes, como o log de eventos do Windows, os logs do IIS e o Syslog.  Você pode definir os critérios pelos quais os dados são coletados por meio do portal do Log Analytics e, em seguida, criar Consultas do Log para analisar os dados coletados.  Um conjunto de critérios padrão é definido quando você cria seu espaço de trabalho do OMS, e é possível definir dados adicionais para determinados aplicativos. 

![Definindo os logs de eventos no Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Embora o SCOM tenha vários fluxos de trabalho detalhados que, normalmente, definem critérios específicos para dados e a ação que deve ser executada em resposta, o Log Analytics apresenta critérios mais gerais para a coleta de dados.  As consultas do log e as soluções fornecem critérios mais direcionados para a análise e ação em dados específicos na nuvem após a coleta.

#### <a name="solutions"></a>Soluções
As soluções fornecem lógica adicional para a análise e coleta de dados.  É possível selecionar as soluções que serão adicionadas à sua assinatura do OMS por meio da Galeria de Soluções.

![Galeria de Soluções](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

As soluções são executadas principalmente na nuvem, oferecendo a análise dos eventos e dos contadores de desempenho coletados no repositório do OMS.  Elas também podem definir dados adicionais a serem coletados que podem ser analisados com Consultas do Log ou pela interface do usuário adicional fornecida pela solução no painel do OMS. 

Por exemplo, a [solução de Controle de Alterações](https://technet.microsoft.com/library/mt484099.aspx) detecta alterações de configuração em sistemas de agente e grava eventos no repositório do OMS que podem ser analisados com várias exibições gráficas que resumem as alterações detectadas.  É possível fazer uma busca detalhada por meio da exibição resumida nas consultas do log que exibem os dados detalhados coletados pela solução.

Embora você possa selecionar quais soluções serão adicionadas à sua assinatura, atualmente, não é possível criar suas próprias soluções.  Você pode selecionar os eventos e os contadores de desempenho para coletar e criar exibições personalizadas baseadas em suas próprias consultas do log.

A lógica de monitoramento do Log Analytics é resumida no diagrama a seguir.

![Fluxo da Solução do Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitoramento de integridade
### <a name="operations-manager"></a>Operations Manager
O SCOM pode modelar os diferentes componentes de um aplicativo e fornecer uma integridade em tempo real para cada um deles.  Isso permite tanto a exibição dos erros detectados e o desempenho ao longo do tempo quanto a validação da integridade real de um aplicativo ou sistema e de cada um de seus componentes em determinado momento.  Como ele entende os períodos em que um aplicativo está disponível, o mecanismo de integridade do SCOM também dá suporte a SLA (Contrato de Nível de Serviço) que analisam e relatam sobre a disponibilidade de um aplicativo ao longo do tempo.

Por exemplo, a exibição abaixo mostra a integridade em tempo real dos mecanismos de banco de dados SQL monitorados pelo SCOM.  A integridade de cada um dos bancos de dados para um dos mecanismos de banco de dados é mostrada na metade da exibição.

![Exibição do estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

O Gerenciador de Integridade de um dos mecanismos de banco de dados é mostrado abaixo com os monitores que são usados para determinar a integridade geral.  Esses monitores são definidos no pacote de gerenciamento do SQL e executados em todos os mecanismos de banco de dados SQL descobertos pelo SCOM.

![Gerenciador de integridade](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Os componentes de vários sistemas podem ser combinados para medir a integridade de um aplicativo distribuído.  Isso pode ser especialmente útil para aplicativos de linha de negócios que incluem vários componentes distribuídos.  É possível criar um modelo que mede a integridade de cada componente que integra um pacote cumulativo de atualizações na disponibilidade do aplicativo.

O Active Directory é um exemplo de um pacote de gerenciamento que fornece um modelo para análise dos componentes distribuídos.  O diagrama de exemplo abaixo mostra a integridade do ambiente geral e a relação entre florestas, domínios e controladores de domínio.  Cada um desses componentes inclui subcomponentes e vários monitores semelhantes ao exemplo do SQL acima.

![Exibição de diagrama do SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
O OMS não inclui um mecanismo comum para modelar aplicativos ou medir sua integridade em tempo real.  As soluções individuais podem avaliar a integridade geral de serviços específicos com base nos dados coletados, além de instalar uma lógica personalizada no agente para realizar uma análise em tempo real.  Como as soluções são executadas na nuvem com acesso ao repositório do OMS, em geral, elas podem fornecer uma análise mais aprofundada do que geralmente é realizado pelos pacotes de gerenciamento. 

Por exemplo, as [soluções de Avaliação do AD e Avaliação do SQL](https://technet.microsoft.com/library/mt484102.aspx) analisam os dados coletados e fornecem uma classificação de diferentes aspectos do ambiente.  Incluem recomendações de melhorias que podem ser feitas para melhorar a disponibilidade e o desempenho do ambiente.

![Solução de Avaliação do AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análise de Dados
O SCOM e o Log Analytics fornecem recursos diferentes para análise dos dados coletados.  O SCOM traz Exibições e Painéis no Console de Operações para análise de dados recentes em uma variedade de formatos e relatórios a fim de apresentar os dados do data warehouse em formato de tabela.  O Log Analytics fornece uma linguagem de consulta de log completa e uma interface para análise dos dados no repositório do OMS.  Quando o SCOM é usado como uma fonte de dados para o Log Analytics, o repositório inclui os dados coletados pelo SCOM, para que as ferramentas do Log Analytics possam ser usadas para analisar dados de ambos os sistemas.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Modos de exibição
As exibições no Console de Operações permitem exibir diferentes tipos de dados coletados pelo SCOM em formatos diferentes, geralmente, tabela para eventos, alertas e dados de estado, bem como gráficos de linhas para dados de desempenho.  As exibições realizam uma análise mínima ou consolidação dos dados, mas permitem a filtragem de acordo com critérios específicos. 

![Modos de exibição](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Normalmente, os pacotes de gerenciamento fornecem várias exibições que dão suporte ao aplicativo ou sistema monitorado.  Isso pode incluir exibições de estado para os diferentes objetos descobertos pelo pacote de gerenciamento, exibições de alerta para os problemas detectados e exibições de desempenho para os contadores.

As exibições são ideais para analisar o estado atual do ambiente, incluindo alertas em aberto e o estado de integridade de sistemas e objetos monitorados.  Você pode fazer uma busca detalhada em eventos ou dados de desempenho que dão suporte a um alerta específico para diagnosticar a causa raiz. Da mesma forma, é possível exibir o desempenho e a integridade de diferentes componentes de um aplicativo para avaliar a integridade atual.

#### <a name="dashboards"></a>Painéis
Os painéis no Console de Operações funcionam principalmente com os mesmos dados que as exibições, mas são mais personalizáveis e podem incluir visualizações mais avançadas.  Um conjunto de painéis padrão que pode ser personalizado com facilidade está disponível para propósitos pessoais.  Você também pode usar um widget do PowerShell que pode exibir os dados retornados de uma consulta do PowerShell.

![Painel](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Os desenvolvedores têm a capacidade de adicionar componentes personalizados aos painéis incluídos em seus pacotes de gerenciamento.  Eles variam de altamente especializados até um aplicativo específico, como o painel no pacote de gerenciamento do SQL mostrado abaixo.  Este painel também pode ser usado como modelo para versões personalizadas.

![Painel do SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Relatórios
Os relatórios no SCOM analisam dados do data warehouse em formato de tabela.  Eles podem ser impressos e agendados para a entrega automatizada em formatos de arquivo diferentes, incluindo PDF, CSV e Word.  Os relatórios trabalham com dados do data warehouse e, portanto, são ideais para a análise de tendências de longo prazo.

Geralmente, os pacotes de gerenciamento fornecerão relatórios personalizados para determinado aplicativo.  Também é possível selecionar em uma biblioteca de relatórios genéricos que podem ser personalizados para seus próprios aplicativos ou para a execução de análise ad hoc.

Apresentamos a seguir um relatório de desempenho de exemplo mostrando os dados coletados pelo Pacote de Gerenciamento do Active Directory.

![Relatório](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
O Log Analytics tem uma [linguagem de consulta](https://technet.microsoft.com/library/mt484120.aspx) que pode ser usada para executar a análise de dados de vários aplicativos, sem a necessidade de criar uma exibição ou um relatório personalizado.  Como OMS é implementado na nuvem, o desempenho de consultas e a análise de dados não estão sujeitos a limitações de hardware, e o OMS pode analisar consultas rapidamente, incluindo milhões de registros. 

As consultas do Log Analytics também são a base de outras funcionalidades.  Você pode salvar uma consulta, exportar os resultados para o Excel ou automatizar sua execução em intervalos regulares e gerar um alerta caso os resultados correspondam a critérios específicos.  

![Fluxo de consulta de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Veja abaixo um exemplo de uma consulta do Log Analytics.  Neste exemplo, todos os eventos com "iniciado" no nome são retornados e agrupados por ID do evento.  O usuário apenas fornece a consulta, e o Log Analytics gera dinamicamente a interface do usuário para executar a análise.  A seleção de qualquer item na lista retornará os dados detalhados do evento.

![Consulta de log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Além de fornecer análise ad hoc, as consultas do Log Analytics podem ser salvas para uso futuro e também são adicionadas ao [painel do OMS](http://technet.microsoft.com/library/mt484090.aspx) , conforme mostrado no exemplo a seguir.

![painel do OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Próximas etapas
* Implante o [SCOM (System Center Operations Manager)](https://technet.microsoft.com/library/hh205987.aspx).
* Inscreva-se no [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics).  

