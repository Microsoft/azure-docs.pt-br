---
title: "Notas de versão do Stream Analytics | Microsoft Docs"
description: "Notas de versão do Stream Analytics"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: samacha
ms.openlocfilehash: 3251cd47bb917912d63330345dbf392e724448ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-release-notes"></a>Notas de versão do Stream Analytics

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Observações para atualização de 14/06/2017 das Ferramentas do Azure Stream Analytics para Visual Studio
Esta atualização é para nossas Ferramentas do Visual Studio. Esta versão contém os novos recursos a seguir:

| Title | Descrição |
| --- | --- |
| Suporte ao editor de scripts Java |Você pode aproveitar a experiência de editor de scripts Java nativo depois de criar suas funções de script Java.|
| Exibir mensagem de erro de tempo de execução do trabalho | Se houver erros de tempo de execução durante a execução do trabalho, você poderá exibi-los na guia Erros ajustando a janela de tempo da exibição. Por padrão, ela mostra as mensagens de erro dos últimos 30 minutos. |
| Suporte a CSV e Avro para entrada de teste local | Além de JSON, agora você pode usar formato de arquivo CSV e Avro para a entrada de teste local.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Notas da atualização de 03/05/2017 do Stream Analytics
Esta atualização é para nossa versão de documentação da solução de problemas.

O [guia de solução de problemas](stream-analytics-troubleshooting-guide.md) e outros documentos foram lançados. Examine, pois o comentário é bem-vindo.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Observações para atualização de 24/04/2017 das Ferramentas do Azure Stream Analytics para Visual Studio
Esta atualização é para nossas Ferramentas do Visual Studio. Esta versão contém os novos recursos a seguir:

| Title | Descrição |
| --- | --- |
| Exibir o resultado do teste local no Visual Studio | Para exibir o resultado do teste local, apenas pressione ENTER na janela do console de saída ou feche-o. O resultado é mostrado em uma janela no Visual Studio, no formato de tabela. |
| Resultado local de saída no formato JSON | Quando você executa um teste local, o resultado de saída é gerado como ambos os formatos de arquivo JSON e CSV. |
| Visualizar dados de entradas/saídas de armazenamento de Blobs/de tabela | Clicando duas vezes na entrada/saída de um armazenamento de Blobs ou de tabela na exibição de trabalho, você pode visualizar facilmente os dados dentro do Visual Studio. |
| Exibir mensagem de erro para entradas/saídas | Se houver alguns erros de tempo de execução relacionados à entrada ou à saída do trabalho, eles serão mostrados no diagrama de trabalho sobre o qual você pode passar o cursor do mouse para ver a mensagem de erro detalhada.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Notas de versão de 01/02/2017 do Stream Analytics
Esta versão contém a atualização a seguir:

| Title | Descrição |
| --- | --- |
| Introdução a UDF (Funções definidas pelo usuário) do JavaScript |[As Funções definidas pelo usuário de Java](stream-analytics-javascript-user-defined-functions.md) agora estão disponíveis para proporcionar mais flexibilidade na criação de consultas. |
| Apresentação das ferramentas para Visual Studio e Stream Analytics |[As Ferramentas para Visual Studio](stream-analytics-tools-for-visual-studio.md) agora estão disponíveis para depuração e com mais utilitários. |
| Apresentação do registro em log de diagnóstico |[O Registro em log de diagnóstico](stream-analytics-job-diagnostic-logs.md) agora está disponível para mais opções de solução de problemas. |
| Introdução às Funções Geoespaciais |As [Funções Geoespaciais](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) agora estão disponíveis para o público geral. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notas de versão de 15/04/2016 do Stream Analytics
Esta versão contém a atualização a seguir:

