---
title: "Usar tabelas de pesquisa e dados de referência no Stream Analytics | Microsoft Docs"
description: "Usar dados de referência em uma consulta do Stream Analytics"
keywords: "tabela de pesquisa, dados de referência"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 06103be5-553a-4da1-8a8d-3be9ca2aff54
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 438ec565f3c6e06ab7ec92cf1bbfbdde88f99b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Usando dados de referência e tabelas de pesquisa em um fluxo de entrada do Stream Analytics
Dados de referência (também conhecidos como tabela de pesquisa) são um conjunto finito de dados estáticos ou com alteração lenta por natureza, usados para executar uma pesquisa ou para correlacionar com seu fluxo de dados. Para usar dados de referência no seu trabalho do Stream Analytics do Azure geralmente é preciso usar uma [União de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) em sua Consulta. O Stream Analytics usa o armazenamento de Blobs do Azure como a camada de armazenamento para dados de referência e com os dados de referência do Azure Data Factory podem ser transformados e/ou copiados para o armazenamento de Blobs do Azure, para uso como dados de referência de [qualquer número armazenamentos de dados de nuvem e locais](../data-factory/copy-activity-overview.md). Dados de referência são modelados como uma sequência de blobs (definidos na configuração de entrada) em ordem crescente segundo a data/hora especificada no nome do blob. Ele **somente** dá suporte à adição ao final da sequência usando um valor de data/hora **maior** que aquele especificado pelo último blob na sequência.

O Stream Analytics tem uma **limite de 100 MB por blob** mas trabalhos podem processar vários blobs de referência usando o **padrão de caminho** propriedade.


## <a name="configuring-reference-data"></a>Configurando os dados de referência
Para configurar os dados de referência, você primeiro precisa criar uma entrada que seja do tipo **Dados de Referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar os entrada de dados de referência com sua descrição:


<table>
<tbody>
<tr>
<td>Nome da Propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde estão localizados os blobs. Se estiver na mesma assinatura que o trabalho do Stream Analytics, você pode selecioná-lo na lista suspensa.</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento. É preenchida automaticamente se a conta de armazenamento estiver na mesma assinatura que o trabalho do Stream Analytics.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.</td>
</tr>
<tr>
<td>Padrão de caminho</td>
<td>O caminho usado para localizar seus blobs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir:<BR>{data}, {hora}<BR>Exemplo 1: products/{data}/{hora}/product-list.csv<BR>Exemplo 2: products/{data}/product-list.csv
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se você tiver usado {data} no padrão de caminho que você especificou, você pode selecionar o formato de data no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: AAAA/MM/DD, MM/DD/AAAA etc.</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se você tiver usado {hora} no padrão de caminho que você especificou, você pode selecionar o formato de hora no qual os blobs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: HH, HH/mm ou HH-mm</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Para verificar se as consultas funcionam da maneira esperada, o Stream Analytics precisa saber qual formato de serialização você está usando para os fluxos de dados de entrada. Para dados de referência, os formatos com suporte são CSV e JSON.</td>
</tr>
<tr>
<td>Codificação</td>
<td>UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Gerar dados de referência em uma agenda
Se os seus dados de referência são um conjunto de dados de alteração lenta, o suporte para atualização de dados de referência é habilitado especificando, na configuração de entrada, um padrão de caminho usando os tokens de substituição {date} e {time}. O Stream Analytics separa as definições de dados de referência baseadas nesse padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data **"AAAA-MM-DD"** e um formato de hora **"HH: mm"** informa o Stream Analytics para acompanhar o blob `sample/2015-04-16/17-30/products.csv` atualizado às 17:30, em 16 de abril de 2015 fuso horário UTC.

> [!NOTE]
> Atualmente os trabalhos do Stream Analytics procuram pela atualização de blob somente quando a hora do computador avança até a hora codificada no nome do blob. Por exemplo o trabalho irá procurar `sample/2015-04-16/17-30/products.csv` assim que possível, mas não antes das 17:30 UTC do dia 16 de abril de 2015. Ele *nunca* procurará um blob com uma hora codificada anterior ao último que foi descoberto.
> 
> Por exemplo Depois que o trabalho localiza o blob `sample/2015-04-16/17-30/products.csv`, ele ignora todos os arquivos com uma data codificada anterior às 17:30 de 16 de abril de 2015. Portanto, se um blob `sample/2015-04-16/17-25/products.csv` atrasado for criado no mesmo contêiner, o trabalho não o usará.
> 
> Da mesma forma, se `sample/2015-04-16/17-30/products.csv` só é produzido às 22:03 do dia 16 de abril de 2015, mas nenhum blob com uma data anterior está presente no contêiner, o trabalho usa esse arquivo começando às 22:03 do dia 16 de abril de 2015 e usando os dados de referência anteriores.
> 
> Uma exceção a isso é iniciada quando o trabalho precisa reprocessar dados de volta no tempo ou quando o trabalho é o primeiro a iniciar. Na hora de início, o trabalho está procurando o blob mais recente produzido antes da hora especificada de início do trabalho. Isso é feito para garantir que haja um conjunto de dados de referência **não vazio** quando o trabalho é iniciado. Se um não for encontrado, o trabalho exibirá o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pode ser utilizada para orquestrar a tarefa de criar os blobs atualizados exigidos pelo Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. O Data Factory dá suporte [à conexão de um grande número de armazenamentos de dados local baseados em nuvem](../data-factory/copy-activity-overview.md) e mover dados facilmente em um agendamento regular que você especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline de Data Factory para gerar dados de referência para o Stream Analytics que é atualizado em um cronograma predefinido, confira este [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Dicas sobre como atualizar seus dados de referência
1. Substituir blobs de dados de referência não fará com que o Stream Analytics recarregue o blob e, em alguns casos, isso pode fazer com que o trabalho falhe. A maneira recomendada de alterar os dados de referência é adicionar um novo blob usando o mesmo padrão de caminho e contêiner definidos na entrada de trabalho e usar um valor de data/hora **maior** que o especificado pelo último blob na sequência.
2. Blobs de dados de referência **não** são ordenados pela hora da "Última modificação" do blob, mas apenas pela hora e data especificadas no nome do blob usando as substituições {date} e {time}.
3. Em algumas ocasiões um trabalho deve voltar no tempo, portanto, blobs de dados de referência não devem ser alterados ou excluídos.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
Você foi apresentado ao Stream Analytics, um serviço gerenciado para análise de streaming em dados da Internet das coisas. Para saber mais sobre esse serviço, consulte:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
