---
title: Power BI saída de Azure Stream Analytics
description: Este artigo descreve como gerar dados de saída de Azure Stream Analytics para Power BI.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a94389a075fd62d80345a21e32f1bc977dfdee87
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020053"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI saída de Azure Stream Analytics

Você pode usar o [Power BI](https://powerbi.microsoft.com/) como saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Você pode usar essa funcionalidade para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

A saída do Power BI do Stream Analytics atualmente não está disponível nas regiões Azure China 21Vianet e Azure Alemanha (T-Systems International).

## <a name="output-configuration"></a>Configuração de saída

A tabela a seguir lista nomes de propriedade e suas descrições para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Fornece é um nome amigável que é usado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Agrupar o workspace |Para permitir o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher **Meu Workspace** caso não queira gravar em um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Fornece um conjunto de dados que você deseja que a saída do Power BI use. |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |
| Autorizar conexão | Você precisa autorizar com o Power BI para definir as configurações de saída. Depois de conceder esse acesso de saída ao seu painel do Power BI, você pode revogar o acesso alterando a senha da conta de usuário, excluindo a saída do trabalho ou excluindo o trabalho do Stream Analytics. | 

Para obter um passo a passo de configuração de uma saída e de um painel do Power BI, consulte o tutorial [Azure Stream Analytics e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Se a consulta do trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados. Se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos.
>

### <a name="create-a-schema"></a>Criar um esquema

O Stream Analytics do Azure cria um conjunto de dados do Power BI e um esquema de tabela para o usuário caso eles ainda não existam. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, só pode existir uma tabela em um conjunto de dados. 

O Power BI usa a política de retenção de PEPS (primeiro a entrar, primeiro a sair). Os dados serão coletados em uma tabela até que ela atinja 200.000 linhas.

> [!NOTE]
> Não recomendamos o uso de várias saídas para gravar no mesmo conjunto de resultados porque isso pode causar vários problemas. Cada saída tenta criar o conjunto de Power BI DataSet independentemente, o que pode resultar em vários conjuntos de valores com o mesmo nome. Além disso, se as saídas não tiverem esquemas consistentes, o conjunto de resultados alterará o esquema em cada gravação, o que leva a muitas solicitações de alteração de esquema. Mesmo que esses problemas sejam evitados, várias saídas serão menos modeladas do que uma única saída mesclada.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converter um tipo de dados do Stream Analytics para o Power BI

O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no runtime se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões de tipo de dados dos [Tipos de dados do Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics) para [Tipos de EDM (Modelo de Dados de Entidade)](/dotnet/framework/data/adonet/entity-data-model) do Power BI caso não existam um conjunto de dados e uma tabela do Power BI.

Do Stream Analytics | Para o Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
DATETIME | Datetime
FLOAT | Double
Matriz de registro | Tipo de cadeia de caracteres, valor constante "IRecord" ou "IArray"

### <a name="update-the-schema"></a>Atualizar o esquema

o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que não se encaixam no esquema original.

Evite a consulta `SELECT *` para impedir que o esquema dinâmico atualize as linhas. Além das possíveis implicações de desempenho, ela pode resultar na incerteza do tempo necessário para obter os resultados. Selecione os campos exatos que precisam ser mostrados no painel do Power BI. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.

Anterior/atual | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres | String 
Datetime | String | String |  Datetime | String

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para o tamanho do lote de saída, confira [Power bi limites da API REST](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Próximas etapas

* [Usar identidade gerenciada para autenticar seu trabalho de Azure Stream Analytics para Power BI (versão prévia)](powerbi-output-managed-identity.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)