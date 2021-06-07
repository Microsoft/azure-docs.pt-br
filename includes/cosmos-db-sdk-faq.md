---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90068721"
---
**Como serei notificado sobre a desativação do SDK?**

A Microsoft enviará uma notificação com 12 meses de antecedência informando sobre o fim do suporte para o SDK a ser desativado, para facilitar uma transição suave para um SDK com suporte. Você será notificado por meio de vários canais de comunicação: o portal do Azure, as atualizações do Azure e a comunicação direta com os administradores de serviços atribuídos.

**Durante o período de 12 meses, posso criar aplicativos usando um SDK do Azure Cosmos DB que será desativado?** 

Sim, durante o período de notificação de 12 meses, você poderá criar, implantar e modificar aplicativos usando o SDK do Azure Cosmos DB que será desativado. Recomendamos que você migre para uma versão do SDK do Azure Cosmos DB mais recente com suporte durante o período de notificação de 12 meses, conforme o necessário. 

**Após a data de desativação, o que acontece com os aplicativos que usam o SDK do Azure Cosmos DB sem suporte?** 

Após a data de desativação, o Azure Cosmos DB não fará mais correções de bug, não adicionará novos recursos nem dará suporte às versões desativadas do SDK. Se você preferir não fazer a atualização, as solicitações enviadas das versões desativadas do SDK continuarão sendo atendidas pelo serviço Azure Cosmos DB. 

**Quais versões do SDK terão os recursos e as atualizações mais recentes?**

Os novos recursos e atualizações serão adicionados somente à última versão secundária da última versão principal do SDK com suporte. Recomendamos que você sempre use a última versão para aproveitar os novos recursos, os aprimoramentos de desempenho e as correções de bug. Se você estiver usando uma versão antiga e não desativada do SDK, suas solicitações para o Azure Cosmos DB continuarão a funcionar, mas você não terá acesso a nenhuma funcionalidade nova.  

**O que fazer se eu não conseguir atualizar meu aplicativo antes da data limite?**

Recomendamos atualizar o mais rápido possível para o SDK mais recente. Depois que um SDK for marcado para desativação, você terá 12 meses para atualizar seu aplicativo. Se você não conseguir fazer a atualização até data de desativação, as solicitações enviadas das versões desativadas do SDK continuarão a ser atendidas pelo Azure Cosmos DB, para que os aplicativos em execução continuem a funcionar. No entanto, o Azure Cosmos DB não fará mais correções de bug, não adicionará novos recursos nem dará suporte às versões desativadas do SDK. 

Se você tem um plano de suporte e precisa de suporte técnico, [entre em contato conosco](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) abrindo um tíquete de suporte.
    


