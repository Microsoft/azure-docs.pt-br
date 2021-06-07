---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633441"
---
Os Arquivos do Azure oferecem quatro camadas diferentes de armazenamento: premium, transação otimizada, quente e fria para permitir que você personalize seus compartilhamentos conforme os requisitos de desempenho e preço de seu cenário:

- **Premium**: os compartilhamentos de arquivo Premium contam com SSDs (unidades de estado sólido) e fornecem alto desempenho consistente e baixa latência, em milissegundos de dígito único para a maioria das operações de E/S e para cargas de E/S intensivas. Compartilhamentos de arquivo Premium são adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, hospedagem de websites e ambientes de desenvolvimento. Os compartilhamentos de arquivo Premium podem ser usados com protocolos SMB e NFS (Network File System).
- **Transação otimizada**: Compartilhamentos de arquivos com transação otimizada permitem cargas de trabalho pesadas de transação que não precisam da latência oferecida por compartilhamentos de arquivos Premium. Os compartilhamentos de arquivo com transação otimizada são oferecidos no hardware de armazenamento padrão apoiado por HDs (unidades de disco rígido). A transação otimizada tem sido, historicamente, chamada de "padrão". No entanto, isso se refere ao tipo de mídia de armazenamento, não à camada em si (as camadas quente e fria também são "padrão", pois estão no hardware de armazenamento padrão).
- **Frequente**: os compartilhamentos de arquivo frequentes oferecem armazenamento otimizado para cenários de compartilhamento de arquivos de uso geral, como compartilhamentos de equipe. Os compartilhamentos de arquivo quentes são oferecidos no hardware de armazenamento padrão apoiado por HDs.
- **Esporádico**: Os compartilhamentos de arquivos frios oferecem armazenamento econômico otimizado para cenários de armazenamento de arquivos online. Os compartilhamentos de arquivo frios são oferecidos no hardware de armazenamento padrão apoiado por HDs.

Os compartilhamentos de arquivo Premium são implantados no tipo de **conta de armazenamento do FileStorage** e só estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de cobrança provisionado para compartilhamentos de arquivos premium, confira [Noções básicas sobre provisionamento de compartilhamentos de arquivos premium](../articles/storage/files/understanding-billing.md#provisioned-model). Os compartilhamentos de arquivo padrão, incluindo compartilhamentos de arquivo com transação otimizada, quentes e frios, são implantados no tipo de **conta de armazenamento GPv2 (uso geral versão 2)** e estão disponíveis por meio da cobrança de pagamento conforme o uso. Os compartilhamentos de arquivo quentes e frios estão disponíveis em todas as regiões públicas do Azure e do Azure Government. Os compartilhamentos de arquivo com transação otimizada estão disponíveis em todas as regiões do Azure, incluindo Azure China e Azure Alemanha.

Ao selecionar uma camada de armazenamento para sua carga de trabalho, considere seus requisitos de desempenho e uso. Se a sua carga de trabalho exigir uma latência de dígito único ou se você estiver usando uma mídia de armazenamento SSD local, a camada Premium provavelmente será a melhor opção. Se a baixa latência não for muito importante, por exemplo, com compartilhamentos de equipe montados localmente no Azure ou armazenados em cache no local usando a Sincronização de Arquivos do Azure, o armazenamento padrão poderá ser uma melhor opção da perspectiva de custo.

Depois de criar um compartilhamento de arquivo em uma conta de armazenamento, você não poderá movê-lo para camadas exclusivas de tipos de conta de armazenamento diferentes. Por exemplo, para mover um compartilhamento de arquivo com transação otimizada para a camada Premium, você precisará criar um compartilhamento de arquivo em uma conta de armazenamento do FileStorage e copiar os dados do compartilhamento original para um novo compartilhamento de arquivo na conta do FileStorage. Recomendamos usar o AzCopy para copiar dados entre compartilhamentos de arquivo do Azure, mas você também pode usar ferramentas como o `robocopy` no Windows ou o `rsync` no macOS e no Linux. 

Os compartilhamentos de arquivo implantados em contas de armazenamento GPv2 podem ser movidos entre as camadas Standard (com transação otimizada, quente e fria) sem criar uma conta de armazenamento e migrar os dados, mas você vai gerar custos de transações quando alterar a camada. Ao mover um compartilhamento de uma camada mais quente para uma camada mais fria, você vai gerar um custo de transação de gravação da camada mais fria para cada arquivo do compartilhamento. A movimentação de um compartilhamento de arquivo de uma camada mais fria para uma camada mais quente vai gerar um custo de transação de leitura da camada fria para cada arquivo do compartilhamento.

Confira [Noções básicas sobre a cobrança dos Arquivos do Azure](../articles/storage/files/understanding-billing.md) para saber mais.