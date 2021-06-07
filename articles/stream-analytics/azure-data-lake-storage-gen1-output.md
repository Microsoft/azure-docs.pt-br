---
title: Saída Azure Data Lake Storage Gen 1 de Azure Stream Analytics
description: Este artigo descreve Azure Data Lake Storage Gen 1 como uma opção de saída para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016534"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Saída Azure Data Lake Storage Gen 1 de Azure Stream Analytics

O Stream Analytics dá suporte a saídas de [Azure data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) . O Azure Data Lake Storage é um repositório de hiperescala em toda a empresa para cargas de trabalho de análise de Big Data. Você pode usar o Data Lake Storage para armazenar dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. O Stream Analytics deve estar autorizado a acessar o Data Lake Storage.

Azure Data Lake Storage saída do Stream Analytics não está disponível nas regiões do Azure China 21Vianet e do Azure Alemanha (T-Systems International).

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista nomes de propriedade e suas descrições para configurar a saída do Data Lake Storage Gen 1.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para o Data Lake Store. |
| Subscription | A assinatura que contém sua conta do Azure Data Lake Storage. |
| Nome da conta | O nome da conta do Data Lake Store para a qual você está enviando sua saída. Você verá uma lista suspensa de contas do Data Lake Store que estão disponíveis na sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo que é usado para gravar seus arquivos na conta do Data Lake Store. Você pode especificar uma ou mais instâncias de variáveis de {data} e {hora}:<br /><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br />O registro de data e hora da estrutura de pastas criada segue o UTC (Tempo Universal Coordenado) e não o horário local.<br /><br />Se o padrão de caminho do arquivo não contiver uma barra (/) à direita, o último padrão no caminho do arquivo é tratado como um prefixo de nome de arquivo. <br /><br />Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinicialização externa ou interna de um trabalho</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.|
| Codificação | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento.|
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido.  A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar o JSON separado por linha, porque ele não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado.|
| Modo de autenticação | Você pode autorizar o acesso à sua conta de Data Lake Storage usando a [identidade gerenciada](stream-analytics-managed-identities-adls.md) (versão prévia) ou o token de usuário. Depois de conceder acesso, você pode revogar o acesso alterando a senha da conta do usuário, excluindo a saída do Data Lake Storage para esse trabalho ou excluindo o trabalho do Stream Analytics. |

## <a name="partitioning"></a>Particionamento

Para a chave de partição, use tokens {Date} e {time} no padrão de prefixo de caminho. Escolha um formato de data, como aaaa/MM/DD, DD/MM/aaaa ou MM-DD-AAAA. Use HH para o formato de hora. O número de gravadores de saída segue o particionamento de entrada para [consultas totalmente paralelizáveiss](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [limites de data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Para otimizar o tamanho do lote, use até 4 MB por operação de gravação.

## <a name="next-steps"></a>Próximas etapas

* [Autenticar Stream Analytics para Azure Data Lake Storage Gen1 usando identidades gerenciadas (versão prévia)](stream-analytics-managed-identities-adls.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)