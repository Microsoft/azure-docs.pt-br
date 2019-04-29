---
title: Solucionando problemas do Armazenamento do Azure com Analisador de Mensagem e Diagnóstico | Microsoft Docs
description: Um tutorial que demonstra a solução de problemas ponta a ponta com Análise de Armazenamento do Azure, AzCopy e Analisador de Mensagem da Microsoft
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f88a560d4fa819a055534530ddc0862e4aa330fe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098602"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Solução de problemas ponta a ponta usando Métricas de Armazenamento do Azure e Registro em Log, AzCopy e Analisador de Mensagem
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnóstico e solução de problemas são habilidades chaves para a criação e o suporte a aplicativos de clientes com o Armazenamento do Microsoft Azure. Devido à natureza distribuída de um aplicativo do Azure, diagnosticar e solucionar problemas de desempenho e erros pode ser mais complexo do que em ambientes tradicionais.

Neste tutorial, demonstramos como identificar alguns erros do cliente que podem afetar o desempenho e solucionar esses erros de ponta a ponta usando as ferramentas fornecidas pela Microsoft e o Armazenamento do Azure, a fim de otimizar o aplicativo cliente.

Este tutorial fornece uma exploração prática de um cenário de solução de problemas de ponta a ponta. Para obter um guia conceitual detalhado para solucionar problemas dos aplicativos de armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas de Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Ferramentas para solucionar problemas de aplicativos de armazenamento do Azure
Para solucionar problemas de aplicativos cliente que usam o armazenamento do Microsoft Azure, você pode usar uma combinação de ferramentas para determinar quando um problema ocorreu e o que pode ser a causa do problema. Essas ferramentas incluem:

* **Análise de Armazenamento do Azure**. [A Análise de Armazenamento do Azure](/rest/api/storageservices/Storage-Analytics) fornece métricas e registro em log para o Armazenamento do Azure.

  * **A métrica de armazenamento** controla as métricas de transação e as métricas de capacidade para sua conta de armazenamento. Usando métricas, você pode determinar o desempenho do seu aplicativo de acordo com uma variedade de medidas diferentes. Consulte o [Esquema da Tabela de Métricas de Análise do Armazenamento](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) para obter mais informações sobre os tipos de métricas controladas pela Análise de Armazenamento.
  * **O log de armazenamento** registra cada solicitação dos serviços de Armazenamento do Azure em um log do servidor. O log registra dados detalhados para cada solicitação, incluindo a operação executada, o status da operação e informações de latência. Consulte o [Formato do Log de Análise de Armazenamento](/rest/api/storageservices/Storage-Analytics-Log-Format) para obter mais informações sobre os dados de solicitação e resposta gravados nos logs pela Análise de Armazenamento.

