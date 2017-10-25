---
title: "Plataformas de análise: comparação do Apache Storm com o Stream Analytics | Microsoft Docs"
description: "Obtenha diretrizes ao escolher uma plataforma de análise de nuvem, usando a comparação entre o Apache Storm e o Stream Analytics. Compreender os recursos e diferenças."
keywords: "plataforma de análise, plataformas de análise, plataforma de análise de nuvem, comparação com o storm"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: 97044cb5d7b0b3fcb3b85328df618a265bc59b61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Escolher uma plataforma de análise de streaming: comparação do Apache Storm com o Stream Analytics do Azure
O Azure fornece várias soluções para analisar dados de streaming: [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) e [Apache Storm no Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Ambas as plataformas de análise oferecem os benefícios de uma solução de PaaS. Mas as plataformas têm algumas diferenças significativas em suas funcionalidades e também em como configurá-las e gerenciá-las. 

Este artigo fornece uma comparação lado a lado dos recursos para ajudá-lo a escolher entre o Apache Storm e o Stream Analytics do Azure como uma plataforma de análise na nuvem. 

## <a name="general-features"></a>Recursos gerais

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Software livre?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não. O Stream Analytics do Azure é uma oferta de propriedade da Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim. O Apache Storm é uma tecnologia licenciada pela Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte da Microsoft?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nenhuma. Azure Stream Analytics é um serviço do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nenhuma. Apache tempestade é um serviço do Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidade de nível superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os usuários implantam e monitoram trabalhos de streaming.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
O usuários implantam e monitoram um cluster inteiro, que pode hospedar vários trabalhos do Storm, bem como outras cargas de trabalho (inclusive lote).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preço</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O preço é calculado pelo volume de dados processados e o número de unidades de streaming necessárias por hora de execução do trabalho. 
                </p>
                    <p>Para obter mais informações, consulte <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Preços do Stream Analytics</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A unidade de compra é baseada em cluster e é cobrada com base no tempo de execução do cluster, independentemente dos trabalhos implantados.
                </p>
                <p>
Para obter mais informações, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Preços do HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Criação

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Recursos: SQL DSL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim. O Stream Analytics fornece uma linguagem SQL para criar transformações.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não. Os usuários devem escrever código em Java ou C# ou então usar APIs do Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Recursos: operadores temporais?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Agregações em janelas e junções temporais têm suporte por padrão.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporais devem ser implementados pelo usuário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiência de desenvolvimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os usuários podem criar, depurar e monitorar trabalhos através do Portal do Azure, usando dados de exemplo derivados de um transmissão ao vivo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Usuários que usam o .NET podem desenvolver, depurar e monitorar por meio do Visual Studio. Os usuários usando o Java ou outras linguagens podem usar o IDE de sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte para depuração</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os logs de operações e de status de trabalho básicos estão disponíveis para ajudar na depuração. O Stream Analytics atualmente não permite aos usuários especificar o conteúdo ou a quantidade de conteúdo incluída nos logs (ou seja, modo detalhado).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os logs detalhados estão disponíveis. Os usuários podem acessar os logs no Visual Studio ou fazendo logon no cluster e acessando os logs diretamente.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilidade usando código personalizado?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Suporte parcial com UDFs de JavaScript. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">Integração do JavaScript UDF</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim. Os usuários podem gravar código personalizado em C#, Java ou qualquer outra linguagem com suporte no Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Fontes de dados (entradas) e saídas ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Fontes de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Hubs de Eventos do Azure e Armazenamento de Blobs do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os Conectores estão disponíveis para os Hubs de Eventos do Azure, o Barramento de Serviço do Microsoft Azure, o Kafka e muito mais. Os usuários podem criar conectores adicionais usando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Os usuários podem implementar qualquer formato usando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Saídas</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Um trabalho de streaming pode ter várias saídas. As saídas com suporte são Hubs de Eventos do Azure, Armazenamento de Blobs do Azure, Armazenamento de Tabelas do Azure, BD SQL do Azure e Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
O Storm dá suporte a várias saídas em uma topologia e cada saída pode ter uma lógica personalizada para processamento downstream. O Storm inclui conectores para o Power BI, Hubs de Eventos do Azure, Armazenamento de Blobs do Azure, Azure Cosmos DB, SQL e HBase. Os usuários podem criar conectores adicionais usando código personalizado.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificação de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os dados devem ser formatados usando UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Os usuários podem implementar qualquer formato de codificação de dados usando código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Gerenciamento e operações ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implantação de trabalho</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Portal do Azure, PowerShell e APIs REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portal do Azure PowerShell, o Visual Studio e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoramento (estatísticas, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O monitoramento é implementado usando o Portal do Azure e APIs REST. Usuários também podem configurar alertas do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
O monitoramento é feito usando a interface do usuário do Storm e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidade</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
A escalabilidade é determinada pelo número de unidades de Streaming (SUs) para cada trabalho. Cada unidade de Streaming processa até 1 MB/segundo, com um máximo de 50 unidades. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">Escalar para aumentar a produtividade</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A escalabilidade é determinada pelo número de nós no cluster Storm no HDInsight. O limite superior no número de nós é definido pela cota do Azure do usuário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de processamento de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os usuários podem aumentar o processamento de dados ou otimizar os custos aumentando ou diminuindo o número de unidades de Streaming, com um limite superior de 1 GB/segundo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os usuários podem escalar ou reduzir verticalmente o tamanho do cluster.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Parar/Retomar</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interromper e retomar no último lugar interrompido.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Interromper e retomar no último local parado com base em uma marca-d'água.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Atualização de serviço e estrutura</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Aplicação de patch automáticas sem tempo de inatividade.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aplicação de patch automáticas sem tempo de inatividade.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidade de negócios por meio de um serviço altamente disponível com SLAs garantidos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA de 99,9% de tempo de atividade</li>
                <li>Recuperação automática de falhas</li>
                <li>Recuperação interna de operadores temporais com monitoração de estado</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA de 99,9% de tempo de atividade de cluster do Storm. 
                </p>
                <p>
O Apache Storm é uma plataforma de streaming tolerante a falhas. No entanto, é responsabilidade do usuário garantir que os trabalhos de streaming sejam executados sem interrupção.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Recursos avançados ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Chegada tardia e manipulação de eventos fora de ordem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Políticas internas configuráveis podem reordenar eventos, soltá-los ou ajustar a hora do evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Os usuários devem implementar a lógica para manipular esse cenário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dados de referência</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados de referência estão disponíveis no Armazenamento de Blobs do Azure com um máximo de 100 MB de cache na memória. Dados de referência são atualizados pelo serviço.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sem limites de tamanho dos dados. Os conectores estão disponíveis para HBase, Azure Cosmos DB, SQL Server e Azure. Os usuários podem criar conectores adicionais usando código personalizado. Os dados de referência devem ser atualizados usando código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integração ao Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Modelos de Machine Learning do Azure publicados podem ser configurados como funções durante a criação do trabalho. Para saber mais, confira <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">Escalar para funções de Machine Learning</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponível por meio de bolts do Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
