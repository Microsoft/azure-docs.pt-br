---
title: "Atualizar os Serviços de Mídia após implantar as chaves de acesso de armazenamento | Microsoft Docs"
description: "Este artigo fornece diretrizes sobre como atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento

Ao criar uma nova conta do AMS (Serviços de Mídia do Azure), você também é solicitado a selecionar uma conta de Armazenamento do Azure, que é usada para armazenar o conteúdo de mídia. Você pode adicionar mais de uma conta de armazenamento à sua conta dos Serviços de Mídia. Este tópico mostra como revezar as chaves de armazenamento. Também mostra como adicionar contas de armazenamento a uma conta de mídia. 

Para executar as ações descritas neste tópico, use as [APIs ARM](https://docs.microsoft.com/rest/api/media/mediaservice) e o [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Para saber mais, consulte [Como gerenciar os recursos do Azure com o PowerShell e o Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Visão geral

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as conexões de armazenamento mais seguro, é recomendável regenerar periodicamente e fazer uma rotação de sua chave de acesso de armazenamento. Duas chaves de acesso (primária e secundária) são fornecidas para habilitá-lo a manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. Esse procedimento também é chamado de "implantação de chaves de acesso".

Os Serviços de Mídia dependem de uma chave de armazenamento fornecida a eles. Especificamente, os localizadores que são usados para transmitir ou baixar os ativos dependem da chave de acesso de armazenamento especificada. Quando uma conta AMS é criada, ela usa uma dependência na chave de acesso de armazenamento primário por padrão; no entanto, como um usuário, você pode atualizar a chave de armazenamento que o AMS tem. Você deve garantir que os Serviços de Mídia saibam qual chave usar seguindo as etapas descritas neste tópico.  

>[!NOTE]
> Se tiver várias contas de armazenamento, você deverá executar esse procedimento com cada conta de armazenamento. A ordem de rotação das chaves de armazenamento não é fixa. Você pode revezar a primeira chave secundária e, em seguida, a chave primária, ou vice-versa.
>
> Antes de executar as etapas descritas neste tópico em uma conta de produção, teste-as em uma conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Etapas para revezar as chaves de armazenamento 
 
 1. Altere a Chave primária da conta de armazenamento por meio do cmdlet do powershell ou do Portal do [Azure](https://portal.azure.com/).
 2. Chame o cmdlet Sync-AzureRmMediaServiceStorageKeys com os parâmetros apropriados para forçar a conta de mídia a obter as chaves da conta de armazenamento
 
    O exemplo a seguir mostra como sincronizar chaves para contas de armazenamento.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Aguarde uma hora mais ou menos. Verifique se os cenários de transmissão estão funcionando.
 4. Altere a chave secundária da conta de armazenamento por meio do cmdlet do powershell ou do portal do Azure.
 5. Chame Sync-AzureRmMediaServiceStorageKeys powershell com os parâmetros apropriados para forçar a conta de mídia a obter as novas chaves da conta de armazenamento. 
 6. Aguarde uma hora mais ou menos. Verifique se os cenários de transmissão estão funcionando.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet do powershell 

O exemplo a seguir demonstra como obter a conta de armazenamento e sincronizá-la com a conta do AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Etapas para adicionar contas de armazenamento à sua conta do AMS

O tópico a seguir mostra como adicionar contas de armazenamento à sua conta do AMS: [Anexar várias contas de armazenamento a uma conta de Serviços de Mídia](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de agradecer às pessoas que contribuíram para a criação deste documento: Cenk Dingiloglu, Milan Gada, Seva Titov.