* **Portal do Azure**. É possível configurar o log e as métricas da conta de armazenamento no [portal do Azure](https://portal.azure.com). Você também pode exibir grafos que mostram o desempenho do seu aplicativo ao longo do tempo e configurar alertas para notificá-lo se seu aplicativo for executado de forma diferente do esperado para uma métrica especificada.

    Consulte [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md) para obter informações sobre como configurar o monitoramento no portal do Azure.
* **AzCopy**. Os logs do servidor do Armazenamento do Azure são armazenados como blobs, então você pode usar o AzCopy para copiar os blobs de log para um diretório local para análise usando o Analisador de Mensagem da Microsoft. Confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) para obter mais informações sobre o AzCopy.
* **Analisador de Mensagem da Microsoft**. O Analisador de Mensagem é uma ferramenta que consome os arquivos de log e exibe dados de log em um formato visual que torna mais fácil a filtragem, pesquisa e agrupamento de dados de log em conjuntos úteis que você pode usar para analisar erros e problemas de desempenho. Consulte o [Guia Operacional do Analisador de Mensagem da Microsoft](https://technet.microsoft.com/library/jj649776.aspx) para obter mais informações sobre o Analisador de Mensagem.

## <a name="about-the-sample-scenario"></a>Sobre o cenário de exemplo
Para este tutorial, vamos examinar um cenário onde as métricas de armazenamento do Azure indicam uma taxa de sucesso de porcentagem baixa de um aplicativo que chama o armazenamento do Azure. A métrica da taxa de sucesso de percentual baixo (mostrada como **PercentSuccess** no [portal do Azure](https://portal.azure.com) e nas tabelas de métricas) acompanha as operações com êxito, mas que retornam um código de status HTTP maior que 299. Nos arquivos de log do lado do servidor, essas operações são registradas com um status de transação de **ClientOtherErrors**. Para obter mais detalhes sobre a métrica de sucesso de porcentagem baixa, consulte [As métricas mostram o PercentSuccess baixo ou as entradas do log de análise têm operações com status de transação de ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operações de armazenamento do Azure podem retornar códigos de status HTTP maior 299 como parte de sua funcionalidade normal. Porém esses erros em alguns casos indicam que você poderá otimizar o aplicativo cliente para melhorar o desempenho.

Nesse cenário, vejamos uma taxa de sucesso de porcentagem baixa que seja algo abaixo de 100%. Você pode escolher um nível diferente de métrica, no entanto, de acordo com suas necessidades. É recomendável que, durante o teste do seu aplicativo, você estabeleça uma tolerância de linha de base para suas principais métricas de desempenho. Por exemplo, você pode determinar, com base nos testes, que seu aplicativo deve ter uma taxa de porcentagem de êxitos consistente de 90% ou 85%. Se seus dados de métricas mostram que o aplicativo desviam-se desse número, você pode investigar o que está causando o aumento.

Em nosso cenário de exemplo, depois que estabelecemos que a métrica de taxa de porcentagem de êxitos está abaixo de 100%, vamos examinar os logs para localizar os erros que correlacionam as métricas e usá-las para descobrir o que está causando a taxa inferior de sucesso de porcentagem. Vamos examinar especificamente erros no intervalo de 400. Em seguida, examinaremos com mais detalhes os erros 404 (não encontrado).

### <a name="some-causes-of-400-range-errors"></a>Algumas causas de erros no intervalo 400
Os exemplos a seguir mostram uma amostra de alguns erros de intervalo 400 para solicitações do Armazenamento de Blob do Azure e suas possíveis causas. Qualquer um desses erros, bem como erros no intervalo 300 e 500, podem contribuir para uma taxa de sucesso de porcentagem baixa.

Observe que as listas abaixo estão longe de serem completas. Consulte [Status e Códigos de Erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no MSDN para obter detalhes sobre os erros gerais de Armazenamento do Azure e sobre os erros específicos de cada um dos serviços de armazenamento.

**Exemplos do Código de Status 404 (Não Encontrado)**

Ocorre quando uma operação de leitura em um contêiner ou blob falha porque o contêiner ou blob não foi encontrado.

* Ocorre se um contêiner ou blob tiver sido excluído por outro cliente antes desta solicitação.
* Ocorre se você estiver usando uma chamada à API que cria o contêiner ou blob depois de verificar se ele existe. As APIs CreateIfNotExists realizam uma chamada HEAD para verificar a existência do contêiner ou blob. Se ele não existir, será retornado um erro 404 e, em seguida, uma segunda chamada PUT é feita para gravar o contêiner ou blob.

**Exemplos do Código de Status 409 (Conflito)**

* Ocorre se você usar uma API para criar um novo contêiner ou blob sem verificar primeiro a existência e se um contêiner ou blob com esse nome já existe.
* Ocorre se um contêiner está sendo excluído e você tentar criar um novo contêiner com o mesmo nome antes da operação de exclusão ser concluída.
* Ocorre se você especificar uma concessão em um contêiner ou blob e já houver uma concessão presente.

**Exemplos do Código de Status 412 (Falha na Pré-condição)**

* Ocorre quando a condição especificada por um cabeçalho condicional não foi atendida.
* Ocorre quando a ID de concessão especificada não coincide com a ID de concessão no contêiner ou blob.

## <a name="generate-log-files-for-analysis"></a>Gerar arquivos de log para análise
Neste tutorial, usaremos o Analisador de Mensagem para trabalhar com três tipos diferentes de arquivos de log, embora você possa optar por trabalhar com qualquer um desses:

* O **log do servidor**que é criado quando você habilita o log de armazenamento do Azure. O log do servidor contém dados sobre cada operação de chamada com um dos serviços de armazenamento do Azure: blob, fila, tabela e arquivo. O log do servidor indica qual operação foi chamada e o código de status retornado, bem como outros detalhes sobre a solicitação e resposta.
* O **log do cliente .NET**que é criado quando você habilita o log de cliente de dentro de seu aplicativo .NET. O log de cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.
* O **log de rastreamento de rede HTTP**que coleta dados nos dados de solicitação e resposta HTTP/HTTPS, inclusive para as operações no Armazenamento do Azure. Neste tutorial, vamos gerar o rastreamento de rede por meio do Analisador de Mensagem.

### <a name="configure-server-side-logging-and-metrics"></a>Configurar o log de servidor e métricas
Primeiro, precisaremos configurar o log de armazenamento do Azure e métricas, para que tenhamos dados do lado do serviço para analisar. É possível configurar o log e as métricas de diversas maneiras, por meio do [portal do Azure](https://portal.azure.com), usando o PowerShell ou de forma programática. Ver [habilitar as métricas](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) e [habilitar registro em log](storage-analytics-logging.md#enable-storage-logging) para obter detalhes sobre como configurar o registro em log e métricas.

### <a name="configure-net-client-side-logging"></a>Configurar o log de cliente .NET
Para configurar o log do cliente de um aplicativo .NET, habilite o diagnóstico do .NET no arquivo de configuração do aplicativo (Web.config ou App.config). Confira [Registro em log do Cliente usando a Biblioteca do Cliente de Armazenamento do .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) e [Registro em log do Cliente com o SDK de Armazenamento do Microsoft Azure para Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) no MSDN para obter detalhes.

O log do lado do cliente inclui informações detalhadas sobre como o cliente prepara a solicitação e recebe e processa a resposta.

A biblioteca de cliente de armazenamento armazena dados de log do cliente no local especificado no arquivo de configuração do aplicativo (Web.config ou App.config).

### <a name="collect-a-network-trace"></a>Coletar um rastreamento de rede
Você pode usar o Analisador de Mensagem para coletar um rastreamento de rede HTTP/HTTPS, enquanto o aplicativo cliente está em execução. O Analisador de Mensagem usa o [Fiddler](https://www.telerik.com/fiddler) no back-end. Antes de você coletar o rastreamento de rede, recomendamos que você configure o Fiddler para registrar o tráfego HTTPS não criptografado:

1. Instale o [Fiddler](https://www.telerik.com/download/fiddler).
2. Inicie o Fiddler.
3. Selecione **Ferramentas| Opções do Fiddler**.
4. Na caixa de diálogo Opções, verifique se **Capturar CONEXÕES HTTPS** e **Descriptografar Tráfego HTTPS** estão selecionadas, conforme mostrado abaixo.

![Configurar Opções do Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Para o tutorial, colete e salve um rastreamento de rede pela primeira vez no Analisador de Mensagem e crie uma sessão de análise para analisar os logs e o rastreamento. Para coletar um rastreamento de rede no Analisador de Mensagem:

1. No Analisador de Mensagem, selecione **Arquivo | Rastreamento Rápido | HTTPS Sem Criptografia**.
2. O rastreamento será iniciado imediatamente. Selecione **Parar** para parar o rastreamento para que possamos configurá-lo para rastrear apenas o tráfego de armazenamento.
3. Selecione **Editar** para editar a sessão de rastreamento.
4. Selecione o link **Configurar** à direita do provedor ETW **Microsoft-Pef-WebProxy** .
5. Na caixa de diálogo **Configurações Avançadas**, clique na guia **Provedor**.
6. No campo **Filtro do Nome de Host** , especifique os pontos de extremidade do armazenamento, separados por espaços. Por exemplo, você pode especificar os pontos de extremidade como se segue; altere `storagesample` para o nome da sua conta de armazenamento:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Saia da caixa de diálogo e clique em **Reiniciar** para começar a coleta do rastreamento com o filtro do nome de host no local, para que somente o tráfego da rede de armazenamento do Azure seja incluído no rastreamento.

> [!NOTE]
> Após coletar o rastreamento de rede, é altamente recomendável que você reverta as configurações que possa ter alterado no Fiddler para descriptografar o tráfego HTTPS. Na caixa de diálogo Opções do Fiddler, desmarque as caixas de seleção **Capturar CONEXÕES HTTPS** e **Descriptografar Tráfego HTTPS**.
>
>

Consulte [Usando os Recursos de Rastreamento de Rede](https://technet.microsoft.com/library/jj674819.aspx) no Technet para obter mais detalhes.

## <a name="review-metrics-data-in-the-azure-portal"></a>Examinar dados de métricas no portal do Azure
Quando aplicativo já estiver sendo executado por algum tempo, é possível examinar os gráficos de métricas exibidos no [portal do Azure](https://portal.azure.com) para observar o desempenho do serviço.

Primeiro, navegue para sua conta de armazenamento no portal do Azure. Por padrão, um gráfico de monitoramento com a métrica **Percentual de êxito** é exibido na folha da conta. Se você modificou o gráfico anteriormente para exibir métricas diferentes, adicione a métrica **Percentual de êxito**.

Agora você verá **Percentual de êxito** no gráfico de monitoramento, junto com as outras métricas adicionadas. No cenário que investigaremos a seguir analisando os logs do Analisador de Mensagem, a taxa de sucesso de percentual é um pouco inferior a 100%.

Para obter mais detalhes sobre como adicionar e personalizar gráficos de métricas, consulte [Personalizar gráficos de métricas](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Pode levar algum tempo para que os dados de métricas sejam exibidos no portal do Azure depois que você habilitar a métrica de armazenamento. Isso ocorre porque as métricas por hora da hora anterior só serão exibidas no portal do Azure quando a hora atual tiver decorrido. Além disso, atualmente, as métricas por minuto não são exibidas no portal do Azure. Dessa forma, dependendo de quando você habilitar a métrica, pode levar até duas horas para ver os dados dela.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Use AzCopy para copiar logs do servidor para um diretório local
O Armazenamento do Azure grava os dados de log do servidor para blobs, enquanto as métricas são gravadas em tabelas. Os blobs de log estão disponíveis no contêiner `$logs` conhecido para sua conta de armazenamento. Blobs de log são nomeados hierarquicamente por ano, mês, dia e hora, para que você possa localizar facilmente o intervalo de tempo que deseja investigar. Por exemplo, na conta `storagesample`, o contêiner para os blobs de log para 02/01/2015, de 8:00-9:00, é `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Os blobs individuais nesse contêiner são nomeados em sequência, começando com `000000.log`.

Você pode usar a ferramenta de linha de comando AzCopy para baixar esses arquivos de log do servidor para um local de sua escolha no computador local. Por exemplo, você pode usar o comando a seguir para baixar os arquivos de log para as operações de blob que ocorreram no dia 2 de janeiro de 2015 para a pasta `C:\Temp\Logs\Server`; substitua `<storageaccountname>` pelo nome de sua conta de armazenamento e `<storageaccountkey>` pela chave de acesso da conta:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
O AzCopy está disponível para download na página [Downloads do Azure](https://azure.microsoft.com/downloads/) . Para obter detalhes sobre como usar o AzCopy, confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md).

Para obter informações adicionais sobre como baixar os logs do servidor, confira [Download Storage Logging log data](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)(Baixar dados de log do Log de Armazenamento).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Usar o Analisador de Mensagem da Microsoft para analisar dados de log
O Analisador de Mensagem da Microsoft é uma ferramenta para capturar, exibir e analisar o tráfego, eventos e outras mensagens de sistema ou aplicativo em cenários de diagnóstico e solução de problemas de mensagens de protocolo. O Analisador de Mensagem também permite carregar, agregar e analisar dados de log e arquivos de rastreamento salvos. Para obter mais informações sobre o Analisador de Mensagem, consulte [Guia Operacional do Analisador de Mensagem da Microsoft](https://technet.microsoft.com/library/jj649776.aspx).

O Analisador de Mensagem inclui ativos para o Armazenamento do Azure que ajudam você a analisar logs de rede, cliente e servidor. Nesta seção, abordaremos como usar essas ferramentas para resolver o problema de baixa porcentagem de êxitos nos logs de armazenamento.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Baixe e instale o Analisador de Mensagem e os ativos de Armazenamento do Azure
1. Baixe o [Analisador de Mensagem](https://www.microsoft.com/download/details.aspx?id=44226) no Centro de Download da Microsoft e execute o instalador.
2. Inicie o Analisador de Mensagem.
3. No menu **Ferramentas**, selecione **Gerenciador de Ativos**. Na caixa de diálogo **Gerenciador de Ativos**, escolha **Downloads** e filtre por **Armazenamento do Azure**. Você verá os ativos de Armazenamento do Azure, conforme mostrado na figura abaixo.
4. Clique em **Sincronizar Todos os Itens Exibidos** para instalar os Ativos de Armazenamento do Azure. Os ativos disponíveis incluem:
   * **Regras de cores do Armazenamento do Azure:** As regras de cores do Armazenamento do Azure permitem que você defina filtros especiais que usam estilos de fontes, texto e cor para realçar as mensagens que contêm informações específicas em um rastreamento.
   * **Gráficos do Armazenamento do Azure:** Os gráficos do Armazenamento do Azure são gráficos predefinidos que representam os dados de log do servidor. Observe que para usar os gráficos de Armazenamento do Azure no momento, você pode carregar apenas o log do servidor na Grade de Análise.
   * **Analisadores do Armazenamento do Azure:** Os analisadores do Armazenamento do Azure analisam os logs do cliente, do servidor e do HTTP do Armazenamento do Azure para exibi-los na Grade de Análise.
   * **Filtros do Armazenamento do Azure:** Os filtros do Armazenamento do Azure são critérios predefinidos que você pode usar para consultar os dados na Grade de Análise.
   * **Layouts de Exibição do Armazenamento do Azure:** Os layouts de exibição do Armazenamento do Azure são layouts e agrupamentos de colunas predefinidos na Grade de Análise.
5. Reinicie o Analisador de Mensagem depois de instalar os ativos.

![Gerenciador de Ativos do Analisador de Mensagem](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Instale todos os ativos de Armazenamento do Azure mostrados para os fins deste tutorial.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Importar os arquivos de log para o Analisador de Mensagem
Você pode importar todos os arquivos de log salvos (do servidor, cliente e rede) para uma única sessão do Analisador de Mensagem da Microsoft para análise.

1. No menu **Arquivo** no Analisador de Mensagem da Microsoft, clique em **Nova Sessão**, em seguida, clique em **Sessão em Branco**. Na caixa de diálogo **Nova Sessão** , digite um nome para a sessão de análise. No painel **Detalhes da Sessão**, clique no botão **Arquivos**.
2. Para carregar os dados de rastreamento de rede gerados pelo Analisador de Mensagem, clique em **Adicionar Arquivos**, navegue até o local onde você salvou o arquivo .matp da sua sessão de rastreamento da Web, selecione o arquivo .matp e clique em **Abrir**.
3. Para carregar os dados de log do servidor, clique em **Adicionar Arquivos**, navegue até o local onde você baixou os logs do servidor, selecione os arquivos de log para o intervalo de tempo que deseja analisar e clique em **Abrir**. Em seguida, no painel **Detalhes da Sessão**, defina a lista suspensa **Configuração do Log de Texto** de cada arquivo de log do servidor para **AzureStorageLog** para garantir que o Analisador de Mensagem da Microsoft possa analisar corretamente o arquivo de log.
4. Para carregar os dados de log do cliente, clique em **Adicionar Arquivos**, navegue até o local onde você salvou os logs do cliente, selecione os arquivos de log que deseja analisar e clique em **Abrir**. Em seguida, no painel **Detalhes da Sessão**, defina a lista suspensa **Configuração do Log de Texto** de cada arquivo de log do cliente para **AzureStorageClientDotNetV4** para garantir que o Analisador de Mensagem da Microsoft possa analisar corretamente o arquivo de log.
5. Clique em **Iniciar** na caixa de diálogo **Nova Sessão** para carregar e analisar os dados do log. Os dados de log são exibidos na grade de análise do Analisador de Mensagem.

A figura abaixo mostra uma sessão de exemplo configurada com o servidor, cliente e arquivos de log de rastreamento de rede.

![Configurar Sessão do Analisador de Mensagem](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Observe que o Analisador de Mensagem carrega arquivos de log na memória. Se você tiver um grande conjunto de dados de log, é recomendável filtrá-los para obter o melhor desempenho do Analisador de Mensagem.

Primeiro, determine o intervalo de tempo que você está interessado em analisar e mantenha esse período o mais breve possível. Em muitos casos, é recomendável examinar um período de minutos ou horas, no máximo. Importe o menor conjunto de logs que pode atender às suas necessidades.

Se você ainda tiver uma grande quantidade de dados de log, convém especificar uma sessão de filtro para filtrar os dados do log antes de carregá-los. Na caixa **Filtro da Sessão**, selecione o botão **Biblioteca** para escolher um filtro predefinido. Por exemplo, escolha **Filtro de Hora Global I** nos filtros do Armazenamento do Azure para filtrar em um intervalo de tempo. Você pode editar os critérios de filtro para especificar os carimbos de data/hora inicial e final para o intervalo que você deseja ver. Você também pode filtrar um código de status específico. Por exemplo, você pode optar por carregar apenas entradas de log em que o código de status é 404.

Para obter mais informações sobre como importar os dados de log para o Analisador de Mensagem da Microsoft, consulte [Recuperar Dados da Mensagem](https://technet.microsoft.com/library/dn772437.aspx) no TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Use a ID de solicitação do cliente para correlacionar dados do arquivo de log
A biblioteca de cliente de armazenamento gera automaticamente uma única ID de solicitação do cliente para cada solicitação. Esse valor é gravado no log do cliente, o log do servidor e o rastreamento de rede, para que possa ser usado para correlacionar dados em todos os três logs no Analisador de Mensagem. Consulte [ID de solicitação do cliente](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) para obter informações adicionais sobre a ID de solicitação do cliente.

As seções a seguir descrevem como usar os modos de exibição de layout pré-configurados e personalizados para correlacionar e agrupar dados com base na ID de solicitação do cliente.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Selecione um layout de exibição para exibir na grade de análise
Os ativos de armazenamento para o Analisador de Mensagem incluem Layouts de exibição do Armazenamento do Azure, que são exibições pré-configuradas que você pode usar para exibir seus dados com agrupamentos úteis e colunas para cenários diferentes. Você também pode criar layouts de modo de exibição personalizado e salvá-los para reutilização.

A figura abaixo mostra o menu **Layout de Exibição**, disponível selecionando **Layout de Exibição** na faixa de opções da barra de ferramentas. Os layouts de exibição do Armazenamento do Azure estão agrupados sob o nó **Armazenamento do Azure** no menu. Você pode pesquisar `Azure Storage` na caixa de pesquisa para filtrar apenas os layouts de exibição do Armazenamento do Azure. Você também pode selecionar a estrela ao lado de um layout de exibição para torná-lo um favorito e exibi-lo na parte superior do menu.

![Menu Layout de Exibição](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Para começar, selecione **Agrupados por Módulo e ClientRequestID**. Esse layout de exibição agrupa os dados de log de todos os três logs, primeiro pela ID de solicitação do cliente, depois pelo arquivo de log de origem (ou **Módulo** no Analisador de Mensagem). Nesta exibição, você pode fazer uma busca detalhada em uma ID de solicitação de cliente específico e ver os dados de todos os três arquivos de log para essa ID de solicitação de cliente.

A figura abaixo mostra esse modo de exibição de layout aplicado a dados de log de exemplo, com um subconjunto de colunas exibidas. Você pode ver que, para uma ID de solicitação de cliente específico, a grade de análise exibe os dados do log do cliente, o log do servidor e o rastreamento de rede.

![Layout de Exibição do Armazenamento do Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Arquivos de log diferentes têm colunas diferentes, portanto, quando dados de vários arquivos de log são exibidos na grade de análise, algumas colunas podem não conter nenhum dado para uma determinada linha. Por exemplo, na figura acima, as linhas de log do cliente não mostram dados para as colunas **Carimbo de data/hora**, **Tempo Decorrido**, **Origem**e **Destino** porque essas colunas não existem no log do cliente, mas existem no rastreamento de rede. Da mesma forma, a coluna **Carimbo de data/hora** exibe os dados do carimbo de data/hora do log do servidor, mas ão há dados exibidos para as colunas **Tempo Decorrido**, **Origem** e **Destino**, que não fazem parte do log do servidor.
>
>

Além de usar os layouts do modo de Armazenamento do Azure, você também pode definir e salvar seus próprios layouts de exibição. Você também pode selecionar outros campos desejados para o agrupamento de dados e salvar o agrupamento como parte do seu layout personalizado.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Aplicar regras de cores à grade de análise
Os ativos de armazenamento também incluem regras de cores, que oferecem uma maneira visual de identificar os diferentes tipos de erros na grade de análise. As regras de cores predefinidas aplicam-se aos erros HTTP, por isso elas aparecem somente para o rastreamento de rede e de log do servidor.

Para aplicar as regras da cor, selecione **Regras da Cor** na faixa de opções da barra de ferramentas. Você verá as regras de cores do Armazenamento do Azure no menu. Para o tutorial, selecione **Erros do Cliente (StatusCode entre 400 e 499)**, conforme mostrado na figura abaixo.

![Layout de Exibição do Armazenamento do Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Além de usar as regras de cores de Armazenamento do Azure, você também pode definir e salvar suas próprias regras de cores.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Agrupar e filtrar os dados de log para localizar erros de intervalo de 400
Em seguida, vamos agrupar e filtrar os dados de log para localizar todos os erros no intervalo de 400.

1. Localize a coluna **StatusCode** na Grade de Análise, clique com o botão direito no cabeçalho da coluna e selecione **Agrupar**.
2. Em seguida, agrupe a coluna **ClientRequestId** . Você verá que os dados na grade de análise agora estão organizados por código de status e por ID de solicitação do cliente.
3. Exiba a janela de ferramentas Filtro de exibição se ela já não estiver exibida. Na faixa de opções da barra de ferramentas, selecione **Janelas de Ferramenta** e **Filtro de Exibição**.
4. Para filtrar os dados de log para exibir apenas os erros do intervalo 400, adicione os seguintes critérios de filtro à janela **Filtro de Exibição** e clique em **Aplicar**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

A figura a seguir mostra os resultados desse agrupamento e filtro. Expandir o campo **ClientRequestID** abaixo do agrupamento para o código de status 409, por exemplo, mostra uma operação que resultou nesse código de status.

![Layout de Exibição do Armazenamento do Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Depois de aplicar esse filtro, você verá que são excluídas linhas do log do cliente, pois o log do cliente não inclui uma coluna **StatusCode** . Para começar, vamos examinar o servidor e os logs de rastreamento de rede para localizar os erros 404 e vamos voltar para o log de cliente para examinar as operações de cliente que levou a eles.

> [!NOTE]
> Você poderá filtrar na coluna **StatusCode** e ainda exibir os dados de todos os três logs, incluindo o log do cliente, se adicionar uma expressão ao filtro que inclui entradas de log nas quais o código de status é nulo. Para criar esta expressão de filtro, use:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Esse filtro retorna todas as linhas de log do cliente e apenas as linhas de log do servidor e log HTTP em que o código de status é maior que 400. Se você aplicá-lo ao layout do modo de exibição agrupado por ID de solicitação do cliente e do módulo, poderá pesquisar ou percorrer as entradas de log para encontrar aqueles em que todos os três logs são representados.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>Filtrar dados de log para localizar erros 404
Os ativos de armazenamento incluem filtros predefinidos que você pode usar para restringir os dados de log para localizar os erros ou tendências que você está procurando. Em seguida, vamos aplicar dois filtros predefinidos: um que filtra o servidor e os logs de rastreamento de rede para erros 404 e outro que filtra os dados em um intervalo de tempo especificado.

1. Exiba a janela de ferramentas Filtro de exibição se ela já não estiver exibida. Na faixa de opções da barra de ferramentas, selecione **Janelas de Ferramenta** e **Filtro de Exibição**.
2. Na janela Filtro de Exibição, selecione **Biblioteca** e pesquise `Azure Storage` para encontrar os filtros do Armazenamento do Azure. Selecione o filtro para as mensagens **404 (Não Encontrado) em todos os logs**.
3. Exiba o menu **Biblioteca** novamente, localize e selecione o **Filtro de Hora Global**.
4. Edite os carimbos de hora mostrados no filtro para o intervalo que você deseja exibir. Isso ajudará a limitar o intervalo de dados a serem analisados.
5. Seu filtro deve ser semelhante ao exemplo a seguir. Clique em **Aplicar** para aplicar o filtro à Grade de Análise.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Layout de Exibição do Armazenamento do Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analisar os dados de log
Agora que você já agrupou e filtrou os dados, poderá examinar os detalhes de solicitações individuais que geraram erros 404. No layout do modo de exibição atual, os dados são agrupados por ID de solicitação de cliente e, em seguida, pela origem de log. Uma vez que estamos filtrando solicitações onde o campo StatusCode contém 404, veremos apenas o servidor e os dados de rastreamento de rede, não os dados de log do cliente.

A figura a seguir mostra uma solicitação específica em que uma operação Get Blob gerou um 404 porque o blob não existe. Observe que algumas colunas foram removidas do modo de exibição padrão para exibir os dados relevantes.

![Logs de Rastreamento Filtrados do Servidor e da Rede](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Em seguida, correlacionamos essa ID de solicitação do cliente com os dados de log do cliente para ver as ações que o cliente tomou quando o erro ocorreu. Você pode exibir uma nova exibição de grade de análise para essa sessão para exibir os dados de log do cliente, que é aberto em uma segunda guia:

1. Primeiro, copie o valor do campo **ClientRequestId** para a área de transferência. Você pode fazer isso selecionando qualquer linha, localizando o campo **ClientRequestId**, clicando com o botão direito no valor dos dados e escolhendo **Copiar 'ClientRequestId'**.
2. Na faixa de opções da barra de ferramentas, selecione **Novo Visualizador** e **Grade de Análise** para abrir uma nova guia. A nova guia mostra todos os dados em seus arquivos de log, sem agrupamento, filtragem ou regras de cores.
3. Na faixa de opções da barra de ferramentas, selecione **Layout de Exibição** e **Todas as Colunas do Cliente .NET** na seção **Armazenamento do Azure**. Esse layout do modo de exibição mostra dados do log do cliente, bem como os logs de rastreamento de servidor e rede. Por padrão, ele é classificado pela coluna **MessageNumber** .
4. Em seguida, pesquise o log do cliente para a ID de solicitação do cliente. Na faixa de opções da barra de ferramentas, selecione **Localizar Mensagens** e especifique um filtro personalizado na ID de solicitação do cliente no campo **Localizar**. Use esta sintaxe para o filtro, especificando sua própria ID de solicitação de cliente:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

O Analisador de Mensagem localiza e seleciona a primeira entrada de log em que os critérios de pesquisa correspondem à ID da solicitação de cliente. No log do cliente, existem várias entradas para cada ID de solicitação do cliente, portanto, você pode agrupá-las no campo **ClientRequestId** para facilitar vê-las juntas. A figura abaixo mostra todas as mensagens no log do cliente para a ID de solicitação do cliente especificada.

![Log do cliente mostrando erros 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Usando os dados mostrados em layouts de exibição nessas duas guias, você pode analisar os dados da solicitação para determinar o que pode ter causado o erro. Você também pode examinar solicitações que precedem esta para ver se um evento anterior pode ter levado ao erro 404. Por exemplo, você pode revisar as entradas de log do cliente anteriores a essa ID de solicitação do cliente para determinar se o blob pode ter sido excluído ou se o erro ocorreu devido à aplicação do cliente chamar uma API CreateIfNotExists em um contêiner ou blob. No log do cliente, você pode encontrar o endereço do blob no campo **Descrição**. Nos logs do servidor e de rastreamento de rede, essas informações são exibidas no campo **Resumo**.

Se você souber o endereço do blob que gerou o erro 404, poderá investigar ainda mais. Se você pesquisar as entradas de log para outras mensagens associadas às operações no mesmo blob, poderá verificar se o cliente já foi excluído da entidade.

## <a name="analyze-other-types-of-storage-errors"></a>Analisar outros tipos de erros de armazenamento
Agora que você está familiarizado com o uso do Analisador de Mensagem para analisar os dados de log, poderá analisar outros tipos de erros usando a exibição de layouts, regras de cores e pesquisa/filtragem. As tabelas a seguir listam alguns problemas possíveis e os critérios de filtro que você pode usar para localizá-los. Para obter mais informações sobre como construir filtros e a linguagem de filtragem do Analisador de Mensagem, consulte [Filtrando Dados de Mensagem](https://technet.microsoft.com/library/jj819365.aspx).

| Para Investigar... | Use a Expressão do Filtro... | A expressão se Aplica ao Log (Cliente, Servidor, Rede, Todos) |
| --- | --- | --- |
| Atrasos inesperados na entrega de mensagens em uma fila |AzureStorageClientDotNetV4.Description contém "Repetindo a operação que falhou". |Cliente |
| Aumento de HTTP no PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Rede |
| Aumento em PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Rede |
| Aumento em PercentTimeoutError (todos) |*StatusCode   == 500 |Todos |
| Aumento em PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Cliente |
| Mensagens HTTP 403 (Proibido) |HTTP.Response.StatusCode   == 403 |Rede |
| Mensagens HTTP 404 (Não encontrado) |HTTP.Response.StatusCode   == 404 |Rede |
| 404 (todos) |*StatusCode   == 404 |Todos |
| Problema de autorização de SAS (Assinatura de Acesso Compartilhado) |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Rede |
| Mensagens HTTP 409 (Conflito) |HTTP.Response.StatusCode   == 409 |Rede |
| 409 (todos) |*StatusCode   == 409 |Todos |
| Baixo PercentSuccess ou as entradas de log analíticas têm operações com status de transação de ClientOtherErrors |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Servidor |
| Aviso Nagle |((AzureStorageLog.EndToEndLatencyMS   - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS *   1.5)) and (AzureStorageLog.RequestPacketSize <1460) and (AzureStorageLog.EndToEndLatencyMS -   AzureStorageLog.ServerLatencyMS >= 200) |Servidor |
| Intervalo de horas nos logs de Servidor e Rede |#Timestamp   >= 2014-10-20T16:36:38 e #Timestamp <= 2014-10-20T16:36:39 |Servidor, Rede |
| Intervalo de horas nos logs de Servidor |AzureStorageLog.Timestamp   >= 2014-10-20T16:36:38 e AzureStorageLog.Timestamp <=   2014-10-20T16:36:39 |Servidor |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre cenários de ponta a ponta para solução de problemas no Armazenamento do Azure, consulte estes recursos:

* [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)
* [Análise de Armazenamento](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Transferir dados com o Utilitário da Linha de Comando AzCopy](storage-use-azcopy.md)
* [Guia Operacional do Analisador de Mensagem da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