| Title | Descrição |
| --- | --- |
| Disponibilidade geral para saídas do Power BI |[Saídas do Power BI](stream-analytics-power-bi-dashboard.md) agora estão em disponibilidade geral. A expiração de autorização de 90 dias para o Power BI foi removida. Para obter mais informações sobre os cenários em que a autorização precisa ser renovada, consulte a seção [Renovar autorização](stream-analytics-power-bi-dashboard.md#renew-authorization) do painel Criar um Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notas de versão de 03/03/2016 do Stream Analytics
Esta versão contém a atualização a seguir:

| Title | Descrição |
| --- | --- |
| Novos itens de linguagem de consulta de Stream Analytics |SAQL agora tem [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN Page"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN Page") e [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN Page"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notas de versão de 10/12/2015 do Stream Analytics
Esta versão contém a atualização a seguir:

| Title | Descrição |
| --- | --- |
| Atualização de versão da API REST |A versão da API REST foi atualizada para 2015-10-01. Detalhes podem ser encontrados no MSDN em [Referência de API REST do gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [Integração do Machine Learning ao Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Integração de Azure Machine Learning |Com essa versão, é fornecido suporte para funções definidas pelo usuário do Azure Machine Learning. Veja o [tutorial](stream-analytics-machine-learning-integration-tutorial.md) para obter mais informações, bem como o [comunicado geral do blog](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notas de versão de 12/11/2015 do Stream Analytics
Esta versão contém a atualização a seguir:

| Title | Descrição |
| --- | --- |
| Novo comportamento de SELECT |SELECT no Stream Analytics foi estendido para permitir * como um acessador de propriedade de um registro aninhado. Para saber mais, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de dados complexos"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notas de versão de 22/10/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Recursos adicionais de linguagem de consulta |O Stream Analytics expandiu a linguagem de consulta, incluindo os seguintes recursos: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) e [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Limitações de agregação removidas |Esta versão remove a limitação de 15 agregações em uma consulta. Agora não há um limite para o número de agregações por consulta. |
| Acréscimo do recurso System.Timestamp a GROUP BY |Agora, a função [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) permite window_type ou [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Acréscimo de OFFSET para janelas com Cascata e Salto |Por padrão, janelas [Em Cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [De Salto](https://msdn.microsoft.com/library/azure/dn835041.aspx) são alinhadas com tempo zero (01/01/0001 12:00:00 AM UTC). O novo parâmetro (opcional) “offsetsize” permite especificar um deslocamento (ou alinhamento) personalizado. |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notas de versão de 29/09/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Visualização Pública do Azure IoT Suite |O Stream Analytics está incluído na Visualização Pública do Azure IoT Suite. |
| Integração ao portal do Azure |Além da presença contínua no Portal do Azure, o Stream Analytics agora está integrado ao [Portal do Azure](https://azure.microsoft.com/overview/preview-portal/). Lembre-se de que a funcionalidade do Stream Analytics no portal de Visualização é atualmente um subconjunto da funcionalidade oferecida no Portal do Azure, sem suporte para o teste de consulta no navegador, configuração de saída do Power BI e navegação ou criação de novos recursos de entrada e saída nas assinaturas às quais você tem acesso. |
| Suporte para a saída do Cosmos DB |Os trabalhos do Stream Analytics agora podem ser importados para o [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Suporte para a entrada do Hub IoT |Os trabalhos do Stream Analytics agora podem ingerir dados dos Hubs IoT. |
| TIMESTAMP BY para eventos heterogêneos |Quando uma transmissão de dados única contém vários tipos de evento com carimbos de data/hora em diferentes campos, agora você pode usar o [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) com expressões para especificar diferentes campos de carimbo de data/hora para cada caso. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notas de versão de 10/09/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Suporte para grupos do PowerBI |Para habilitar o compartilhamento de dados com outros usuários do Power BI, os trabalhos do Stream Analytics agora podem ser gravados nos [grupos do PowerBI](stream-analytics-define-outputs.md#power-bi) dentro de sua conta do Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notas de versão de 20/08/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Função LAST adicionada |A função [LAST](http://msdn.microsoft.com/library/mt421186.aspx) agora está disponível nos trabalhos do Stream Analytics, permitindo que você recupere o evento mais recente em um fluxo de eventos, em um determinado período de tempo. |
| Novas funções de matriz |As funções de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) agora estão disponíveis. |
| Novas funções de registro |As funções de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) agora estão disponíveis. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notas de versão de 30/07/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Id da Org Power BI dissociada da Id do Azure |Esse recurso habilita a [saída do Power BI](stream-analytics-power-bi-dashboard.md) para trabalhos de ASA em qualquer tipo de conta do Azure (Live ID ou Org ID). Além disso, você pode ter uma Id da Org para sua conta do Azure e usar uma diferente para autorizar a saída do Power BI. |
| Suporte para saída de filas do barramento de serviço |[Filas do Barramento de Serviço](stream-analytics-define-outputs.md#service-bus-queues) agora estão disponíveis nos trabalhos do Stream Analytics. |
| Suporte para saída de filas de tópicos do Barramento de Serviço |[Tópicos do Barramento de Serviço](stream-analytics-define-outputs.md#service-bus-topics) agora estão disponíveis nos trabalhos do Stream Analytics. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notas de versão de 09/07/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Particionamento de saída de blob personalizado |Saídas de armazenamento de blob agora permitem uma opção para especificar a frequência em que os blobs de saída são gravados e a estrutura e o formato da pasta do caminho de saída dados. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notas de versão de 03/05/2015 do Stream Analytics
Esta versão contém as atualizações a seguir:

| Title | Descrição |
| --- | --- |
| Maior valor máximo aumentado da janela de tolerância inoperante |O tamanho máximo da janela de tolerância inoperante agora é 59:59 (MM:SS) |
| Formato de saída JSON: linha separada ou matriz |Agora há uma opção para gerar a armazenamento de blob ou hub de evento de saída como uma matriz de objetos JSON ou separando os objetos JSON com uma nova linha. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notas de versão de 16/04/2015 do Stream Analytics
| Title | Descrição |
| --- | --- |
| Atraso na configuração da conta de Armazenamento do Azure |Ao criar um trabalho do Stream Analytics em uma região pela primeira vez, você precisará criar uma nova conta de Armazenamento ou especifique uma conta existente para monitorar trabalhos do Stream Analytics nessa região. Devido à latência na configuração de monitoramento, criar outro trabalho do Stream Analytics na mesma região dentro de 30 minutos solicita a especificação de uma segunda conta de armazenamento em vez de mostrar aquela recentemente configurada no menu suspenso de Conta de Monitoramento de Armazenamento. Para evitar a criação de uma conta de Armazenamento desnecessária, aguarde 30 minutos após a criação de um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região. |
| Atualização de trabalho |Atualmente, o Stream Analytics não dá suporte a edições dinâmicas para a definição ou a configuração de um trabalho em execução. Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro interromper o trabalho. |
| Tipos de dados inferidos da fonte de entrada |Se uma instrução CREATE TABLE não for usada, o tipo de entrada será derivado do formato de entrada, por exemplo, todos os campos do arquivo CSV são cadeias de caracteres. Os campos precisam ser explicitamente convertidos no tipo certo usando a função CAST para evitar erros de incompatibilidade de tipo. |
| Os campos ausentes são enviados como valores nulos |Fazer referência a um campo que não esteja presente na fonte de entrada resulta em valores nulos no evento de saída. |
| As instruções WITH devem preceder as instruções SELECT |Na consulta, as instruções SELECT devem seguir subconsultas definidas em instruções WITH. |
| Problema de memória insuficiente |Trabalhos do Stream Analytics com uma grande tolerância para eventos de problemas verificação e/ou consultas complexas com uma grande quantidade de estado podem causar insuficiência de memória para o trabalho, resultando no reinício do trabalho. As operações de iniciar e parar ficarão visíveis nos logs de operação do trabalho. Para evitar esse comportamento, dimensione a consulta por várias partições. Em uma versão futura, essa limitação será tratada por meio da redução do desempenho nos trabalhos afetados em vez de reiniciá-los. |
| Grandes entradas de blob sem carimbo de data/hora de carga podem causar o problema de memória insuficiente |Consumir grandes arquivos de um armazenamento Blob pode fazer com que trabalhos do Stream Analytics falhem caso um campo de carimbo de data/hora não seja especificado via TIMESTAMP BY. Para evitar esse problema, não permita que os blobs tenham mais de 10 MB de tamanho. |
| Limitação de volume de eventos de Banco de Dados SQL |Quando o Banco de Dados SQL for usado como um destino de saída, volumes muito grandes de dados de saída poderão fazer com que o trabalho do Stream Analytics atinja o tempo limite. Para resolver esse problema, reduza o volume de saída usando agregações ou operadores de filtro, ou escolha o armazenamento Blob ou Eventos de Hubs do Azure como um destino de saída. |
| Os conjuntos de dados do PowerBI só podem conter uma tabela |O PowerBI não oferece suporte a mais de uma tabela em um determinado conjunto de dados. |

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
