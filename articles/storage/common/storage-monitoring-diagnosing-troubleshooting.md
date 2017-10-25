---
title: Monitorar, diagnosticar e solucionar problemas do Armazenamento do Azure | Microsoft Docs
description: "Use recursos como análise de armazenamento, registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure."
services: storage
documentationcenter: 
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: 1a9c9354b665294778886441cc6d7f02adb1163f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Visão geral
Questões de diagnóstico e de solução de problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas que em ambientes tradicionais. Aplicativos podem ser implantados em infraestrutura PaaS ou IaaS, no local, em um dispositivo móvel ou em uma dessas combinações. Normalmente, o tráfego de rede do seu aplicativo pode passar por redes privadas e públicas e o seu aplicativo pode usar múltiplas tecnologias de armazenamento tais como: Tabelas de Armazenamento, Blobs, Filas e Arquivos do Microsoft Azure, além de outros repositórios de dados, tais como: bancos de dados de documentos e relacionais.

Para gerenciar esses aplicativos com êxito, monitore-os de forma proativa e entenda todos os aspectos de como se faz o diagnóstico e a solução de problemas deles e de suas tecnologias dependentes. Como usuário dos serviços de Armazenamento do Azure, monitore continuamente o serviços de Armazenamento que o seu aplicativo utiliza para qualquer mudança inesperada em comportamento (como um tempo maior de resposta do que o normal) e faça o login para coletar mais dados detalhados para analisar o problema em profundidade. As informações de diagnósticos que você obtiver tanto do monitoramento como do registro em log irão ajudá-lo a determinar a raiz do problema que o seu aplicativo encontrou. Você poderá solucionar o problema e determinar as etapas apropriadas que você pode tomar para corrigi-lo. O Armazenamento do Azure é um serviço básico do Azure e é parte importante da maioria das soluções que os clientes implantam para a infraestrutura Azure. O Armazenamento do Azure inclui capacidades de simplificar questões de monitoramento, diagnóstico e de soluções de problemas de armazenamento em seus aplicativos em nuvem.

> [!NOTE]
> No momento, o Arquivos do Azure não dá suporte a registro em log.
> 

Para obter um guia prático para solução de problemas de ponta a ponta em aplicativos de armazenamento do Azure, consulte [Solução de problemas de ponta a ponta usando métricas de armazenamento do Azure e registro em log, AzCopy e Message Analyzer](../storage-e2e-troubleshooting.md).

* [Introdução]
  * [Como esse guia está organizado]
* [Monitoramento do seu serviço de armazenamento]
  * [Monitoramento da integridade do serviço]
  * [Monitoramento de capacidade]
  * [Monitoramento de disponibilidade]
  * [Monitoramento de desempenho]
* [Diagnóstico de problemas de armazenamento]
  * [Problemas de integridade do serviço]
  * [Problemas de desempenho]
  * [Diagnóstico de erros]
  * [Problemas de emulador de armazenamento]
  * [Ferramentas de log de armazenamento]
  * [Uso de ferramentas de log de rede]
* [Rastreamento de ponta a ponta]
  * [Correlacionamento de dados de log]
  * [ID de solicitação do cliente]
  * [ID de solicitação do servidor]
  * [Carimbos de data/hora]
* [Diretrizes para solução de problemas]
  * [As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency]
  * [As métricas mostram baixa AverageE2ELatency e baixa AverageServerLatency, mas o cliente está recebendo uma latência alta]
  * [As métricas mostram alta AverageServerLatency]
  * [Você está sofrendo atrasos inesperados na entrega de mensagens na fila]
  * [As métricas mostram um aumento em PercentThrottlingError]
  * [As métricas mostram um aumento em PercentTimeoutError]
  * [As métricas mostram um aumento em PercentNetworkError]
  * [O cliente está recebendo mensagens HTTP 403 (Proibido)]
  * [O cliente está recebendo mensagens HTTP 404 (Não encontrado)]
  * [O cliente está recebendo mensagens HTTP 409 (Conflito)]
  * [As métricas mostram uma baixa PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors]
  * [As métricas de capacidade mostram um aumento inesperado em uso de capacidade de armazenamento]
  * [Você está enfrentando reinicializações inesperadas das máquinas virtuais que contêm um grande número de VHDs anexados]
  * [Seu problema apareceu por usar o emulador de armazenamento para desenvolvimento ou teste]
  * [Você encontrou problemas ao instalar o SDK do Azure para .NET]
  * [Você tem um problema diferente com um serviço de armazenamento]
  * [Solução de problemas do Arquivos do Azure com Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Solução de problemas do Arquivos do Azure com Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Anexos]
  * [Anexo 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]
  * [Anexo 2: Usando o Wireshark para capturar o tráfego de rede]
  * [Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]
  * [Anexo 4: Usando o Excel para exibir as métricas e os dados de log]
  * [Anexo 5: Monitoramento com o Application Insights no Visual Studio Team Services]

## <a name="introduction"></a>Introdução
Esse guia mostra como você usa recursos como o Armazenamento Analítico do Azure, a biblioteca de armazenamento do cliente Azure com login do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure.

![][1]

Esse guia deve ser lido primeiramente pelos desenvolvedores de serviços online que usam os Serviços Armazenamento do Azure e profissionais de TI para gerenciar esses serviços online. Os objetivos desse guia são:

* Ajudar a manter a integridade e o desempenho de suas contas de Armazenamento do Azure.
* Oferecer os processos e as ferramentas necessários para ajudá-lo a decidir se um problema em um aplicativo está ou não relacionado ao armazenamento do Azure.
* Oferecer diretrizes de ações para resolver problemas relacionados ao armazenamento do Azure.

### <a name="how-this-guide-is-organized"></a>Como esse guia está organizado
A seção "[Monitoramento do seu serviço de armazenamento]“ descreve como monitorar a integridade e o desempenho de seus serviços de armazenamento do Azure usando as métricas analíticas de armazenamento do Azure (Métricas de Armazenamento).

A seção "[Diagnóstico de problemas de armazenamento]" descreve como diagnosticar os problemas ao usar o log de armazenamento analítico (Log de Armazenamento). Ela também descreve como habilitar o log do lado do cliente usando as facilidades em uma das bibliotecas do cliente, como a Bibliotecas de Cliente de Armazenamento para .NET ou o SDK do Azure para Java.

A seção "[Rastreamento de ponta a ponta]" descreve como você correlaciona as informações contidas em vários arquivos de log e em dados de métrica.

A seção "[Diretrizes para solução de problemas]" oferece diretrizes para a solução dos problemas mais comuns relacionados a armazenamento que você possa encontrar.

Os "[Anexos]" incluem informações sobre o uso de ferramentas como o Wireshark e Netmon para a análise de dados de pacote de rede, Fiddler para a análise de mensagens HTTP/HTTPS e o Microsoft Message Analyzer para correlacionar os dados de log.

