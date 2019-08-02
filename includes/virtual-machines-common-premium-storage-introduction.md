---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717131"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento Premium do Azure: projeto para alto desempenho

Este artigo fornece diretrizes para a criação de aplicativos de alto desempenho usando o Armazenamento Premium do Azure. Você pode usar as instruções fornecidas neste documento combinadas com as práticas recomendadas de desempenho aplicáveis às tecnologias usadas pelo aplicativo. Para ilustrar as diretrizes, usamos o SQL Server em execução no Armazenamento Premium como exemplo em todo este documento.

Embora, neste artigo, tenhamos abordado cenários da camada de Armazenamento, você precisará otimizar a camada de aplicativo. Por exemplo, se estiver hospedando um Farm do SharePoint no Armazenamento Premium do Azure, você poderá usar os exemplos do SQL Server deste artigo para otimizar o servidor de banco de dados. Adicionalmente, otimize o servidor Web e o servidor de Aplicativos do Farm do SharePoint para obter o melhor desempenho possível.

Este artigo ajudará a responder às perguntas comuns a seguir sobre como otimizar o desempenho de aplicativos no Armazenamento Premium do Azure.

* Como avaliar o desempenho do aplicativo?  
* Por que você não está vendo o alto desempenho esperado?  
* Quais fatores influenciam o desempenho do aplicativo no Armazenamento Premium?  
* Como esses fatores influenciam o desempenho do aplicativo no Armazenamento Premium?  
* Como você pode otimizar para IOPS, Largura de Banda e Latência?  

Fornecemos estas diretrizes especificamente para Armazenamento Premium porque as cargas de trabalho em execução no Armazenamento Premium são altamente sensíveis ao desempenho. Fornecemos exemplos onde apropriado. Também é possível aplicar algumas destas diretrizes a aplicativos em execução nas VMs da IaaS com discos de Armazenamento Padrão.