---
title: Armazenamento de BLOBs e Azure Data Lake saída de Gen2 de Azure Stream Analytics
description: Este artigo descreve o armazenamento de BLOBs e Azure Data Lake Gen 2 como saída para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935139"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Armazenamento de BLOBs e Azure Data Lake saída de Gen2 de Azure Stream Analytics

O Data Lake Storage Gen2 torna o armazenamento do Azure a fundação para a criação de data lakes empresariais no Azure. Projetado desde o início para atender a vários petabytes de informações enquanto mantém centenas de gigabits de taxa de transferência, o Data Lake Storage Gen2 permite que você gerencie de uma maneira fácil grandes quantidades de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um namespace hierárquico para armazenamento de Blobs.

O Armazenamento de blobs do Azure oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o Armazenamento de blobs e seu uso, consulte [Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de um blob ou de uma saída do ADLS Gen2.

| Nome da propriedade       | Descrição                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento para a qual você está enviando a saída.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contêiner de armazenamento   | Um agrupamento lógico para blobs armazenados no serviço Blob do Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão do caminho do arquivo que é usado para gravar seus blobs no contêiner especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das duas variáveis de hora e data para especificar a frequência com a qual os blobs são gravados: <br /> {data}, {hora} <br /><br />Você pode usar um particionamento de blob para especificar um nome de {field} personalizado dos dados do seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Nomes de campo não diferenciam maiúsculas de minúsculas. Por exemplo, o serviço não consegue diferenciar entre "ID" e "id" da coluna.</li><li>Campos aninhados não são permitidos. Em vez disso, use um alias na consulta de trabalho para “nivelar” o campo.</li><li>As expressões não podem ser usadas como um nome de campo.</li></ul> <br />Esse recurso permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Formatos de data e hora personalizados devem ser especificados um de cada vez, entre a \<specifier> palavra-chave {DateTime:}. As entradas permitidas para \<specifier> são AAAA, mm, m, DD, d, hh, H, mm, m, SS ou s. A \<specifier> palavra-chave {DateTime:} pode ser usada várias vezes no caminho para formar configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} em que a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O registro de data e hora da estrutura de pastas criada segue o UTC (Tempo Universal Coordenado) e não o horário local. [System. Timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) é o tempo usado para todo o particionamento baseado em tempo.<br /><br />A nomenclatura de arquivo usa a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br /> Aqui, GUID representa o identificador exclusivo atribuído a um gravador interno que é criado para gravar em um arquivo de BLOB. O número representa o índice do bloco de BLOB. <br /><br /> Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esse recurso, consulte [Particionamento de saída de blobs personalizados do Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV, Avro e Parquet. |
|Mínimo de linhas |O número mínimo de linhas por lote. Para o Parquet, cada lote criará um novo arquivo. O valor padrão atual é de 2.000 linhas, e o máximo permitido é de 10.000 linhas.|
|Tempo máximo |O tempo máximo de espera por lote. Após esse período, o lote será gravado na saída mesmo que o requisito de linhas mínimas não seja atendido. O valor padrão atual é de 1 minuto, e o máximo permitido é de 2 horas. Se a saída de blobs tiver a frequência de padrão do caminho, o tempo de espera não poderá ser maior que o intervalo de tempo da partição.|
| Codificação    | Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador   | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável somente à serialização de JSON. Uma **Linha separada** especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. Se você selecionar **Linha separada**, o JSON será lido um objeto por vez. O conteúdo inteiro por si só não seria um JSON válido. A **matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar o JSON separado por linha, porque ele não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado. |

## <a name="blob-output-files"></a>Arquivos de saída de BLOB

Ao usar o Armazenamento de blobs como saída, um novo arquivo será criado no blob nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). Você pode alcançar o número máximo permitido de blocos sem atingir o tamanho máximo permitido do blob. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída exigir esquema fixo (CSV, Avro, parquet).
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.
* Se a consulta for totalmente particionada, e um novo arquivo for criado para cada partição de saída.
* Se o usuário excluir um arquivo ou um contêiner da conta de armazenamento.
* Se a saída for particionada por tempo usando o padrão de prefixo do caminho, e um novo blob for usado quando a consulta se mover para a próxima hora.
* Se a saída for particionada por um campo personalizado, e um novo blob for criado por chave de partição caso não exista.
* Se a saída for particionada por um campo personalizado, em que a cardinalidade da chave de partição exceda 8.000, e um novo blob for criado por chave de partição.

## <a name="partitioning"></a>Particionamento

Para chave de partição, use tokens {Date} e {time} de seus campos de evento no padrão de caminho. Escolha o formato de data, como AAAA/MM/DD, DD/MM/AAAA ou MM-DD-AAAA. HH é usado para o formato de hora. A saída de blob pode ser particionada por um único atributo de evento personalizado {FieldName} ou {DateTime: \<specifier> }. O número de gravadores de saída segue o particionamento de entrada para [consultas totalmente paralelizáveiss](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O tamanho máximo do bloco de blob é de 4 MB e a contagem máxima de Bock de blob é 50.000. |

## <a name="next-steps"></a>Próximas etapas

* [Usar identidade gerenciada (versão prévia) para autenticar seu trabalho de Azure Stream Analytics para o armazenamento de BLOBs do Azure](blob-output-managed-identity.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
