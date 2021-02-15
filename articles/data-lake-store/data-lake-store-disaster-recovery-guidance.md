---
title: Orientação de recuperação de desastre para o Armazenamento de Data Lake do Azure Gen1 | Microsoft Docs
description: Saiba como proteger ainda mais seus dados de interrupções em toda a região ou exclusões acidentais além do armazenamento com redundância local de Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109112"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Diretrizes de alta disponibilidade e recuperação de desastre para Data Lake Storage Gen1

Data Lake Storage Gen1 fornece armazenamento com redundância local (LRS). Portanto, os dados em sua conta de Data Lake Storage Gen1 são resilientes a falhas de hardware transitórias em um datacenter por meio de réplicas automatizadas. Isso garante durabilidade e alta disponibilidade, atendendo ao SLA do Data Lake Storage Gen1. Este artigo fornece diretrizes de como proteger ainda mais seus dados contra as raras interrupções de toda a região ou contra exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres

É essencial que você prepare um plano de recuperação de desastres. Examine as informações neste artigo e esses recursos adicionais para ajudá-lo a criar seu próprio plano.

* [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Orientações técnicas de resiliência do Azure](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>Recomendações de melhores práticas

Recomendamos que você copie seus dados críticos para outra conta do Data Lake Storage Gen1 em outra região com uma frequência alinhada às necessidades do seu plano de recuperação de desastres. Há uma variedade de métodos para copiar dados, incluindo [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)ou [Azure data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

Se ocorrer uma indisponibilidade regional, você poderá acessar seus dados na região onde os dados foram copiados. Você pode monitorar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para determinar o status do serviço do Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientação de recuperação contra corrupção de dados ou exclusão acidental

Embora o Data Lake Storage Gen1 forneça resiliência de dados por meio de réplicas automatizadas, isso não impede que seu aplicativo (ou desenvolvedores / usuários) corrompam dados ou os excluam acidentalmente.

Para evitar a exclusão acidental, recomendamos que você primeiro defina as políticas de acesso corretas para sua conta do Data Lake Storage Gen1. Isso inclui a aplicação de [bloqueios de recursos do Azure](../azure-resource-manager/management/lock-resources.md) para bloquear recursos importantes e aplicar o controle de acesso de nível de conta e arquivo usando os [recursos de segurança de data Lake Storage Gen1](data-lake-store-security-overview.md)disponíveis. Também recomendamos que você crie cópias de seus dados críticos rotineiramente usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) em outra conta, pasta ou servidor do Data Lake Storage Gen1. Assinatura do Azure. Isso pode ser usado para a recuperação de um incidente de corrupção ou de exclusão de dados. O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

Você também pode habilitar o [log de diagnóstico](data-lake-store-diagnostic-logs.md) para uma conta de data Lake Storage Gen1 para coletar trilhas de auditoria de acesso a dados. As trilhas de auditoria fornecem informações sobre quem pode ter excluído ou atualizado um arquivo.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)