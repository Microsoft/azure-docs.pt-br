---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776350"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do lote do Azure por região e assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 10-100 | N/A<sup>1</sup> |
| Núcleos de baixa prioridade por conta do Lote | 10-100 | N/A<sup>2</sup> |
| Trabalhos ativos e agendas de trabalho<sup>3</sup> por Conta de lote | 100-300 | 1,000<sup>4</sup> |
| Núcleos por conta do Lote | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>o número de núcleos dedicados por conta do lote pode ser aumentado, mas o número máximo não está especificado. Para discutir as opções de aumento, entre em contato com o suporte do Azure.

<sup>2</sup>o número de núcleos de baixa prioridade por conta do lote pode ser aumentado, mas o número máximo não está especificado. Para discutir as opções de aumento, entre em contato com o suporte do Azure.

<sup>3</sup>trabalhos concluídos e as agendas de trabalho não forem limitadas.

<sup>4</sup>para solicitar um aumento além desse limite, entre em contato com o suporte do Azure.
