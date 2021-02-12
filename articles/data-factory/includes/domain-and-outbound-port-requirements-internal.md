---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121564"
---
| Nomes de domínios                                          | Portas de saída | Descrição                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Nuvem pública: `*.servicebus.windows.net` <br> Azure governamental: `*.servicebus.usgovcloudapi.net` <br> Chinês `*.servicebus.chinacloudapi.cn`   | 443            | Exigido pelo runtime de integração auto-hospedada para criação interativa. |
| Nuvem pública: `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` <br> Azure governamental: `{datafactory}.{region}.datafactory.azure.us` <br> Chinês `{datafactory}.{region}.datafactory.azure.cn` | 443            | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. <br>Para novos Data Factory criados na nuvem pública, localize o FQDN da sua chave de Integration Runtime de hospedagem interna que está no formato {datafactory}. {região}. datafactory. Azure. net. Para o Data Factory antigo, se você não vir o FQDN na sua chave de Integração Auto-Hospedada, use *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| URL do cofre da chave | 443           | Exigido por Azure Key Vault se você armazenar a credencial no Key Vault. |
