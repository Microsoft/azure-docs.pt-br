---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171922"
---
A tabela a seguir lista as combinações possíveis de upload e captura de imagens do sistema operacional generalizadas (Gen.) e especializadas (spec.) do Windows. As combinações que serão processadas sem erros são indicadas por um Y e aquelas que gerarão erros são indicadas por um N. As causas e resoluções para os diferentes erros que ocorrem são fornecidas abaixo da tabela.

| Sistema operacional | Upload espec. | Upload gen. | Captura espec. | Captura gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |S |N<sup>3</sup> |S |
| Windows espec. |S |N<sup>2</sup> |S |N<sup>4</sup> |

