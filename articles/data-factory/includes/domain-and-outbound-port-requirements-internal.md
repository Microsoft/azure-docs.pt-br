---
title: incluir arquivo
description: incluir arquivo
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389478"
---
| Nomes de domínios                                          | Portas de saída | Descrição                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Nuvem pública: `*.servicebus.windows.net` <br> Azure governamental: `*.servicebus.usgovcloudapi.net` <br> Chinês `*.servicebus.chinacloudapi.cn`   | 443            | Exigido pelo runtime de integração auto-hospedada para criação interativa. |
| Nuvem pública: `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` <br> Azure governamental: `{datafactory}.{region}.datafactory.azure.us` <br> Chinês `{datafactory}.{region}.datafactory.azure.cn` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. <br>Para novos Data Factory criados na nuvem pública, localize o FQDN da sua chave de Integration Runtime de hospedagem interna que está no formato {datafactory}. {região}. datafactory. Azure. net. Para o Data Factory antigo, se você não vir o FQDN na sua chave de Integração Auto-Hospedada, use *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| URL do cofre da chave | 443           | Exigido por Azure Key Vault se você armazenar a credencial no Key Vault. |
