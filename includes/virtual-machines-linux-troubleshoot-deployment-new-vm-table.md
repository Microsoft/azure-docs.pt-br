---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171925"
---
A tabela a seguir lista as possíveis combinações de upload e captura de imagens de sistema operacional especializadas e generalizadas do Linux. As combinações que serão processadas sem erros são indicadas por um Y e aquelas que gerarão erros são indicadas por um N. As causas e resoluções para os diferentes erros que ocorrem são fornecidas abaixo da tabela.

| SO | Upload espec. | Upload gen. | Captura espec. | Captura gen. |
| --- | --- | --- | --- | --- |
| Linux gen. |N<sup>1</sup> |S |N<sup>3</sup> |S |
| Linux espec. |S |N<sup>2</sup> |S |N<sup>4</sup> |

