---
title: Identidades gerenciadas no Azure HDInsight
description: Fornece uma visão geral da implementação de identidades gerenciadas no Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 5012b669b7460a44cb2732d7db7bf76fd1f567cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766949"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades gerenciadas no Azure HDInsight

Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (Azure AD) cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar as entidades de serviço no Azure AD ou manter as credenciais, como certificados.

Identidades gerenciadas podem ser usadas no HDInsight do Azure para permitir que os clusters para acessar serviços de domínio do AD do Azure, acessar o Azure Key Vault ou acessar arquivos no armazenamento do Azure Data Lake Gen2.

Há dois tipos de identidades gerenciadas: usuário atribuído e atribuída pelo sistema. HDInsight do Azure usa identidades gerenciadas atribuída pelo usuário. Uma identidade atribuída pelo usuário gerenciada é criada como um recurso autônomo do Azure que você pode atribuir a uma ou mais instâncias de serviço do Azure. Em contraste, uma identidade gerenciada atribuído pelo sistema é criada no Azure AD e habilitada diretamente em uma instância de serviço específico do Azure automaticamente. A vida de identidade gerenciada atribuída pelo sistema, em seguida, é vinculada à vida da instância do serviço for habilitado no.

## <a name="hdinsight-managed-identity-implementation"></a>Implementação de identidade gerenciada do HDInsight

No Azure HDInsight, identidades gerenciadas são provisionadas em cada nó do cluster. No entanto, esses componentes de identidade, só são utilizáveis pelo serviço do HDInsight. Atualmente, não há nenhum método com suporte para gerar tokens de acesso usando as identidades gerenciadas instaladas em nós de cluster do HDInsight. Para alguns serviços do Azure, as identidades gerenciadas são implementadas com um ponto de extremidade que você pode usar para adquirir tokens de acesso para interagir com outros serviços do Azure por conta própria.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Identidades gerenciadas podem ser criadas com qualquer um dos seguintes métodos:

* [Portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [PowerShell do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

As etapas restantes para configurar a identidade gerenciada dependem do cenário em que ele será usado.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Cenários de identidade gerenciada no Azure HDInsight

Identidades gerenciadas são usadas no Azure HDInsight em vários cenários. Consulte os documentos relacionados para a configuração detalhada e instruções de configuração:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Próximos passos

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
