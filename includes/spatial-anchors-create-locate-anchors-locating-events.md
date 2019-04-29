---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232488"
---
Depois que o observador é criado, o `AnchorLocated` evento será acionado para cada âncora solicitada. Esse evento é acionado quando uma âncora for localizada, ou se a âncora não pode ser localizada. Se essa situação ocorrer, o motivo pelo qual será declarado no status. Após o processamento de todas as âncoras de um observador, encontrado ou não encontrado, o `LocateAnchorsCompleted` evento será disparado.
