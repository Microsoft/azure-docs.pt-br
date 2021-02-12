---
title: Residência de dadosResidência de dados
description: Descreve a residência de dados ao usar a renderização remota do Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576863"
---
# <a name="azure-remote-rendering-data-residency"></a>Residência de dados de renderização remota do Azure 
Este artigo explica o conceito de residência de dados e como ele se aplica à renderização remota do Azure. 

## <a name="data-residency"></a>Residência de dadosResidência de dados 
A renderização remota do Azure usa contêineres de blob do Azure fornecidos pelo usuário para o armazenamento de modelo. Quando o modelo não está em uso, ele permanece na região de armazenamento de BLOBs do Azure fornecida pelo usuário. Quando o modelo é usado para renderização, os dados são copiados para a região que o usuário escolhe ao iniciar a sessão de renderização.

## <a name="next-steps"></a>Próximas etapas
Se você quiser saber como configurar um contêiner de armazenamento de BLOBs do Azure para a renderização remota do Azure, confira [converter um modelo para renderização](../quickstarts/convert-model.md).
