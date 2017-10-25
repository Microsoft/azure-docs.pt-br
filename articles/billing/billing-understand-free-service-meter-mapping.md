---
title: "Serviço para mapeamento de medidor de conta gratuita – Azure | Microsoft Docs"
description: "Entenda o serviço para mapeamento de medidor dos serviços incluídos na conta gratuita."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 9d7e355e755f2bac8929ab16f7f71aa3b0702658
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-free-service-to-meter-mapping"></a>Entender o serviço gratuito para mapeamento de medidor

Cada serviço do Azure emite a utilização em relação a medidores, utilizados pelo sistema de cobrança do Azure para cobrar os usuários pelos serviços. Para entender melhor o uso dos serviços gratuitos, vamos analisar o serviço de mapeamento de medidor para esses serviços. Para saber como criar serviços gratuitos, consulte [Criar serviços gratuitos com a conta gratuita do Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Serviço para mapeamento de medidor de serviços qualificados da conta gratuita 

|    O Barramento de   | Nome do medidor no Portal do Azure | Nome do medidor no arquivo/API de uso | ID de medidor |
| ------------ | -------------------------- | -------------------------| -------- |
| VM do Linux B1S | Horas de Computação – VM Standard_B1 | Horas de Computação – Gratuito | 8260cba2-4437-47d1-a31e-2561cd370f50
| VM Windows B1S | Horas de Computação – VM Standard_B1 (Windows) | Horas de Computação – Gratuito | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| Endereços IP Públicos – VM B1S  | Horas de Endereço IP – Endereços IP Públicos | Horas de Endereço IP – Gratuito | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Armazenamento (GB) – Cosmos DB | Armazenamento (GB) – Gratuito | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 Unidades de Solicitação (horas) – Cosmos DB | 100 Unidades de Solicitação (horas) – Gratuito | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| Armazenamento de arquivo | E/S Padrão – Arquivos (GB) – Com redundância local | E/S Padrão – Arquivos (GB) – Gratuito | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Armazenamento de arquivo | E/S Padrão – Unidades de Operação de Leitura de Arquivo (por 10 mil) | E/S Padrão – Unidades de Operação de Leitura de Arquivo (por 10 mil) – Gratuito | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Armazenamento de arquivo | E/S Padrão – Unidades de Operação de Gravação de Arquivo (por 10 mil) | E/S Padrão – Unidades de Operação de Gravação de Arquivo (por 10 mil) – Gratuito | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Armazenamento de arquivo | E/S Padrão – Unidades de Operação Protocolo de Arquivo (por 10 mil) | E/S Padrão – Unidades de Operação Protocolo de Arquivo (por 10 mil) – Gratuito | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Armazenamento de arquivo | E/S Padrão – Unidades de Operação de Lista de Arquivos (por 10 mil) | E/S Padrão – Unidades de Operação de Lista de Arquivos (por 10 mil) – Gratuito | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Armazenamento de blobs de blocos quente | E/S Padrão – Operações de Leitura de Blob de Blocos Quente (por 10 mil) | E/S Padrão – Operações de Leitura de Blob de Blocos Quente (por 10 mil) – Gratuito |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Armazenamento de blobs de blocos quente | E/S Padrão – Blob de Blocos Quente (GB) – Com redundância local | E/S Padrão – Blob de Blocos Quente (GB) – Gratuito | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Armazenamento de blobs de blocos quente | E/S Padrão – Operações de Gravação do Blob de Blocos Quente (por 10 mil) | E/S Padrão – Operações de Gravação do Blob de Blocos Quente (por 10 mil) – Gratuito | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Armazenamento de blobs de blocos quente  | E/S Padrão – Operações de Gravação/Lista do Blob de Blocos Quente (por 10 mil) | E/S Padrão – Operações de Gravação/Lista do Blob de Blocos Quente (por 10 mil) – Gratuito | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disco gerenciado *  | Disco Gerenciado Standard/Instantâneos (GB) – Com redundância local | Disco Gerenciado Standard/Instantâneos (GB) – Gratuito | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disco gerenciado *  | Operações do Disco Gerenciado Standard (por 10 mil) | Operações do Disco Gerenciado Standard (por 10 mil) – Gratuito | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disco gerenciado *  | Armazenamento Premium – Blob de páginas/P6 (unidades) – Com redundância local | Armazenamento Premium – Blob de páginas/P6 (unidades) – Gratuito | 2b98c168-27ca-4cc1-b509-e887dec87657
| Banco de dados SQL | Dias de Banco de Dados Standard S0 – Banco de Dados SQL | Dias de Banco de Dados Standard S0 – Gratuito | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Compartilhado – Largura de banda ** | Transferência de Dados de Saída (GB) | Transferência de Dados de Saída (GB) – Gratuito | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Se você criar uma máquina virtual do Windows e escolher o disco gerenciado, consumirá do medidor de disco gerenciado como parte da máquina virtual.

\** Medidores compartilhados podem ser consumidos por meio de vários serviços. Por exemplo, Máquinas virtuais e Armazenamento emitem uso em relação ao medidor Transferência de Dados de Saída (GB).





## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
