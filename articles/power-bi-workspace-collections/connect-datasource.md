---
title: "Conectando-se a uma fonte de dados nas Coleções de Espaços de Trabalho do Power BI | Microsoft Docs"
description: "Saiba como se conectar a uma fonte de dados dentro de Coleções de Espaços de Trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>Conectar-se a uma fonte de dados

Com as **Coleções de Espaços de Trabalho do Power BI**, você pode inserir relatórios em seu próprio aplicativo. Quando você insere um relatório do Power BI em seu aplicativo, o relatório conecta-se aos dados subjacentes por meio da **importação** de uma cópia dos dados ou **conectando-se diretamente** à fonte de dados usando o **DirectQuery**.

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Estas são as diferenças entre o uso de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas, *e dados* são importados ou copiados para o conjunto de dados do relatório. Para ver as alterações ocorridas nos dados subjacentes, você deve atualizar ou importar novamente um conjunto de dados completo e atual. |Somente *tabelas e colunas* são importados ou copiados para o conjunto de dados do relatório. Você sempre pode exibir os dados mais atuais. |

Com as Coleções de Espaços de Trabalho do Power BI, você pode usar DirectQuery com fontes de dados de nuvem, mas não em fontes de dados locais no momento.

> [!NOTE]
> Não há suporte para o Gateway de Dados Local com o as Coleções de Espaços de Trabalho do Power BI no momento. Isso significa que não é possível usar o DirectQuery com fontes de dados locais.

## <a name="supported-data-sources"></a>Fontes de dados com suporte

**DirectQuery**
* Banco de Dados SQL Azure
* SQL Data Warehouse do Azure

**Importaçãoação**

É possível importar usando todas as fontes de dados disponíveis dentro do Power BI Desktop. **Não** é possível atualizar esses dados dentro de Coleções de Espaços de Trabalho do Power BI. É necessário carregar as alterações feitas no arquivo PBIX nas Coleções de Espaços de Trabalho do Power BI. Isso ocorre quando não há gateways disponíveis. 

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

* [Introdução às Coleções de Espaços de Trabalho do Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)

