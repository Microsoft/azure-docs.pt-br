---
title: Contas de armazenamento do Azure com as contas de serviços de mídia do Azure | Microsoft Docs
description: Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 5c5bfa224b87040f5142663e6adab01072c6e6ba
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619290"
---
# <a name="azure-storage-accounts"></a>Contas de Armazenamento do Microsoft Azure

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. 

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. Recomenda-se usar as contas de armazenamento no mesmo local da conta de Serviços de Mídia para evitar os custos adicionais da saída de dados e latência.

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). <br/>As contas somente blob não são permitidas como **Primárias**. 

É recomendável que você use o GPv2, portanto, você pode tirar proveito dos recursos e desempenho mais recentes. Para saber mais sobre as contas de armazenamento, consulte [Visão geral da conta de Armazenamento do Microsoft Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Apenas a camada de acesso quente tem suporte para uso com serviços de mídia do Azure, embora outras camadas de acesso podem ser usadas para reduzir os custos de armazenamento no conteúdo que não está sendo usado ativamente.

Há diferentes SKUs que você pode escolher para sua conta de armazenamento. Para obter mais informações, confira [Contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. 

## <a name="assets-in-a-storage-account"></a>Ativos em uma conta de armazenamento

Em serviços de mídia v3, as APIs de armazenamento são usadas para carregar arquivos em ativos. Para obter mais informações, consulte [conceito de ativos](assets-concept.md).

> [!Note]
> Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelo SDK dos Serviços de Mídia sem o uso de APIs de Serviços de Mídia.
 
## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia v3:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v3|
|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia| Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Não é compatível<sup>(1)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|

<sup>1</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="storage-account-errors"></a>Erros de conta de armazenamento

O estado "Desconectado" de uma conta de Serviços de Mídia indica que a conta não tem mais acesso a uma ou mais contas de armazenamento anexadas, devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Mídia exigem chaves de acesso de armazenamento atualizadas para realizar várias tarefas na conta.

Veja a seguir os principais cenários que fariam com que a conta de Serviços de Mídia não tivesse acesso às contas de armazenamento anexadas. 

|Problema|Solução|
|---|---|
|A conta de Serviços de Mídia ou as contas de armazenamento anexadas foram migradas para assinaturas separadas. |Migre as contas de armazenamento ou a conta de Serviços de Mídia de modo que elas fiquem na mesma assinatura. |
|A conta de Serviços de Mídia está usando uma conta de armazenamento anexada em uma assinatura diferente, já que ela era uma conta Serviços de Mídia inicial compatível com este cenário. Todas as contas de Serviços de Mídia iniciais foram convertidas em contas modernas baseadas no Azure Resource Manager e terão o estado de Desconectadas. |Migre a conta de armazenamento ou a conta de Serviços de Mídia de modo que elas fiquem na mesma assinatura.|

## <a name="next-steps"></a>Próximas etapas

Para saber como anexar uma conta de armazenamento para sua conta de Serviços de Mídia, consulte [Criar uma conta](create-account-cli-quickstart.md).
