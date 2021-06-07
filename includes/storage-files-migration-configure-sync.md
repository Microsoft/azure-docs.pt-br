---
title: Configurar Sincronização de Arquivos do Azure
description: Configurar Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043173"
---
Esta etapa reúne todos os recursos e pastas que você configurou na instância do Windows Server durante as etapas anteriores.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Localize o recurso do serviço de sincronização de armazenamento.
1. Crie um novo *grupo de sincronização* dentro do recurso de serviço de sincronização de armazenamento para cada compartilhamento de arquivos do Azure. Na terminologia Sincronização de Arquivos do Azure, o compartilhamento de arquivos do Azure se tornará um *ponto de extremidade de nuvem* na topologia de sincronização que você está descrevendo com a criação de um grupo de sincronização. Como você está criando o grupo de sincronização, dê a ele um nome familiar para que você reconheça qual conjunto de arquivos é sincronizado aqui. Certifique-se de fazer referência ao compartilhamento de arquivos do Azure com um nome correspondente.
1. Depois que o grupo de sincronização for criado, uma linha para ele aparecerá na lista de grupos de sincronização. Selecione o nome (um link) para exibir o conteúdo do grupo de sincronização. Você verá o compartilhamento de arquivos do Azure em **pontos de extremidade de nuvem**.
1. Localize o botão **+ Adicionar ponto de extremidade do servidor** . A pasta no servidor local que você provisionar se tornará o caminho para esse *ponto de extremidade do servidor*.