## <a name="monitoring-your-storage-service"></a>Monitoramento do seu serviço de armazenamento
Se você está acostumado com o monitoramento de desempenho do Windows, é possível entender as Métricas de Armazenamento como equivalentes aos contadores do Monitor de Desempenho do Windows. Nas Métricas de Armazenamento, você encontrará um grupo detalhado de métricas (contadores com terminologia de Monitor de Desempenho do Windows) como disponibilidade de serviço, número total de solicitações de serviço ou percentual de êxito em solicitações de serviço. Para obter uma lista completa das métricas disponíveis, confira [Esquema da tabela de métricas da análise do armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx). Você pode especificar se deseja que o serviço de armazenamento colete e agregue as métricas a cada hora e ou cada minuto. Para mais informações sobre como habilitar as métricas e monitorar suas contas de armazenamento, confira [Habilitação das métricas de armazenamento e exibição dos dados de métrica](http://go.microsoft.com/fwlink/?LinkId=510865).

Você pode escolher quais intervalos de hora das métricas que você quer exibir no [Portal do Azure](https://portal.azure.com) e configurar as regras de notificação dos administradores por email caso a métrica a cada hora ultrapasse um valor particular. Para obter mais informações, confira [Receber notificações de alerta](/azure/monitoring-and-diagnostics/monitoring-overview-alerts.md). 

O serviço de armazenamento coleta as métricas usando o melhor esforço, porém pode não registrar todas as operações de armazenamento.

No Portal do Azure, você pode exibir as métricas de disponibilidade, total de solicitações e números das médias de latência para uma conta de armazenamento. Uma regra de notificação também foi configurada para alertar um administrador caso uma disponibilidade caia abaixo de um certo nível. Para exibir esses dados uma possível área para investigação é a tabela de porcentagem de êxito estar abaixo de 100% (para obter mais informações, consulte a seção "[As métricas mostram uma baixa PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors]").

Monitore continuamente seus aplicativos do Azure para garantir que sua integridade e desempenho estejam como esperados ao:

* Estabelecer algumas métricas de linha de base para aplicativos que permitirão que você compare os dados atuais e identifique qualquer mudança significativa em comportamento do armazenamento do Azure e seu aplicativo. Os valores para as métricas de linha de base irão, em muitos casos, ser específicos para cada aplicativo e você deverá estabelecê-los ao realizar um teste de desempenho do seu aplicativo.
* Registrar métricas a cada minuto e usá-las para monitorar ativamente cada erros e anomalias inesperados tais como: picos em contagem de erros ou taxas de solicitação.
* Registrar métricas a cada hora e usá-las para monitorar os valores médios como: médias de contagem de erros ou taxas de solicitação.
* Investigar os potenciais problemas usando as ferramentas de diagnóstico como discutido anteriormente na seção "[Diagnóstico de problemas de armazenamento]“.

Os gráficos na imagem a seguir ilustram como a média que acontece nas métricas de hora em hora podem esconder picos em atividade. As métricas de hora em hora parecem mostrar uma taxa constante de solicitações, enquanto as métricas de minuto em minuto revelam as flutuações que estão realmente acontecendo.

![][3]

O restante desta seção descreve quais as métricas que você deve monitorar e o porquê.

### <a name="monitoring-service-health"></a>Monitoramento da integridade do serviço
Você pode usar o [Portal do Azure](https://portal.azure.com) para exibir a integridade do serviço de armazenamento (e outros serviços do Azure) em todas as regiões do Azure no mundo. Isso permite que você veja imediatamente se um problema fora do seu controle está afetando o serviço de armazenamento na região em que você usa o seu aplicativo.

O [Portal do Azure](https://portal.azure.com) pode também fornecer notificações de incidentes que afetam os diversos serviços do Azure.
Nota: Essa informação está disponível anteriormente, juntamente com os dados históricos, no [Painel de Serviços do Azure](http://status.azure.com).

Enquanto o [Portal do Azure](https://portal.azure.com) coleta informações sobre integridade de dentro dos centros de dados do Azure (monitoramento inside-out), você também pode considerar a adoção de uma abordagem outside-in para gerar transações sintéticas que acessam periodicamente o seu aplicativo Web hospedado no Azure de vários locais. Os serviços oferecidos pelo [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) e Application Insights no Visual Studio Team Services são exemplos dessa abordagem outside-in. Para obter mais informações sobre o Application Insights no Visual Studio Team Services, consulte o anexo "[Anexo 5: Monitoramento com o Application Insights no Visual Studio Team Services](#appendix-5)".

### <a name="monitoring-capacity"></a>Monitoramento de capacidade
As métricas de armazenamento apenas armazena as métricas de capacidade do serviço blob porque os blobs normalmente são responsáveis pela maior proporção dos dados armazenados (no momento em que se escreve, não é possível usar as métricas de armazenamento para monitorar a capacidade de suas tabelas e filas). Você pode encontrar esses dados na tabela **$MetricsCapacityBlob** se você tiver habilitado o monitoramento para o serviço blob. As métricas de armazenamento registram esses dados uma vez ao dia e você pode usar o valor do **RowKey** para determinar se uma linha contém uma entidade que se relaciona aos **dados** do usuário (dados do valor) ou dados analíticos (valor **analítico**). Cada entidade armazenada contém informações sobre a quantidade de armazenamento usada (**Capacidade** medida em bytes) e o número atual de contêineres (**ContainerCount**) e blobs (**ObjectCount**) em uso em cada conta de armazenamento. Para saber mais sobre as métricas de capacidade armazenadas na tabela **$MetricsCapacityBlob** , consulte [Esquema da tabela de métricas da análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Monitore esses valores por meio de um aviso antecipado de que você está se aproximando dos limites de capacidade de sua conta de armazenamento. No Portal do Azure, você pode adicionar as regras de alertas para notificá-lo se o uso de armazenamento agregado excede ou está abaixo dos limites que você especificou.
> 
> 

Para ajudar na estimativa do tamanho dos diversos objetos de armazenamento, tais como os blobs, consulte a postagem no blog [Compreendendo a cobrança do Armazenamento do Azure – Largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitoramento de disponibilidade
Monitore a disponibilidade dos serviços de monitoramento em sua conta de armazenamento monitorando os valores na coluna de **Disponibilidade** nas tabelas métricas de hora em hora ou de minuto em minuto — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A coluna **Disponibilidade** contém um valor percentual que indica a disponibilidade do serviço ou da operação API representada pela linda (a **RowKey** mostra se uma linha contém as métricas para o serviço como um todo ou para uma operação API específica).

Qualquer valor inferior a 100% indica que houve falha em algumas solicitações de armazenamento. Você pode ver porque estão falhando ao examinar as outras colunas nos dados de métricas que mostras os números de solicitações com diferentes tipos de erros, tais como **ServerTimeoutError**. Espere para ver a **Disponibilidade** cair temporariamente abaixo de 100% devido a razões como tempo limite do servidor transitório enquanto o serviço muda as partições para melhor balancear a carga da solicitação; a lógica de nova tentativa no aplicativo do seu cliente deve lidar com tais condições intermitentes. O artigo [Operações registradas e mensagens de status da análise de armazenamento](http://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que as Métricas de armazenamento incluem em seu cálculo de **Disponibilidade** .

No [Portal do Azure](https://portal.azure.com), você pode adicionar as regras de alertas para notificá-lo se a **Disponibilidade** de um serviço está abaixo dos limites que você especificou.

A seção "[Diretrizes para solução de problemas]" deste guia descreve alguns dos problemas mais comuns de armazenamento relacionados a disponibilidade.

### <a name="monitoring-performance"></a>Monitoramento de desempenho
Para monitorar o desempenho dos serviços de armazenamento, você pode usar as seguintes métricas das tabelas de hora em hora ou minuto em minuto.

* Os valores nas colunas **AverageE2ELatency** e **AverageServerLatency** mostram o tempo médio do serviço de armazenamento ou do tipo da operação de API que está sendo usado para processar as solicitações. **AverageE2ELatency** é uma medida de latência de ponta a ponta que inclui o tempo levado para ler as solicitações e enviar a resposta, além do tempo levado para processar a solicitação (por isso, inclui a latência de rede uma vez que a solicitação atinge o serviço de armazenamento); **AverageServerLatency** é a medida apenas do tempo de processamento e, por isso, exclui qualquer latência de rede relacionada à comunicação com o cliente. Consulte a seção "[As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency]" posteriormente neste guia para uma discussão sobre porque pode haver uma diferença significante entre esses dois valores.
* Os valores nas colunas **TotalIngress** e **TotalEgress** mostram um valor total de dados, em bytes, entrando e saindo do seu serviço de armazenamento ou por um tipo de operação API específica.
* Os valores na coluna **TotalRequests** mostram o número total de solicitações que o serviço de armazenamento da operação API está recebendo. **TotalRequests** é o número total de solicitações que o serviço de armazenamento recebe.

Normalmente, você irá monitorar as mudanças inesperadas em qualquer um desses valores como indicador de que você tem um problema que requer uma investigação.

No [Portal do Azure](https://portal.azure.com), você pode adicionar as regras de alertas para notificá-lo se quaisquer métricas de desempenho desse serviço estão abaixo dos limites que você especificou.

A seção "[Diretrizes para solução de problemas]" deste guia descreve alguns dos problemas mais comuns de armazenamento relacionados a desempenho.

## <a name="diagnosing-storage-issues"></a>Diagnóstico de problemas de armazenamento
Há inúmero caminhos que você pode ter para ficar ciente de um problema em seu aplicativo, entre eles:

* Uma grande falha que faz com que o aplicativo entre em pane ou pare de funcionar.
* Mudanças significativas dos valores de linha de base em métricas que você está monitorando como descritas na seção anterior "[Monitoramento do seu serviço de armazenamento]“.
* Relatórios de usuários do seu aplicativo informando que uma operação em particular não foi concluída como esperado ou que algum recurso não está funcionando.
* Erros gerados dentro do seu aplicativo que aparecem nos arquivos de log ou em outros métodos de notificação.

Normalmente, problemas relacionados aos serviços de armazenamento do Azure estão entre uma das quatro grandes categorias:

* Seu aplicativo apresenta um problema de desempenho, relatado por um de seus usuários ou revelado por mudanças nas métricas de desempenho.
* Há um problema com a infraestrutura de armazenamento do Azure em uma ou mais regiões.
* Seu aplicativo está encontrando um erro, relatado por um de seus usuários ou revelado por um aumento em uma das métricas de contagem de erro que você monitora.
* Durante o desenvolvimento e o teste, você talvez esteja usando o emulador de armazenamento local; você pode encontrar alguns problemas relacionados especificamente ao uso do emulador de armazenamento.

As seguintes seções apresentam as etapas que você deve seguir para diagnosticar e solucionar os problemas em cada uma dessas quatro categorias. A seção "[Diretrizes para solução de problemas]" posteriormente nesse guia dará mais detalhes para alguns dos problemas mais comuns que você pode encontrar.

### <a name="service-health-issues"></a>Problemas de integridade do serviço
Problemas de integridade do serviço são normalmente fora do seu controle. O [Portal do Azure](https://portal.azure.com) dá informações sobre quaisquer problemas existentes com os serviços do Azure inclusive com os serviços de armazenamento. Se você optou pelo armazenamento com redundância geográfica de acesso a leitura (RA_GRS) quando você criou sua conta de armazenamento, então no evento de seus dados estarem indisponíveis no local principal, seu aplicativo pode mudar temporariamente para cópia somente de leitura em um local secundário. Para fazer isso, seu aplicativo deve ser capaz de alterar entre o uso de locais de armazenamento principal e secundário e ser capaz de trabalhar em modo de funcionamento reduzido com dados somente de leitura. As bibliotecas do cliente de armazenamento do Azure permitem que você defina uma política de tentativa que pode ler a partir do armazenamento secundário caso a leitura do armazenamento principal falhar. Seu aplicativo também precisa estar ciente que os dados do local secundário são consistentes. Para saber mais, consulte no blog a postagem [Opções de redundância do Armazenamento do Azure e armazenamento com redundância geográfica do acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de desempenho
O desempenho de um aplicativo pode ser subjetivo, especialmente da perspectiva de um usuário. Por isso, é importante ter métricas de linha de base disponíveis para ajudá-lo a identificar onde pode haver um problema de desempenho. Muitos fatores podem afetar o desempenho de um serviço de armazenamento do Azure da perspectiva do aplicativo do cliente. Esses fatores podem operar em um serviço de armazenamento, no cliente ou em uma infraestrutura de rede; portanto, é importante ter uma estratégia para identificar a origem do problema de desempenho.

Após ter identificado o possível local da causa do problema de desempenho a partir das métricas, você pode usar os arquivos de log para encontrar as informações detalhadas para diagnosticar e solucionar o problema mais profundamente.

A seção "[Diretrizes para solução de problemas]" posteriormente nesse guia dará mais detalhes para alguns dos problemas mais comuns que você pode encontrar.

### <a name="diagnosing-errors"></a>Diagnóstico de erros
Usuários do seu aplicativo podem notificá-lo de erros registrados pelo aplicativo do cliente. Métricas de armazenamento também registram contagens de diferentes tipos de erros do seus serviços de armazenamento, tais como **NetworkError**, **ClientTimeoutError** ou **AuthorizationError**. Enquanto as métricas de armazenamento apenas registram as contagens de diferentes tipos de erros, você obter mais detalhes sobre solicitações individuais ao examinar os logs do servidor, do cliente e da rede. Normalmente, o código de status HTTP que voltam para o serviço de armazenamento darão uma indicação da razão da falha da solicitação.

> [!NOTE]
> Lembre-se de que você deve esperar ver alguns erros intermitentes: por exemplo, erros devido a condições transitórias da rede ou erros de aplicativo.
> 
> 

Os seguintes recursos são úteis para compreender os status relacionados a armazenamento e os códigos de erro:

* [Códigos de erro comuns da API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Códigos de erro do serviço Blob](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Códigos de erro do serviço Fila](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Códigos de erro do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Códigos de erro do serviço de arquivo](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento
O SDK do Azure inclui um emulador de armazenamento que você pode executar em uma estação de trabalho de desenvolvimento. Esse emulador simula a maior parte do comportamento dos serviços de armazenamento do Azure e é útil durante o desenvolvimento e o teste, permitindo que você execute aplicativos que você usam serviços de armazenamento do Azure sem a necessidade de uma assinatura e uma conta de armazenamento do Azure.

A seção "[Diretrizes para solução de problemas]" deste guia descreve alguns dos problemas mais comuns usando o emulador de armazenamento.

### <a name="storage-logging-tools"></a>Ferramentas de log de armazenamento
O log de armazenamento oferece o log do lado do servidor para solicitações de armazenamento na sua conta de armazenamento do Azure. Para saber mais sobre como habilitar o log do lado do servidor e acessar os dados de log, consulte [Enabling Storage Logging and Accessing Log Data](http://go.microsoft.com/fwlink/?LinkId=510867)(Como habilitar o registro em log do armazenamento e o acesso aos dados do log).

A biblioteca do cliente de armazenamento para .NET habilita você a coletar dados de log do cliente relacionados as operações de armazenamento realizadas pelo seu aplicativo. Para saber mais, consulte [Registro em log no lado do cliente com a biblioteca do cliente de armazenamento para .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> Em algumas circunstâncias (tais como falhas de autorizações SAS) um usuário pode informar um erro pelo qual você não pode encontrar nenhum dado de solicitação nos logs de armazenamento do lado do cliente. Você pode usar as capacidades de log da biblioteca do cliente de armazenamento para investigar se a causa desse problema está no cliente ou use as as ferramentas de monitoramento de rede para investigar a rede.
> 
> 

### <a name="using-network-logging-tools"></a>Uso de ferramentas de log de rede
Você pode capturar o tráfego entre o cliente e o servidor para dar informações detalhadas sobre os dados que o cliente e o servidor estão trocando e as condições subjacentes de rede. Ferramentas úteis de log de rede incluem:

* [Fiddler](http://www.telerik.com/fiddler) é um proxy de depuração Web gratuito que permite que você examine os cabeçalhos e dados de conteúdo das solicitações HTTP e HTTPS e as mensagens de resposta. Para saber mais, consulte [Anexo 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS](#appendix-1).
* [Monitor de Rede da Microsoft (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) e [Wireshark](http://www.wireshark.org/) são analisadores de protocolo de rede gratuitos que permitem que você exiba informações detalhadas de pacote de uma vasta gama de protocolos de rede. Para obter mais informações sobre o Wireshark, consulte "[Anexo 2: Usando o Wireshark para capturar o tráfego de rede](#appendix-2)".
* O Microsoft Message Analyzer é uma ferramenta da Microsoft que substitui o Netmon e que além de capturar os dados de pacote de rede, a ajuda a exibir e analisar os dados de log capturados das outras ferramentas. Para saber mais, consulte "[Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede](#appendix-3)".
* Se você quer realizar um teste de conectividade básico para verificar que a máquina do cliente pode se conectar ao serviço de armazenamento do Azure pela rede, você não pode fazer isso usando a ferramenta padrão **ping** no cliente. Entretanto, você pode usar a [**ferramenta** tcping](http://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de log a partir do log de armazenamento e da biblioteca do cliente serão suficientes para diagnosticar um problema, porém, em alguns cenários, você poderá precisar de informações mais detalhadas que podem ser providas por essas ferramentas de log de rede. Por exemplo, usando o Fiddler para ver as mensagens HTTP e HTTPS permitem que você exiba o cabeçalho e os dados de carga enviados para e a partir dos serviços de armazenamento, o qual permite que você examine como o aplicativo do cliente repete as operações de armazenamento. Analisadores de protocolo, tais como Wireshark opera em nível de pacote permitindo que você exiba os dados TCP, os quais permitem que você solucione problemas de pacotes perdidos e problemas de conectividade. O Message Analyzer pode operar tanto em camadas HTTP como TCP.

## <a name="end-to-end-tracing"></a>Rastreamento de ponta a ponta
O rastreamento de ponta a ponta usando uma variedade de arquivos de log é uma técnica útil para a investigação de potenciais problemas. Você pode usar as informações de dia/hora de seus dados de métrica como uma indicação de onde começar a procurar nos arquivos de log para informações mais detalhadas que irão ajudá-lo a solucionar o problema.

### <a name="correlating-log-data"></a>Correlacionamento de dados de log
Ao exibir os logs dos aplicativos do cliente, rastreamento de rede e log de armazenamento do servidor é fundamental ser capaz de correlacionar as solicitações com os diferentes arquivos de log. Os arquivos de log incluem inúmeros campos diferentes que são úteis como identificadores de correlação. A ID de solicitação do cliente é o campo mais útil para usa para correlacionar entradas em logs diferentes. Entretanto, algumas vezes, pode ser útil usar ou a ID de solicitação do servidor ou os carimbos de data/hora. As seções a seguir fornecem mais detalhes sobre essas opções.

### <a name="client-request-id"></a>ID de solicitação do cliente
A Biblioteca de Clientes de Armazenamento gera automaticamente uma ID de solicitação do cliente exclusiva para cada solicitação.

* No log do lado do cliente criado pela Biblioteca de Clientes de Armazenamento, a ID de solicitação do cliente aparece no campo **ID de solicitação do cliente** de cada entrada de log relacionada à solicitação.
* No rastreamento da rede tal como uma capturada pelo Fiddler, a ID de solicitação do cliente está visível em mensagens de solicitação como o valor do cabeçalho HTTP **x-ms-client-request-id**.
* No log de armazenamento do lado do servidor, a ID de solicitação do cliente aparece na coluna ID da solicitação do cliente.

> [!NOTE]
> É possível que várias solicitações compartilhem a mesma ID de solicitação do cliente porque o cliente pode atribuir esse valor (embora a Biblioteca de Clientes de Armazenamento atribua um novo valor automaticamente). No caso de novas tentativas do cliente, todas as tentativas compartilham a mesma ID de solicitação do cliente. No caso de um lote enviado pelo cliente, o lote tem uma única ID de solicitação de cliente.
> 
> 

### <a name="server-request-id"></a>ID de solicitação do servidor
O serviço de armazenamento automaticamente gera ids de solicitação do servidor.

* No log de armazenamento do lado do servidor, a ID de solicitação do servidor aparece na coluna **Cabeçalho da ID de solicitação**.
* Em um rastreamento de rede tal como um capturado pelo Fiddler, a ID de solicitação do servidor aparece em mensagens de solicitação como o valor do cabeçalho HTTP **x-ms-request-id**.
* No log do lado do cliente criado pela Biblioteca de Clientes de Armazenamento, a ID de solicitação do cliente aparece na coluna **Texto de Operação** para a entrada de log mostrando detalhes da resposta do servidor.

> [!NOTE]
> O serviço de armazenamento sempre atribui uma única ID de solicitação de servidor para cada solicitação recebida, de modo que cada tentativa do cliente e cada operação incluída no lote tenha uma ID de solicitação do servidor exclusiva.
> 
> 

Se a biblioteca do cliente de armazenamento aciona uma **StorageException** no cliente, a propriedade **RequestInformation** contém um objeto **RequestResult** que inclui uma propriedade **ServiceRequestID**. Você também pode acessar um objeto **RequestResult** a partir de uma instância de **OperationContext**.

O exemplo de código abaixo demonstra como definir um valor personalizado de **ClientRequestId** ao anexar um objeto **OperationContext** à solicitação para o serviço de armazenamento. Isso também mostra como recuperar p valor de **ServerRequestId** de uma mensagem de resposta.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Carimbos de data/hora
Você também pode usar o carimbo de data/hora para localizar as entradas de log relacionadas, porém cuidado com qualquer distorção que possa existir entre o relógio do cliente e do servidor. Pesquise por mais ou menos 15 minutos para coincidir as entradas do lado do servidor com base no carimbo de data/hora do cliente. Lembre-se que os metadados de blob para os blobs contendo métricas indicam o intervalo de tempo para as métricas armazenadas no blob; isso é útil se você tiver muitas blobs de métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Diretrizes de solução de problemas
Essa seção irá ajudá-lo com o diagnóstico e com a solução de alguns dos problemas mais comuns que seu aplicativo pode encontrar ao usar os serviços de armazenamento do Azure. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

**Árvore de decisão de solução de problemas**

---
O seu problema está relacionado ao desempenho de um dos serviços de armazenamento?

* [As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency]
* [As métricas mostram baixa AverageE2ELatency e baixa AverageServerLatency, mas o cliente está recebendo uma latência alta]
* [As métricas mostram alta AverageServerLatency]
* [Você está sofrendo atrasos inesperados na entrega de mensagens na fila]

---
O seu problema está relacionado à disponibilidade de um dos serviços de armazenamento?

* [As métricas mostram um aumento em PercentThrottlingError]
* [As métricas mostram um aumento em PercentTimeoutError]
* [As métricas mostram um aumento em PercentNetworkError]

---
 O seu aplicativo do cliente está recebendo uma resposta HTTP 4XX (tal como 404) de um serviço de armazenamento?

* [O cliente está recebendo mensagens HTTP 403 (Proibido)]
* [O cliente está recebendo mensagens HTTP 404 (Não encontrado)]
* [O cliente está recebendo mensagens HTTP 409 (Conflito)]

---
[As métricas mostram uma baixa PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors]

---
[As métricas de capacidade mostram um aumento inesperado em uso de capacidade de armazenamento]

---
[Você está enfrentando reinicializações inesperadas das máquinas virtuais que contêm um grande número de VHDs anexados]

---
[Seu problema apareceu por usar o emulador de armazenamento para desenvolvimento ou teste]

---
[Você encontrou problemas ao instalar o SDK do Azure para .NET]

---
[Você tem um problema diferente com um serviço de armazenamento]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency
A figura abaixo da ferramenta de monitoramento do [portal do Azure](https://portal.azure.com) mostra um exemplo em que a **AverageE2ELatency** é significantemente mais alta que a **AverageServerLatency**.

![][4]

Observe que o serviço de armazenamento calcula apenas a métrica **AverageE2ELatency** para solicitações de êxito e, ao contrário de **AverageServerLatency**, inclui o tempo que o cliente leva para enviar os dados e receber a confirmação do serviço de armazenamento. Portanto, a diferença entre a **AverageE2ELatency** e a **AverageServerLatency** pode ser ou devido a lentidão de resposta do aplicativo do cliente ou devido às condições da rede.

> [!NOTE]
> Você também pode exibir **E2ELatency** e **ServerLatency** das operações de armazenamento individual nos dados de registro do log de Armazenamento.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Investigação dos problemas de desempenho do cliente
Entre as possíveis razões para a lentidão de resposta do cliente estão: ter um número limitado de conexões ou threads disponíveis ou ter poucos recursos, como CPU, memória ou largura de banda de rede. Você pode ser capaz de resolver os problemas ao modificar o código do cliente para ser mais eficiente (por exemplo ao usar chamadas assíncronas para o serviço de armazenamento) ou usar uma máquina virtual maior (com mais cores e mais memória).

Para os serviços Tabela e Fila, o algoritmo Nagle também pode causar **AverageE2ELatency** alta em comparação com **AverageServerLatency**: para saber mais, confira a postagem [Nagle’s Algorithm is Not Friendly towards Small Requests](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) (Algoritmo Nagle não é amigável a solicitações pequenas). Você pode desabilitar o algoritmo de Nagle em código ao usar a classe **ServicePointManager** no namespace **System.Net**. Faça isso antes de fazer qualquer chamada para os serviços de tabela ou fila no seu aplicativo já que isso não afeta as conexões que já estão abertas. O exemplo a seguir vem do método **Application_Start** em uma função de trabalho.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Verifique os logs do lado do cliente para ver quantas solicitações seu aplicativo do cliente está enviando e verifique se há afunilamentos gerais de desempenho relacionados ao .NET no seu cliente, tais como CPU, coleta de lixo .NET, utilização da rede ou memória. Como ponto de partida para solucionar problemas de aplicativos do cliente .NET, confira [Depuração, rastreamento e criação de perfil](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigando os problemas de latência de rede
Normalmente a alta latência de ponta a ponta causada pela rede é devido a condições transitórias. Você pode investigar tantos os problemas de rede persistentes ou transitórios, tais como pacotes ignorados ao usar ferramentas, tais como Wireshark ou Microsoft Message Analyzer.

Para saber mais sobre o uso do Wireshark para a solução de problemas de rede, consulte o "[Anexo 2: Usando o Wireshark para capturar o tráfego de rede]".

Para saber mais sobre o uso do Microsoft Message Analyzer para a solução de problemas de rede, consulte o "[Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]".

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>As métricas mostram baixa AverageE2ELatency e baixa AverageServerLatency, mas o cliente está recebendo uma latência alta
Nesse cenário, o caso mais provável é um atraso nas solicitações de armazenamento chegando no serviço de armazenamento. Investigue porque as solicitações do cliente não estão passando pelo serviço de blob.

Uma das possíveis razões para o atraso do cliente em enviar solicitações é que há um número limitado de conexões ou threads disponíveis.

Verifique também se o cliente está realizando várias tentativas e investigue a razão, se for o caso. Para determinar se o cliente está realizando várias tentativas, é possível:

* Examine os logs da Análise de Armazenamento. Se estiverem ocorrendo várias repetições, você verá várias operações com a mesma ID de solicitação do cliente, mas com IDs de solicitação de servidor diferentes.
* Examine os logs do cliente. O log detalhado indica que ocorreu uma repetição.
* Depure seu código e verifique as propriedades do objeto **OperationContext** associado à solicitação. Se a operação foi repetida, a propriedade **RequestResults** incluirá várias IDs de solicitação de servidor exclusivas. Você também pode verificar as horas de início e término de cada solicitação. Para obter mais informações, veja o exemplo de código na seção [ID de solicitação do servidor].

Se não houver problemas no cliente, investigue os possíveis problemas de rede, tais como perda de pacote. Você pode usar as ferramentas, tais como Wireshark ou Microsoft Message Analyzer para investigar os problemas de rede.

Para saber mais sobre o uso do Wireshark para a solução de problemas de rede, consulte o "[Anexo 2: Usando o Wireshark para capturar o tráfego de rede]".

Para saber mais sobre o uso do Microsoft Message Analyzer para a solução de problemas de rede, consulte o "[Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]".

### <a name="metrics-show-high-AverageServerLatency"></a>As métricas mostram alta AverageServerLatency
No caso de alta **AverageServerLatency** para as solicitações de download de blob, você deve usar os registros de log de armazenamento para ver se há solicitações repetidas para o mesmo blob (ou para grupos de blobs). Para solicitações de carregamento de blob, você deve investigar que tamanho de bloco o cliente está usando (por exemplo, blocos inferiores a 64k em tamanho podem resultar em sobrecargas ao menos que leituras também sejam inferiores a 64k partes) e se múltiplos clientes estiverem carregando blocos no mesmo blob em paralelo. Você também deve verificar as métricas por minuto para ver se há picos no número de solicitações que excedem as metas de escalabilidade por segundo: veja também “[As métricas mostram um aumento em PercentTimeoutError]”.

No caso de alta **AverageServerLatency** para as solicitações de download de blob, você deve usar os registros de log de armazenamento para ver se há solicitações repetidas para o mesmo blob (ou para grupos de blobs). Para solicitações de carregamento, você pode aprimorar a produtividade usando um tamanho maior de bloco. Para consultas às tabelas, também é possível implementar o cache no lado do cliente nos clientes que realizam as mesmas operações de consulta e nos casos em que os dados não mudam com frequência.

Valores altos de **AverageServerLatency** podem também ser um sintoma de tabelas ou consultas mal desenhadas que resultam em operações de digitalização ou que seguem a anti-sequência acrescentar/preceder. Consulte "[As métricas mostram um aumento em PercentThrottlingError]" para obter mais informações.

> [!NOTE]
> É possível encontrar uma lista de verificação de desempenho abrangente aqui: [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Você está sofrendo atrasos inesperados na entrega de mensagens na fila
Se você estiver sofrendo um atraso entre o tempo um aplicativo adiciona uma mensagem à fila e o tempo que ela fica disponível para a leitura da fila, então siga as seguintes etapas para diagnosticar o problemas:

* Verifique se o aplicativo está efetivamente adicionando as mensagens à fila. Verifique se o aplicativo não está tentando o método **AddMessage** várias vezes antes de conseguir. Os logs da biblioteca do cliente de armazenamento irá mostrar qualquer tentativa repetida de operações de armazenamento.
* Verifique se não há nenhum distorção no relógio entre a função de trabalho que adiciona a mensagem à fila e a função de trabalho que lê a mensagem da fila que faz parecer como se houvesse um atraso em processamento.
* A função de trabalho lê as mensagens da fila que tem falhas. Se um cliente de fila chama o método **GetMessage**, mas falha em responder com uma confirmação, a mensagem permanecerá invisível na fila até que o período de **invisibilityTimeout** expire. Nesse momento, a mensagem se torna disponibilidade para ser processada novamente.
* Verifique se o tamanho da fila está aumentando com o tempo. Isso pode acontecer se você não tiver operadores disponíveis o suficiente para processar todas as mensagens que os outros operadores estão colocando na fila. Verifique também as métricas para ver se as solicitações excluídas estão falhando e a retirada da fila conta as mensagens as quais podem indicar falhas repetidas de tentativas para excluir a mensagem.
* Examine os registros de log de armazenamento para qualquer operação de fila que possa estar mais alta do que a **E2ELatency** esperada e dos valor **ServerLatency** durante um período mais longo de tempo do que de costume.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>As métricas mostram um aumento em PercentThrottlingError
Erros de limitação acontecem quando você excede os alvos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente ou locatário possa usar o serviço a custos de outros. Para saber mais, consulte [Metas de desempenho e de escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre alvos de escalabilidade para contas de armazenamento e alvos de desempenho para partições dentro de contas de armazenamento.

Se a métrica de **PercentThrottlingError** mostra um aumento na porcentagem de solicitações que estão falhando com um erro de limitação, você precisa investigar um dos dois cenários:

* [Aumento transitório em PercentThrottlingError]
* [Aumento permanente em erro de PercentThrottlingError]

Um aumento em **PercentThrottlingError** frequentemente acontece ao mesmo tempo que há um aumento no número de solicitações de armazenamento ou quando você está fazendo um teste de carga inicial do seu aplicativo. Isso pode também manifestar no cliente como mensagens de status HTTP "503 Server Busy" ou "500 Operation Timeout" a partir das operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório em PercentThrottlingError
Se você estiver vendo picos no valor do **PercentThrottlingError** que coincidam com períodos de alta atividade para o aplicativo, você deve implementar uma retirada estratégia exponencial (não linear) para novas tentativas em seu cliente: isso reduzirá a carga imediata na partição e ajudará seu aplicativo a suavizar picos no tráfego. Para saber mais sobre como implementar políticas de repetição usando a Biblioteca do Cliente de Armazenamento, veja o [Namespace Microsoft.WindowsAzure.Storage.RetryPolicies](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Você também pode ver os picos no valor de **PercentThrottlingError** que não coincidem com períodos de alta atividade para o aplicativo: a causa mais provável aqui é o serviço de armazenamento estar movendo partições para melhorar o balanceamento de carga.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente em erro de PercentThrottlingError
Se você está vendo constantemente um valor alto para **PercentThrottlingError** seguido de um aumento permanente nos seus volumes de transações ou quando você está realizando os seus testes de carga iniciais no seu aplicativo, então você deve avaliar como o seu aplicativo está usando as partições de armazenamento e se está atingindo os alvos de escalabilidade para a conta de armazenamento. Por exemplo, se você está vendo erros de limitação na fila (o qual conta como uma partição única), então considere usar filas adicionais para espalhar as transações entre diversas partições. Se você está vendo erros de limitação em uma tabela, você precisa considerar usar um esquema de partições diferentes para espalhar suas transações entre as diversas partições usando uma gama maior de valores chave de partição. Uma causa comum desse problema é a colocar/acrescentar antipadrão onde você seleciona a data como a chave de partição e, em seguida, todos os dados em um determinado dia são gravados em uma partição: sob carga, isso pode resultar em um afunilamento de gravação. Considere um design de partição diferente ou avalie se usando um armazenamento de blob pode ser uma solução melhor. Verifique também se a limitação está ocorrendo como resultado de picos no seu tráfego e investigue as formas de suavizar seu padrão de solicitações.

Se você distribuir suas transações entre diversas partições, você ainda deve levar em consideração os limites de escalabilidade definidos para a conta de armazenamento. Por exemplo, se você usa dez filas cada processando no máximo 2.000 mensagens de 1KB por segundo, você estará no limite total de 20.000 mensagens por segundo para cada conta de armazenamento. Se você precisa processar mais de 20.000 entidades por segundo, você deve considerar usar diversas contas de armazenamento. Você também deve ter em mente que o tamanho de suas solicitações e entidades tem um impacto no quando o serviço de armazenamento limita seus clientes: se você tiver maiores solicitações e entidades, você pode estar limitada mais cedo.

Designs de consultas ineficientes podem causar também que você atinja os limites de escalabilidade para as partições de tabela. Por exemplo, uma consulta com um filtro que seleciona apenas um por cento das entidades em uma partição, mas que digitaliza todas as entidades em uma partição precisará ter acesso a cada entidade. Toda entidade lida irá contar para um número total de transações naquela partição, portanto, você pode facilmente atingir os alvos de escalabilidade.

> [!NOTE]
> Seu teste de desempenho deve revelar qualquer design de consulta ineficiente em sua partição.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>As métricas mostram um aumento em PercentTimeoutError
Suas métricas mostram um aumento em **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um volume alto de mensagens de status HTTP "500 Operation Timeout" a partir das operações de armazenamento.

> [!NOTE]
> Você pode ver temporariamente erros de tempo limite enquanto o serviço de armazenamento balanceia a carga de solicitações movendo um partição para um novo servidor.
> 
> 

A métrica **PercentTimeoutError** é uma agregação das seguintes métricas: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** e **SASServerTimeoutError**.

Os tempos limites do servidor são causados por um erro no servidor. Os tempos limites do cliente acontecem porque uma operação no servidor excedeu o tempo limite especificado pelo cliente; por exemplo, um cliente usando a biblioteca do cliente de armazenamento pode definir um tempo limite para uma operação usando a propriedade **ServerTimeout** da classe **QueueRequestOptions**.

Os tempos limites indicam um problema com o serviço de armazenamento que requer uma investigação detalhada. Você pode usar as métricas para ver se você está atingindo os limites de escalabilidade do servidor e para identificar quaisquer picos de tráfego que pode estar causando esse problema. Se o problema for intermitente, pode ser devido à atividade de balanceamento de carga no serviço. Se o problema for persistente e não for causado por atingir os limites de escalabilidade do serviço, acione um problema de suporte. Para os tempos limites do cliente, você deve decidir se os tempos limites estão definidos com um valor apropriado no cliente e alterar o valor definido para o tempo limite no cliente ou investigar como você pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizando suas consultas de tabela ou reduzindo o tamanho de suas mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>As métricas mostram um aumento em PercentNetworkError
Suas métricas mostram um aumento em **PercentNetworkError** para um dos seus serviços de armazenamento. A métrica **PercentNetworkError** é uma agregação das seguintes métricas: **NetworkError**, **AnonymousNetworkError** e **SASNetworkError**. Eles ocorrem quando o serviço de armazenamento detecta um erro de rede quando o cliente faz uma solicitação de armazenamento.

A causa mais comum desse erro é um cliente desconectando antes do tempo limite expirar no serviço de armazenamento. Investigue o código no seu cliente para entender porque e quando o cliente desconecta do serviço de armazenamento. Você também pode usar o Wireshark, o Microsoft Mensagem Analyzer ou o Tcping para investigar os problemas de conectividade de rede do cliente. Essas ferramentas estão descritas nos [Anexos].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está recebendo mensagens HTTP 403 (Proibido)
Se o seu aplicativo do cliente está emitindo erros HTTP 403 (Proibido), uma possível causa é que o cliente esteja usando uma assinatura de acesso compartilhado (SAS) expirada quando envia uma solicitação de armazenamento (embora outras causas possíveis incluem distorção de relógio, chaves inválidas e cabeçalhos vazios). Se uma chave SAS expirada for a causa, você não verá nenhuma entrada nos dados de registro de log de armazenamento do lado do servidor. A tabela a seguir mostra um exemplo de log do lado do cliente gerado pela biblioteca do cliente de armazenamento que ilustra esse problema acontecendo:

| Fonte | Detalhamento | Detalhamento | ID de solicitação do cliente | Texto de operação |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-… |Inicialização da operação com o local principal por modo de local PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab -… |Iniciando a solicitação síncrona para https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr=c&amp;si=mypolicy&amp;sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab -… |Esperando uma resposta. |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab -… |Exceção acionada enquanto aguarda a resposta: O servidor remoto retornou um erro: (403) Proibido. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab -… |Resposta recebida. Status code = 403, Request ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab -… |Exceção emitida durante a operação: O servidor remoto retornou um erro: (403) Proibido. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab -… |Verificando se a operação deve ser repetida. Contagem de repetição = 0, Código de status HTTP = 403, Exceção = O servidor remoto retornou um erro: (403) Proibido. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab -… |O próximo local deve ser definido como principal, com base no modo de local. |
| Microsoft.WindowsAzure.Storage |Erro |1 |85d077ab -… |A política de repetição não permitiu uma nova tentativa. Falha com O servidor remoto retornou um erro: (403) Proibido. |

Nesse cenário, você deve investigar porque o token de SAS está expirando antes do cliente enviar o token para o servidor:

* Normalmente, você não deveria definir um tempo de início quando você cria uma SAS para um cliente para usar imediatamente. Se houver pequenas diferenças entre os relógios do hospedeiro que gera o SAS usando o horário atual e o serviço de armazenamento, então é possível que o serviço de armazenamento receba uma SAS que não seja válida.
* Não defina um tempo de expiração muito curto na SAS. Novamente, pequenas diferenças entre os relógios do hospedeiro gerando a SAS e o serviço de armazenamento pode levar a uma SAS aparentemente expirando mais cedo do que esperado.
* Os parâmetros da versão na chave SAS (por exemplo **sv=2015-04-05**) correspondem à versão da Biblioteca do Cliente de Armazenamento usada? Recomendamos usar sempre a versão mais recente da [Biblioteca de Cliente de Armazenamento](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Se você gerar suas chaves de acesso de armazenamento, isso pode invalidar quaisquer tokens de SAS existentes. Isso pode ser um problema se você token de SAS com um tempo de expiração longo para aplicativos de cliente para o cache.

Se você estiver usando a biblioteca do cliente de armazenamento para gerar tokens de SAS, então será fácil compilar um token válido. Entretanto, se você estiver usando a API REST de Armazenamento e compilando tokens de SAS manualmente, leia cuidadosamente o tópico [Delegando acesso com uma assinatura de acesso compartilhado](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>O cliente está recebendo mensagens HTTP 404 (Não encontrado)
Se o aplicativo do cliente recebe uma mensagem HTTP 404 (Não encontrado) do ser, isso implica que o objeto do cliente estava tentando usar (tais como: uma entidade, tabela, blob, contêiner ou fila) não existe no serviço de armazenamento. Existem muitas razões para isso, tais como:

* [O cliente ou outro processo excluiu anteriormente o objeto]
* [Um problema de autorização de assinatura de acesso compartilhado (SAS)]
* [O código JavaScript do lado do cliente não tem permissão para acessar o objeto]
* [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou outro processo excluiu anteriormente o objeto
Em cenários onde o cliente está tentando ler, atualizar ou excluir dados em um serviço de armazenamento é, normalmente, fácil de se identificar nos logs do lado do servidor uma operação anterior que excluiu o objeto em questão de um serviço de armazenamento. Frequentemente, os dados de log mostram que um outro usuário ou processo excluiu o objeto. No registro de log de armazenamento no lado do servidor, as colunas do tipo de operação e de chave de objeto solicitado mostram quando um cliente excluiu um objeto.

No cenário onde um cliente está tentando inserir um objeto, pode não ser imediatamente óbvio porque isso resulta em uma resposta HTTP 404 (Não encontrado) dado que o cliente está criando um novo objeto. Entretanto, se o cliente estiver criando um blob, é possível achar o contêiner do blob, se o cliente estiver criando uma mensagem, é possível encontrar a fila e se o cliente estiver adicionando uma coluna é possível encontrar uma tabela.

Você pode usar o log do lado do cliente a partir da biblioteca do cliente de armazenamento para obter uma compreensão mais detalhada de quando o cliente envia solicitações específicas para o serviço de armazenamento.

O seguinte log do lado do cliente gerado pela biblioteca do cliente de armazenamento ilustra o problema quando o cliente não pode encontrar o contêiner para o blob que está criando. Esse log inclui detalhes das seguintes operações de armazenamento:

| ID de solicitação | Operação |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** para excluir o contêiner do blob. Observe que essa operação inclui uma solicitação **HEAD** para verificar a existência do contêiner. |
| e2d06d78… |**CreateIfNotExists** para criar o contêiner do blob. Observe que essa operação inclui uma solicitação **HEAD** que verifica a existência do contêiner. **HEAD** retorna uma mensagem 404, porém continua. |
| de8b1c3c-... |**UploadFromStream** para criar um blob. A solicitação **PUT** falha com uma mensagem 404 |

Entradas de log:

| ID de solicitação | Texto de operação |
| --- | --- |
| 07b26a5d-... |Inicialização de solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Esperando uma resposta. |
| 07b26a5d-... |Resposta recebida. Status code = 200, Request ID = eeead849-...Content-MD5 = , ETag =    &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Cabeçalhos da resposta foram processados com êxito, procedendo com o resto da operação. |
| 07b26a5d-... |Baixar o corpo da resposta. |
| 07b26a5d-... |Operação concluída com sucesso. |
| 07b26a5d-... |Inicialização de solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Esperando uma resposta. |
| 07b26a5d-... |Resposta recebida. Status code = 202, Request ID = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Cabeçalhos da resposta foram processados com êxito, procedendo com o resto da operação. |
| 07b26a5d-... |Baixar o corpo da resposta. |
| 07b26a5d-... |Operação concluída com sucesso. |
| e2d06d78-... |Inicialização de solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Esperando uma resposta. |
| de8b1c3c-... |Inicialização de solicitação síncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Preparação para gravar os dados solicitados. |
| e2d06d78-... |Exceção acionada enquanto aguarda a resposta: O servidor remoto retornou um erro: (404) Não encontrado. |
| e2d06d78-... |Resposta recebida. Status code = 404, Request ID = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Cabeçalhos da resposta foram processados com êxito, procedendo com o resto da operação. |
| e2d06d78-... |Baixar o corpo da resposta. |
| e2d06d78-... |Operação concluída com sucesso. |
| e2d06d78-... |Inicialização de solicitação assíncrona para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Esperando uma resposta. |
| de8b1c3c-... |Gravação dos dados solicitados. |
| de8b1c3c-... |Esperando uma resposta. |
| e2d06d78-... |Exceção acionada enquanto aguarda a resposta: O servidor remoto retornou um erro: (409) Conflito. |
| e2d06d78-... |Resposta recebida. Status code = 409, Request ID = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Erro ao baixar o corpo da resposta. |
| de8b1c3c-... |Exceção acionada enquanto aguarda a resposta: O servidor remoto retornou um erro: (404) Não encontrado. |
| de8b1c3c-... |Resposta recebida. Status code = 404, Request ID = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Exceção emitida durante a operação: O servidor remoto retornou um erro: (404) Não encontrado. |
| de8b1c3c-... |A política de repetição não permitiu uma nova tentativa. Falha com o servidor remoto retornou um erro: (404) Não encontrado. |
| e2d06d78-... |A política de repetição não permitiu uma nova tentativa. Falha com o servidor remoto retornou um erro: (409) Conflito. |

Nesse exemplo, o log mostra que o cliente está intercalando solicitações do método **CreateIfNotExists** (ID de solicitação e2d06d78…) com as solicitações do método **UploadFromStream** (de8b1c3c-...); isso está acontecendo porque o aplicativo do cliente está chamando esses métodos de modo assíncrono. Modifique o código assíncrono no cliente para garantir que ele crie o contêiner antes de tentar carregar qualquer dado para o blob nesse contêiner. Idealmente, crie todos os contêineres antes.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso compartilhado (SAS)
Se o aplicativo do cliente tentar usar uma chave de SAS que não inclui as permissões necessárias para a operação, o serviço de armazenamento retorna uma mensagem HTTP 404 (Não encontrado) para o cliente. Ao mesmo tempo, você também verá um valor diferente de zero para **SASAuthorizationError** nas métricas.

A tabela a seguir mostra um exemplo de mensagem d log do lado do servidor a partir do arquivo de registro de log de armazenamento:

| Nome | Valor |
| --- | --- |
| Hora de início da solicitação | 2014-05-30T06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Status da solicitação     | SASAuthorizationError        |
| Código de status HTTP   | 404                          |
| Tipo de autenticação.| Sas                          |
| Tipo de serviço       | Blob                         |
| URL de Solicitação        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Cabeçalho da id de solicitação do   | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID de solicitação do cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Investigue porque o aplicativo do cliente está tentando realizar uma operação que não tem permissão.

#### <a name="JavaScript-code-does-not-have-permission"></a>O código JavaScript do lado do cliente não tem permissão para acessar o objeto
Se você estiver usando um cliente JavaScript e um serviço de armazenamento está retornando mensagens HTTP 404 (Não encontrado), verifique os seguintes erros JavaScript no navegador:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Você pode usar a ferramenta de desenvolvedor F12 no Internet Explorer para rastrear as mensagens trocadas entre o navegador e o serviço de armazenamento quando você estiver solucionando os problemas JavaScript do lado do cliente.
> 
> 

Esses erros acontecem porque o navegador da Web implementa a restrição de segurança de [política de mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy) , que impede uma página da Web de chamar uma API em um domínio diferente do domínio de onde a página vem.

Para resolver o problema JavaScript, configure o compartilhamento de recursos entre origens (CORS) para o serviço de armazenamento que o cliente está acessando. Para saber mais, veja [Suporte de CORS (Compartilhamento de Recursos entre Origens) para os Serviços de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

O código a seguir mostra como configurar seu serviço blob para permitir que o JavaScript execute o domínio Contoso para acessar um blob no seu serviço de armazenamento de blob:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Falha de rede
Em algumas circunstâncias, os pacotes de rede perdidos podem levar o serviço de armazenamento a retornar mensagens HTTP 404 para o cliente. Por exemplo, quando o seu aplicativo do cliente está excluindo uma entidade do serviço de tabela, você pode ver o cliente emitir uma exceção de armazenamento relatando uma mensagem de status "HTTP 404 (Não encontrado)" do serviço de tabela. Quando você investiga a tabela no serviço de armazenamento da tabela, é possível ver que o serviço excluiu a entidade como solicitado.

Os detalhes da exceção no cliente incluem a ID da solicitação (7e84f12d...) atribuída pelo serviço Tabela para a solicitação: você pode usar essas informações para localizar os detalhes da solicitação nos logs de armazenamento no lado do servidor pesquisando na coluna **request-id-header** no arquivo de log. Você pode também usar as métricas para identificar quando falhas como essa ocorrem e pesquisar os arquivos de log com base no horário que as métricas registraram esse erro. Essa entrada de log mostram a falha excluída com uma mensagem de status "HTTP (404) Outro Erro do Cliente". A mesma entrada de log também inclui a ID de solicitação gerada pelo cliente na coluna **client-request-id** (813ea74f…).

O log do lado do servidor inclui também uma outra entrada com o mesmo valor **client-request-id** (813ea74f…) para uma operação de exclusão com sucesso para a mesma entidade e do mesmo cliente. A operação de exclusão com êxito aconteceu um pouco antes da solicitação de exclusão falhar.

O caso mais provável nesse cenário é que o cliente enviou uma solicitação excluída da entidade para o serviço de tabela, a qual teve êxito, mas não recebeu uma confirmação do servidor (talvez devido a problemas temporários de rede). O cliente repetiu a operação automaticamente (usando o mesmo **client-request-id**) e essa tentativa falhou porque a entidade já tinha sido excluída.

Se esse problema ocorre com frequência, investigue porque o cliente não está recebendo as confirmações do serviço de tabela. Se o problema for intermitente, intercepte o erro "HTTP (404) Não encontrado" e log no cliente, mas permita que o cliente continue.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está recebendo mensagens HTTP 409 (Conflito)
A tabela a seguir mostra um trecho do log do lado do servidor para duas operações de cliente: **DeleteIfExists** seguida imediatamente de **CreateIfNotExists**, ambas usando o mesmo nome de contêiner de blob. Observe que a operação do cliente resulta em duas solicitações enviadas para o servidor, primeiro uma solicitação **GetContainerProperties** para verificar se o contêiner existe, seguida por uma solicitação de **DeleteContainer** ou **CreateContainer**.

| Timestamp | Operação | Result | Nome do contêiner | ID de solicitação do cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

O código no aplicativo cliente exclui e, em seguida, recria imediatamente um contêiner de blob usando o mesmo nome: o método **CreateIfNotExists** (ID de solicitação de cliente bc881924-...), eventualmente, falha com o erro de HTTP 409 (Conflito). Quando um cliente exclui contêineres de blob, tabelas ou filas há um breve período antes do nome ficar disponível novamente.

O aplicativo do cliente deve usar nomes de contêiner exclusivos sempre que criar novos contêineres caso o padrão excluir/recriar for comum.

### <a name="metrics-show-low-percent-success"></a>As métricas mostram uma baixa PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors
As métricas de **PercentSuccess** capturam a porcentagem das operações que tiveram êxito com base nos códigos de status HTTP. As operações com códigos de status 2XX contam como bem sucedidas, enquanto as operações com código de status de intervalos 3XX, 4XX e 5XX são contadas como sem sucesso e inferiores aos valores de métricas **PercentSucess** . Nos arquivos de log do lado do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**.

É importante observar que essas operações foram concluídas com sucesso e por isso não afetam as outras métricas como a de disponibilidade. Alguns exemplos de operações que executam com sucesso, mas que podem resultar em códigos de status HTTP sem sucesso incluem:

* **ResourceNotFound** (Não encontrado 404), por exemplo, de uma solicitação GET para o blob que não existe.
* **ResouceAlreadyExists** (Conflito 409), por exemplo, de uma operação **CreateIfNotExist** em que o recurso já existe.
* **ConditionNotMet** (Não Modificado 304), por exemplo, de uma operação condicional tal como quando o cliente envia um valor **ETag** e um cabeçalho HTTP **If-None-Match** para solicitar uma imagem apenas se ela tiver sido carregada desde a última operação.

Você pode encontrar uma lista de códigos de erro comuns da API REST que os serviços de armazenamento retornam na página [Códigos de erro comuns da API REST](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>As métricas de capacidade mostram um aumento inesperado em uso de capacidade de armazenamento
Se você vê mudanças repentinas, inesperadas na capacidade de uso na sua conta de armazenamento, você pode investigar as razões, primeiramente olhando as métricas de disponibilidade; por exemplo, um aumento no número de falhas de solicitações de exclusão pode levar a um aumento na quantidade de armazenamento de blobs que você está usando, já que operações de limpeza específicas de aplicativo que você esperava que estivessem liberando espaço podem não estar funcionando como esperado (por exemplo, porque os tokens SAS usados para liberação de espaço expiraram).

### <a name="you-are-experiencing-unexpected-reboots"></a>Você está enfrentando reinicializações inesperadas das máquinas virtuais do Azure que contêm um grande número de VHDs anexados
Se uma máquina virtual (VM) do Azure tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, você pode exceder os alvos de escalabilidade de uma conta de armazenamento individual causando uma falha na VM. Verifique as métricas de minuto da conta de armazenamento ((**TotalRequests**/**TotalIngress**/**TotalEgress**) para ver se há picos que excedem as metas de escalabilidade de uma conta de armazenamento. Consulte a seção "[As métricas mostram um aumento em PercentThrottlingError]" para ajudar a determinar se a limitação ocorreu na sua conta de armazenamento.

Em geral, cada operação de entrada ou saída individual em um VHD a partir de uma máquina virtual se traduz em operações **Get Page** ou **Put Page** no blob de página subjacente. Portanto, você pode usar os IOPS estimados para o seu ambiente para ajustar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico do seu aplicativo. Não recomendamos ter mais do que 40 discos em uma única conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre as metas de escalabilidade atuais para contas de armazenamento, em particular a largura de banda total e a taxa de solicitação para o tipo de conta de armazenamento que você está usando.
Se você está excedendo as metas de escalabilidade para sua conta de armazenamento, você deve colocar seus VHDs em várias contas de armazenamento diferentes para reduzir a atividade de cada conta individual.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Seu problema apareceu por usar o emulador de armazenamento para desenvolvimento ou teste
Você normalmente usa o emulador de armazenamento durante o desenvolvimento e teste para evitar o requisito de uma conta de armazenamento do Azure. Os problemas comuns que podem ocorrer quando você estiver usando o emulador de armazenamento são:

* [O recurso "X” não está funcionando no emulador de armazenamento]
* [Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]
* [A execução do emulador de armazenamento requer privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>O recurso "X” não está funcionando no emulador de armazenamento
O emulador de armazenamento não é compatível com todos os recursos dos serviços de armazenamento do Azure, como o serviço de arquivo. Para saber mais, confira [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md).

Para os recursos que não são compatíveis com o emulador de armazenamento, use o serviço de armazenamento do Azure em nuvem.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento
Você está testando o seu aplicativo (que usa a Biblioteca de Clientes de Armazenamento) no emulador de armazenamento local e chamadas de método como **CreateIfNotExists** falham com uma mensagem de erro "O valor para um dos cabeçalhos HTTP não está no formato correto". Isso indica que a versão do emulador de armazenamento que você está usando não é compatível com a versão da biblioteca do cliente de armazenamento que você está usando. A biblioteca do cliente de armazenamento adiciona um cabeçalho **x-ms-version** em todas as solicitações que ela faz. Se o emulador de armazenamento não reconhecer o valor no cabeçalho **x-ms-version** , ele rejeita a solicitação.

Você pode usar os log de clientes de biblioteca de armazenamento para ver o valor do **cabeçalho x-ms-version** que está enviando. Você também pode ver o valor do **cabeçalho x-ms-version** se você usar o Fiddler para rastrear as solicitações do aplicativo do cliente.

Esse cenário normalmente acontece se você instalar e usar a versão mais recente da biblioteca do cliente de armazenamento sem atualizar o emulador de armazenamento. Instale a versão mais recente do emulador de armazenamento ou use o armazenamento em nuvem em vez do emulador para desenvolvimento ou teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>A execução do emulador de armazenamento requer privilégios administrativos
Você pode solicitar credenciais para o administrador quando você executar o emulador de armazenamento. Isso acontece apenas quando você inicializar o emulador de armazenamento pela primeira vez. Após você ter inicializado o emulador de armazenamento, você não precisa de privilégios de administrador para executá-lo novamente.

Para saber mais, confira [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md). Observe que você também pode inicializar o emulador de armazenamento no Visual Studio, o que também exige privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Você encontrou problemas ao instalar o SDK do Azure para .NET
Quando você tenta instalar o SDK, ele falhar ao tentar instalar o emulador de armazenamento no seu computador local. O log de instalação contém uma das seguintes mensagens:

* CAQuietExec: Erro: Não é possível acessar a instância do SQL
* CAQuietExec: Erro: Não é possível criar o banco de dados

A causa é um problema com a instalação LocalDB existente. Por padrão, o emulador de armazenamento usa o LocalDB para persistir os dados quando simula os serviços de armazenamento do Azure. Você pode reiniciar a sua instância LocalDB ao executar os seguintes comando na janela de prompt de comando antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O comando **delete** remove qualquer arquivo de bancos de dado antigo das instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Você tem um problema diferente com um serviço de armazenamento
Se as seções anteriores de solução de problemas não incluem os problemas que você está tendo com o serviço de armazenamento, adote a seguinte abordagem para diagnosticar a solução de seu problema.

* Verifique as métricas para ver se há qualquer mudança de comportamento da linha de base esperada. A partir das métricas, você pode determinar se o problema é transitório ou permanente, e quais as operações de armazenamento que o problema está afetando.
* Você pode usar as informações de métricas para ajudá-lo a procurar os dados de log do lado do servidor para obter informações mais detalhadas sobre qualquer erro que esteja ocorrendo. Essa informação pode ajudá-lo a encontrar e a solucionar o problema.
* Se a informação nos logs do lado do servidor não forem suficientes para resolver o problema com êxito, você pode usar os logs do lado do cliente da biblioteca do cliente de armazenamento para investigar o comportamento do seu aplicativo do cliente e ferramentas, tais como Fiddler, Wireshark e Microsoft Message Analyzer para investigar a sua rede.

Para saber mais sobre como usar o Fiddler, consulte "[Anexo 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]".

Para saber mais sobre usar o Wireshark, veja “[Anexo 2: Usando o Wireshark para capturar o tráfego de rede]”.

Para saber mais sobre como usar o Microsoft Message Analyzer, consulte o "[Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]".

## <a name="appendices"></a>Anexos
Os anexos descrevem várias ferramentas que você pode achar úteis ao diagnosticar ou solucionar os problemas com o armazenamento do Azure (e outros serviços). Essas ferramentas não são parte do armazenamento do Azure e alguns são produtos de terceiros. Como tal, as ferramentas discutidas nesses anexos não são cobertas por nenhum contrato de suporte que você possa ter com o Microsoft Azure ou Armazenamento do Azure e, portanto, como parte de seu processo de avaliação examine as opções de licença e de suporte disponíveis pelos fornecedores dessas ferramentas.

### <a name="appendix-1"></a>Anexo 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS
[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre o aplicativo do cliente e o serviço de armazenamento do Azure que você está usando.

> [!NOTE]
> O Fiddler pode decodificar o tráfego HTTPS; leia a documentação do Fiddler cuidadosamente para entender como ele faz isso e para entender as implicações de segurança.
> 
> 

Esse anexo dá um breve passo a passo de como configurar o Fiddler para capturar o tráfego entre o computador local onde você instalou o Fiddler e os serviços de armazenamento do Azure.

Após você ter iniciado o Fiddler, ele começará capturar o tráfego HTTP e HTTPS da sua máquina local. A seguir há alguns comandos úteis para controlar o Fiddler:

* Parar e iniciar a captura de tráfego. No menu principal, vá para **Arquivo** e clique em **Capturar Tráfego** para alternar entre captura ativada e desativada.
* Salve os dados de tráfego capturados. No menu principal, vá até **Arquivo**, clique em **Salvar** e, em seguida, clique em **Todas as Sessões**: isso permite que você salve o tráfego em um arquivo de sessão. Você pode recarregar um Session Archive mais tarde para análise ou enviá-lo se solicitado pelo suporte Microsoft.

Para limitar o valor de tráfego que o Fiddler captura, você pode usar filtros que você configura na guia **Filtros** . A seguinte captura de tela mostra um filtro que captura apenas o tráfego enviado para o ponto de extremidade de armazenamento **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="appendix-2"></a>Anexo 2: Usando o Wireshark para capturar o tráfego de rede
[Wireshark](http://www.wireshark.org/) é um analisar de protocolo de rede que permite que você exiba informações detalhadas de pacote de uma vasta gama de protocolos de rede.

O procedimento a seguir mostra como capturar informações detalhadas de pacote para tráfego a partir do computador local onde você instalou o Wireshark para o serviço de tabela na sua conta de armazenamento do Azure.

1. Habilite o Wireshark no seu computador local.
2. Na seção **Iniciar** , selecione a interface de rede local ou as interfaces que estão conectadas à Internet.
3. Clique em **Opções de Captura**.
4. Adicione um filtro à caixa de texto **Filtro de Captura** . Por exemplo, o **host contosoemaildist.table.core.windows.net** configurará o Wireshark para capturar apenas os pacotes enviados para ou a partir do ponto de extremidade de serviço tabela na conta de armazenamento **contosoemaildist**. Confira a [Lista completa de filtros de captura](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Clique em **Iniciar**. O Wireshark capturará agora todos pacotes enviados para ou a partir do ponto de extremidade do serviço de tabela enquanto você usa o aplicativo do cliente no seu computador local.
6. Quando você tiver terminado, no menu principal clique em **Capturar** e em **Parar**.
7. Para salvar os dados capturados no arquivo de captura do Wireshark, no menu principal clique em **Arquivo** e em **Salvar**.

O Wireshark irá realçar qualquer erro que existir na janela **packetlist** . Você também pode usar a janela **Informações do Especialista** (clique em **Analisar** e em **Informações do Especialista**) para exibir um resumo dos erros e avisos.

![][7]

Você também pode escolher exibir os dados de TCP conforme vistos pela camada de aplicativo, clicando com o botão direito do mouse nos dados de TCP e selecionando **Seguir o Fluxo TCP**. Isso é bastante útil se você capturou o seu despejo sem o filtro de captura. Para saber mais, confira [Como seguir fluxos TCP](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Para saber mais sobre como usar o Wireshark, veja o [Guia de Usuários do Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede
Você pode usar o Microsoft Message Analyzer para capturar o tráfego HTTP e HTTPS de uma forma similar ao Fiddler e capturar o tráfego de rede de uma forma similar ao Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configure a sessão de rastreamento Web usando o Microsoft Message Analyzer
Para configurar a sessão de rastreamento Web para tráfego HTTP e HTTPS usando o Microsoft Message Analyzer, execute o aplicativo Microsoft Message Analyzer e, no menu **Arquivo**, clique em **Capturar/Rastrear**. Na lista de cenários de rastreamento disponíveis, selecione **Proxy da Web**. Em seguida, no painel **Rastrear Configuração de Cenário**, na caixa de texto **HostnameFilter**, adicione os nomes dos pontos de extremidade de armazenamento (você pode procurar esses nomes no [Portal do Azure](https://portal.azure.com)). Por exemplo, se o nome da sua conta de armazenamento do Azure é **contosodata**, adicione a seguinte caixa de diálogo **HostnameFilter**:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Um caractere de espaço separa o nome de host.
> 
> 

Quando você estiver pronto para coletar os dados de rastreamento, clique no botão **Iniciar Com** .

Para saber mais sobre o rastreamento de **Proxy da Web** do Microsoft Message Analyzer, consulte [Provedor Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

O rastreamento interno de **Proxy da Web** no Microsoft Message Analyzer é com base no Fiddler; ele pode capturar o tráfego do lado do cliente e exibir as mensagens HTTPS não criptografadas. O rastreamento **Proxy da Web** funciona ao configurar um proxy local para todo o tráfego HTTP e HTTPS que dá acesso às mensagens não criptografadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnóstico dos problemas de rede usando o Microsoft Message Analyzer
Além de usar o rastreamento de **Proxy da Web** do Microsoft Message Analyzer para capturar detalhes do tráfego HTTP/HTTPS entre o aplicativo do cliente e o serviço de armazenamento, você pode usar também o rastreamento interno **Camada de Link Local** para capturar as informações do pacote de rede. Isso permite que você capture os dados similares aos que você pode capturar com o Wireshark e que diagnostique os problemas de rede tal como os pacotes ignorados.

A seguinte captura de tela mostra um exemplo de rastreamento de **Camada de Link Local** com algumas mensagens **informativas** na coluna **DiagnosisTypes**. Clicar no ícone na coluna **DiagnosisTypes** mostra detalhes da mensagem. Neste exemplo, o servidor retransmitiu a mensagem nº 305 porque ele não recebeu uma confirmação do cliente:

![][9]

Quando você cria uma sessão de rastreamento no Microsoft Message Analyzer, é possível especificar filtros para reduzir a quantidade de ruídos no rastreamento. Na página **Capturar/Rastrear** na qual você define o rastreamento, clique no link **Configurar** próximo a **Microsoft-Windows-NDIS-PacketCapture**. A seguinte captura de tela mostra a configuração que filtra o tráfego TCP para os endereços de IP de três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreamento de Camada de Link Local do Microsoft Message Analyzer, consulte [Provedor do Microsoft-PEF-NDIS-PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Anexo 4: Usando o Excel para exibir as métricas e os dados de log
Muitas ferramentas permitem que você baixe os dados de métricas de armazenamento a partir do armazenamento de tabela do Azure em um formato delimitado que o torna fácil para se carregado no Excel para exibição e análise. Os dados de log de armazenamento do armazenamento de blob do Azure já estão em um formato delimitado que pode ser carregado no Excel. Entretanto, você precisará adicionar cabeçalhos apropriados às colunas com base na informação no [Formato de Log Analítico de Armazenamento](http://msdn.microsoft.com/library/azure/hh343259.aspx) e no [Esquema de Tabela de Métricas Analíticas de Armazenamento](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar os dados de log de armazenamento para o Excel após ter baixado do armazenamento de blob:

* No menu **Dados**, clique em **Do Texto**.
* Procure o arquivo de log que deseja exibir e clique em **Importar**.
* Na etapa 1 do **Assistente de Importação de Texto**, selecione **Delimitado**.

Na etapa 1 do **Assistente de Importação de Texto**, selecione **Ponto e vírgula** como o único delimitador e escolha aspas duplas como **Qualificador de texto**. Clique em **Concluir** e escolha onde colocar os dados na sua pasta de trabalho.

### <a name="appendix-5"></a>Anexo 5: Monitoramento com o Application Insights no Visual Studio Team Services
Você pode também usar o recurso Application Insights no Visual Studio Team Services como parte do seu monitoramento de desempenho e disponibilidade. Essa ferramenta pode:

* Garantir que seu aplicativo da Web esteja disponível e respondendo. Se o seu aplicativo é um site ou um aplicativo de dispositivo que usa um serviço Web, você pode testar a sua URL a cada minuto de locais ao redor do mundo e ser avisado se houver um problema.
* Diagnostique rapidamente qualquer problema de desempenho ou exceções no seu serviço da Web. Descubra se a CPU ou outros recursos estão sendo alongados, receba rastreamento de linhas de exceções e pesquise facilmente pelos rastreamentos de log. Se o desempenho do aplicativo cair abaixo dos limites aceitáveis, nós poderemos lhe enviar um email. Você pode monitorar os serviços Web .NET e Java.

Você pode encontrar mais informações em [O que é o Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introdução]: #introduction
[Como esse guia está organizado]: #how-this-guide-is-organized

[Monitoramento do seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitoramento da integridade do serviço]: #monitoring-service-health
[Monitoramento de capacidade]: #monitoring-capacity
[Monitoramento de disponibilidade]: #monitoring-availability
[Monitoramento de desempenho]: #monitoring-performance

[Diagnóstico de problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de integridade do serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Diagnóstico de erros]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de log de armazenamento]: #storage-logging-tools
[Uso de ferramentas de log de rede]: #using-network-logging-tools

[Rastreamento de ponta a ponta]: #end-to-end-tracing
[Correlacionamento de dados de log]: #correlating-log-data
[ID de solicitação do cliente]: #client-request-id
[ID de solicitação do servidor]: #server-request-id
[Carimbos de data/hora]: #timestamps

[Diretrizes para solução de problemas]: #troubleshooting-guidance
[As métricas mostram alta AverageE2ELatency e baixa AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[As métricas mostram baixa AverageE2ELatency e baixa AverageServerLatency, mas o cliente está recebendo uma latência alta]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[As métricas mostram alta AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Você está sofrendo atrasos inesperados na entrega de mensagens na fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[As métricas mostram um aumento em PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento transitório em PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente em erro de PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[As métricas mostram um aumento em PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[As métricas mostram um aumento em PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está recebendo mensagens HTTP 403 (Proibido)]: #the-client-is-receiving-403-messages
[O cliente está recebendo mensagens HTTP 404 (Não encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo excluiu anteriormente o objeto]: #client-previously-deleted-the-object
[Um problema de autorização de assinatura de acesso compartilhado (SAS)]: #SAS-authorization-issue
[O código JavaScript do lado do cliente não tem permissão para acessar o objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está recebendo mensagens HTTP 409 (Conflito)]: #the-client-is-receiving-409-messages

[As métricas mostram uma baixa PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors]: #metrics-show-low-percent-success
[As métricas de capacidade mostram um aumento inesperado em uso de capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[Você está enfrentando reinicializações inesperadas das máquinas virtuais que contêm um grande número de VHDs anexados]: #you-are-experiencing-unexpected-reboots
[Seu problema apareceu por usar o emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[O recurso "X” não está funcionando no emulador de armazenamento]: #feature-X-is-not-working
[Erro "O valor de um dos cabeçalhos HTTP não está no formato correto" ao usar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[A execução do emulador de armazenamento requer privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Você encontrou problemas ao instalar o SDK do Azure para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Você tem um problema diferente com um serviço de armazenamento]: #you-have-a-different-issue-with-a-storage-service

[Anexos]: #appendices
[Anexo 1: Usando o Fiddler para capturar o tráfego HTTP e HTTPS]: #appendix-1
[Anexo 2: Usando o Wireshark para capturar o tráfego de rede]: #appendix-2
[Anexo 3: Usando o Microsoft Message Analyzer para capturar o tráfego de rede]: #appendix-3
[Anexo 4: Usando o Excel para exibir as métricas e os dados de log]: #appendix-4
[Anexo 5: Monitoramento com o Application Insights no Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
