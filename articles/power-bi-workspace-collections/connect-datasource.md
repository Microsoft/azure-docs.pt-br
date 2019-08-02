---
title: Conectando-se a uma fonte de dados nas Coleções de Workspaces do Power BI | Microsoft Docs
description: Saiba como se conectar a uma fonte de dados dentro de Coleções de Workspaces do Power BI.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 1ce666a017c1e5ac25b11fe1c22cdb37746f75df
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669150"
---
# <a name="connect-to-a-data-source"></a>Conectar-se a uma fonte de dados

Com as **Coleções de Workspaces do Power BI**, você pode inserir relatórios em seu próprio aplicativo. Quando você insere um relatório do Power BI em seu aplicativo, o relatório conecta-se aos dados subjacentes por meio da **importação** de uma cópia dos dados ou **conectando-se diretamente** à fonte de dados usando o **DirectQuery**.

> [!IMPORTANT]
> As Coleções de Workspaces do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções de Workspaces do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Estas são as diferenças entre o uso de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas, *e dados* são importados ou copiados para o conjunto de dados do relatório. Para ver as alterações ocorridas nos dados subjacentes, você deve atualizar ou importar novamente um conjunto de dados completo e atual. |Somente *tabelas e colunas* são importados ou copiados para o conjunto de dados do relatório. Você sempre pode exibir os dados mais atuais. |

Com as Coleções de Workspaces do Power BI, você pode usar o DirectQuery com fontes de dados de nuvem, mas não em fontes de dados locais no momento.

> [!NOTE]
> Não há suporte para o Gateway de Dados Local com o as Coleções de Workspaces do Power BI no momento. Isso significa que não é possível usar o DirectQuery com fontes de dados locais.

## <a name="supported-data-sources"></a>Fontes de dados com suporte

**DirectQuery**
* Banco de Dados SQL do Azure
* SQL Data Warehouse do Azure

**Importarar**

É possível importar usando todas as fontes de dados disponíveis dentro do Power BI Desktop. **Não** é possível atualizar esses dados dentro de Coleções de Workspaces do Power BI. É necessário carregar as alterações feitas no arquivo PBIX nas Coleções de Workspaces do Power BI. Isso ocorre quando não há gateways disponíveis.

## <a name="benefits-of-using-directquery"></a>Benefícios do uso do DirectQuery

Há duas vantagens principais ao usar **DirectQuery**:

* **DirectQuery** permite compilar visualizações em conjuntos de dados grandes nos quais, caso contrário, seria inviável importar primeiro todos os dados.
* Alterações de dados subjacentes podem exigir uma atualização de dados e, para alguns relatórios, a necessidade de exibir dados atuais pode exigir grandes transferências de dados, tornando a importação de dados novamente inviável. Por outro lado, relatórios do **DirectQuery** sempre usam dados atuais.

## <a name="limitations-of-directquery"></a>Limitações do DirectQuery

Há algumas limitações no uso do **DirectQuery**:

* Todas as tabelas devem vir de um banco de dados individual.
* Se a consulta for excessivamente complexa, ocorrerá um erro. Para corrigir o erro, você deve refatorar a consulta para que se torne menos complexa. Se for necessário que a consulta seja complexa, você precisará importar os dados em vez de usar o **DirectQuery**.
* Filtragem de relação é limitada a uma única direção, em vez de ambos os trajetos.
* Você não pode alterar o tipo de dados de uma coluna.
* Por padrão, as limitações são colocadas em expressões DAX permitidas em medidas. Consulte [DirectQuery e medidas](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery e medidas
Para garantir que as consultas enviadas à fonte de dados subjacente tenham um desempenho aceitável, limitações são impostas para as medidas. Ao usar o **Power BI Desktop**, usuários avançados podem optar por ignorar essa limitação, escolhendo **Arquivo > Opções e configurações > Opções**. Na caixa de diálogo **Opções**, escolha **DirectQuery** e selecione a opção **Permitir medidas irrestritas no modo DirectQuery**. Quando essa opção estiver selecionada, qualquer expressão DAX válida para uma determinada medida poderá ser usada. No entanto, os usuários devem estar cientes de algumas expressões que funcionam bem quando os dados são importados podem resultar em consultas lentas para a origem de back-end no modo **DirectQuery**. 

## <a name="see-also"></a>Consulte também

* [Introdução às Coleções de Workspaces do Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Mais perguntas? [Experimentar a comunidade do Power BI](https://community.powerbi.com/)