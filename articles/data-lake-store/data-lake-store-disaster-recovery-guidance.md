---
title: Orientação de recuperação de desastre para o Armazenamento de Data Lake do Azure Gen1 | Microsoft Docs
description: Orientação sobre recuperação de desastre para o Armazenamento de Data Lake do Azure Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878283"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Orientação de recuperação de desastre para dados no Azure Data Lake Storage Gen1

O Armazenamento de Data Lake do Azure Gen1 fornece armazenamento redundante localmente (LRS). Portanto, os dados da sua conta do Data Lake Storage Gen1 são resilientes a falhas transitórias de hardware em um datacenter por meio de réplicas automatizadas. Isso garante durabilidade e alta disponibilidade, atendendo ao SLA do Data Lake Storage Gen1. Este artigo fornece diretrizes de como proteger ainda mais seus dados contra as raras interrupções de toda a região ou contra exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres
É essencial para todos os clientes preparar seu próprio plano de recuperação de desastre. Leia as informações neste artigo para criar seu plano de recuperação de desastres. Aqui estão alguns recursos que podem ajudar você a criar seu próprio plano.

* [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas de resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Práticas recomendadas
Recomendamos que você copie seus dados críticos para outra conta do Data Lake Storage Gen1 em outra região com uma frequência alinhada às necessidades do seu plano de recuperação de desastres. Há uma variedade de métodos para copiar dados, incluindo o [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), o [Azure PowerShell](data-lake-store-get-started-powershell.md) ou o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

Se ocorrer uma indisponibilidade regional, você poderá acessar seus dados na região onde os dados foram copiados. Você pode monitorar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para determinar o status do serviço do Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientação de recuperação contra corrupção de dados ou exclusão acidental
Embora o Data Lake Storage Gen1 forneça resiliência de dados por meio de réplicas automatizadas, isso não impede que seu aplicativo (ou desenvolvedores / usuários) corrompam dados ou os excluam acidentalmente.

### <a name="best-practices"></a>Práticas recomendadas
Para evitar a exclusão acidental, recomendamos que você primeiro defina as políticas de acesso corretas para sua conta do Data Lake Storage Gen1.  Isso inclui a aplicação de [bloqueios de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para bloquear recursos importantes, bem como a aplicação de controle de acesso no nível da conta e do arquivo usando os recursos de segurança do [Data Lake Storage Gen1 disponíveis](data-lake-store-security-overview.md). Também recomendamos que você crie cópias de seus dados críticos rotineiramente usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) em outra conta, pasta ou servidor do Data Lake Storage Gen1. Assinatura do Azure.  Isso pode ser usado para a recuperação de um incidente de corrupção ou de exclusão de dados. O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

As organizações também podem habilitar o [log de diagnóstico](data-lake-store-diagnostic-logs.md) para sua conta do Data Lake Storage Gen1 coletar trilhas de auditoria de acesso a dados que forneçam informações sobre quem pode ter excluído ou atualizado um arquivo.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)

