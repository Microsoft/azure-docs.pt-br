---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172325"
---
| Resource | Limite |
| --- | --- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho do cache | 5 GB por unidade<sup>2</sup> |
| Conexões simultâneas de back-end<sup>3</sup> por autoridade HTTP | 2\.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MB |
| Tamanho máximo do documento da política | 256 KB<sup>5</sup> | 
| Domínios de gateway personalizado máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de autoridade de certificação por instância de serviço | 10 | 
| Número máximo de instâncias de serviço por assinatura <sup>7</sup> | 20 | 
| Número máximo de assinaturas por instância de serviço <sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço <sup>7</sup> | 50 | 
| Número máximo de APIs por instância de serviço <sup>7</sup> | 50 | 
| Número máximo de operações de API por instância de serviço <sup>7</sup> | 1\.000 | 
| Duração de solicitação total máxima<sup>7</sup> | 30 segundos | 
| Tamanho máximo de carga útil do buffer <sup>7</sup> | 2 MB | 


<sup>1</sup>limites de colocação em escala dependem do tipo de preço. Para ver os tipos de preço e seus limites de colocação em escala, consulte [preços de gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>cache da unidade por tamanho depende do tipo de preço. Para ver os tipos de preço e seus limites de colocação em escala, consulte [preços de gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>conexões são agrupadas e reutilizadas, a menos que explicitamente fechadas pelo back-end.<br/>
<sup>4</sup>esse limite é por unidade das camadas Basic, Standard e Premium. A camada de desenvolvedor é limitada a 1.024. Esse limite não se aplica a camada de consumo.<br/> 
<sup>5</sup>esse limite se aplica para as camadas Basic, Standard e Premium. Na camada de consumo, o tamanho do documento de política é limitado a 4 KB.<br/>
<sup>6</sup>este recurso está disponível na camada Premium somente.<br/>
<sup>7</sup>esse recurso se aplica somente a camada de consumo.<br/>



