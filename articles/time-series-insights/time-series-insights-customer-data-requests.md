---
title: Recursos de solicitação de dados do cliente no Azure Time Series Insights | Microsoft Docs
description: Resumo de recursos de solicitação de dados do cliente do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 30f6b1fd953f89170a18d56bf0353c643853074e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880701"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente

O Azure Time Series Insights é um serviço de nuvem gerenciado com componentes de armazenamento, análise e visualização que facilitam a ingestão, o armazenamento, a exploração e a análise de bilhões de eventos simultaneamente.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Para exibir, exportar e excluir dados pessoais que podem estar sujeitos a uma solicitação do titular dos dados, um administrador de locatários do Azure Time Series Insights pode usar o portal do Azure ou as APIs REST. O uso do portal do Azure para atender às solicitações do titular dos dados fornece um método menos complexo para executar as operações preferenciais pela maioria dos usuários.

## <a name="identifying-customer-data"></a>Identificando os dados do cliente

O Azure Time Series Insights considera dados pessoais como dados associados aos administradores e usuários do Time Series Insights. O Time Series Insights armazena a ID de objeto do Azure Active Directory de usuários com acesso ao ambiente. O portal do Azure exibe os endereços de email do usuário, mas esses endereços de email não são armazenados no Time Series Insights; eles são pesquisados dinamicamente usando a ID de objeto do Azure Active Directory no Azure Active Directory.

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

Um administrador de locatários pode excluir os dados do cliente usando o portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

No entanto, antes de excluir os dados do cliente por meio do portal, você deve remover as políticas de acesso do usuário do ambiente do Time Series Insights no portal do Azure. Para obter mais informações, confira [Conceder o acesso a dados a um ambiente do Time Series Insights usando o portal do Azure](time-series-insights-data-access.md).

Você também pode executar operações de exclusão em políticas de acesso usando a API REST. Para obter mais informações, confira [Políticas de Acesso – Excluir](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

O Time Series Insights é integrado à folha Política no portal do Azure. O Time Series Insights e a folha Política permitem que você exiba, exporte e exclua os dados do usuário armazenados no serviço. Qualquer ação de exclusão realizada na folha Política do portal do Azure resulta na exclusão dos dados do usuário no Time Series Insights. Por exemplo, se um usuário tem uma consulta pessoal salva, essa consulta é excluída permanentemente do explorador do Time Series Insights. Se o usuário tem uma consulta compartilhada salva, a consulta é persistida, mas as informações do usuário são excluídas permanentemente. A observação a seguir contém instruções sobre como realizar essas tarefas.

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

De forma semelhante à exclusão de dados, um administrador de locatários pode exibir e exportar os dados armazenados no Time Series Insights por meio da folha Política no portal do Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Caso você seja um administrador de locatários, exiba as políticas de acesso a dados no ambiente do Time Series Insights no portal do Azure. Para obter mais informações, confira [Conceder o acesso a dados a um ambiente do Time Series Insights usando o portal do Azure](time-series-insights-data-access.md).

Também é possível executar operações de exportação nas políticas de acesso usando a operação "listar por ambiente" na API REST fornecida. Para obter mais informações, confira [Políticas de Acesso – Listar por Ambiente](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Para excluir os dados armazenados no Time Series Insights

Os dados pessoais podem chegar ao armazenamento do Time Series Insights, um cenário diferente dos dados do usuário e administrador. Caso você considere os dados armazenados no Time Series Insights como dados pessoais, exporte e exclua esses dados usando as seguintes etapas:

**Exibir e exportar os dados**

Para exibir e exportar os dados armazenados no Time Series Insights, é necessário pesquisá-los. Use o explorador do Time Series Insights ou as APIs de consulta do Time Series Insights para exibir e exportar os dados. Para exibir e exportar os dados usando o explorador do Time Series Insights, primeiro pesquise para encontrar os dados do usuário em questão. Após a pesquisa, clique com o botão direito do mouse no gráfico e selecione **Explorar eventos**. A grade de eventos é exibida e apresenta opções para exportar os dados como CSV e JSON.

Para obter mais informações, confira [Explorador do Azure Time Series Insights](time-series-insights-explorer.md).

**Excluir os dados**

No momento, o Time Series Insights não dá suporte à exclusão granular de dados. No entanto, o Time Series Insights fornece a capacidade de remover os dados do cliente armazenados no Time Series Insights com a configuração de políticas de retenção. Ajuste o período de retenção de todo o ambiente do Time Series Insights para qualquer número de dias, a fim de dar suporte aos seus requisitos de exclusão.

Para obter mais informações, confira [Configurando a retenção no Time Series Insights](time-series-insights-how-to-configure-retention.md).