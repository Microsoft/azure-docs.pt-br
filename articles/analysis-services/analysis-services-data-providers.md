---
title: "Bibliotecas de cliente necessárias para conectar-se ao Azure Analysis Services | Microsoft Docs"
description: "Descreve as bibliotecas de cliente necessárias às ferramentas e ao aplicativos cliente para conectar o Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para conectar-se ao Azure Analysis Services

Bibliotecas de cliente são necessárias para que ferramentas e aplicativos cliente se conectem aos servidores do Analysis Services. 

O Analysis Services utiliza três bibliotecas de cliente. O ADOMD.NET e o AMO (Objetos de Gerenciamento do Analysis Services) são bibliotecas de cliente gerenciadas. O provedor OLE DB do Analysis Services (MSOLAP DLL) é uma biblioteca de cliente nativa. Normalmente, as três bibliotecas são instaladas ao mesmo tempo. O Azure Analysis Services exige as versões mais recentes. 

Os aplicativos cliente da Microsoft, como o Power BI Desktop e o Excel, instalam as três bibliotecas de cliente. Contudo, dependendo da versão ou da frequência de atualizações, as bibliotecas de cliente podem não ser as versões mais recentes exigidas pelo Azure Analysis Services. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos exigem a instalação manual das bibliotecas. As bibliotecas de cliente para instalação manual estão incluídas nos pacotes de recursos do SQL Server como pacotes distribuíveis. No entanto, essas bibliotecas de cliente estão vinculadas à versão do SQL Server e talvez não sejam as mais recentes.  

As bibliotecas de cliente para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de fonte de dados, confira [Conexões de fonte de dados](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Baixar as bibliotecas de cliente mais recentes  
Use as bibliotecas de cliente a seguir se estiver em um ambiente de produção e precisar de versões totalmente liberadas e com suporte completo.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Próximas etapas
[Conectar com Excel](analysis-services-connect-excel.md)    
[Conectar com o Power BI](analysis-services-connect-pbi.md)
