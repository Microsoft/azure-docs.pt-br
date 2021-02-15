---
title: incluir arquivo
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283561"
---
Se precisar de configuração adicional, use uma tarefa agendada que seja executada na inicialização para fazer alterações finais na VM após sua implantação. Considere também o seguinte:

- Se for uma tarefa de execução única, a tarefa deverá ser excluída após a conclusão ser realizada com êxito.
- As configurações não devem depender de unidades diferentes de C ou D, pois apenas essas duas unidades sempre têm garantia de existência (a unidade C é o disco do sistema operacional e a unidade D é o disco local temporário).

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](../../virtual-machines/extensions/features-linux.md).