---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993094"
---
Depois que o observador for criado, o evento `AnchorLocated` será acionado para cada âncora solicitada. Esse evento é acionado quando uma âncora é localizada ou se a âncora não puder ser localizada. Se essa situação ocorrer, o motivo será indicado no status. Após todas as âncoras de um observador serem processadas, sejam elas encontradas ou não, o evento `LocateAnchorsCompleted` será acionado. Há um limite de 35 identificadores por observador. 
