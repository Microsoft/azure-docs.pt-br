---
title: Converter modelo de portal para especificação de modelo
description: Descreve como converter um modelo existente na Galeria de portal do Azure para uma especificação de modelo.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555945"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Converter a Galeria de modelos no portal em especificações de modelo

O portal do Azure fornece uma maneira de armazenar modelos de Azure Resource Manager (modelos ARM) em sua conta. No entanto, as [especificações de modelo](template-specs.md) oferecem uma maneira mais fácil de compartilhar seus modelos com os usuários em sua organização e vincular a outros modelos. Este artigo mostra como converter modelos existentes na Galeria de modelos para especificações de modelo.

Para ver se você tem modelos a serem convertidos, exiba a [Galeria de modelos no portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Esses modelos têm o tipo de recurso `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Converter com script do PowerShell

Para simplificar a conversão de modelos na Galeria de modelos, use um script do PowerShell do repositório de modelos de início rápido do Azure. Ao executar o script, você pode criar uma nova especificação de modelo para cada modelo ou baixar um modelo que cria a especificação do modelo. O script não exclui o modelo da Galeria de modelos.

1. Copie o [script de migração](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Salve uma cópia local com o nome *Migrate-GalleryItems.ps1*.
1. Para criar novas especificações de modelo, forneça valores para os `-ResourceGroupName` `-Location` parâmetros e. 

   Defina `ItemsToExport` como `MyGalleryItems` para exportar seus modelos. Defina-o como `AllGalleryItems` para exportar todos os modelos aos quais você tem acesso.

   O exemplo a seguir cria novas especificações de modelo para cada modelo em um grupo de recursos chamado **migratedRG**. O script criará o grupo de recursos se ele não existir.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Para baixar modelos que você pode usar para criar as especificações do modelo, não forneça valores para o grupo de recursos ou o local. Em vez disso, especifique `-ExportToFile` . O modelo não é o mesmo que o modelo na galeria. Em vez disso, ele contém um recurso de especificação de modelo que cria a especificação do modelo para seu modelo.

   O exemplo a seguir baixa os modelos sem criar especificações de modelo.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Para saber como implantar o modelo que cria a especificação do modelo, consulte [início rápido: criar e implantar a especificação do modelo (versão prévia)](quickstart-create-template-specs.md).

Para obter mais informações sobre o script e seus parâmetros, consulte [criar TemplateSpecs de modelos da Galeria de](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)modelos.

## <a name="manually-convert-through-portal"></a>Converter manualmente por meio do portal

Você pode copiar manualmente modelos da galeria para uma nova especificação de modelo.

1. Abra os [modelos (versão prévia)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) no Portal.
1. Selecione o modelo a ser migrado.
1. Selecione **Exibir modelo**.
1. Copie o conteúdo do modelo.
1. Na barra de pesquisa do portal, procure por **especificações de modelo**. Selecione essa opção.
1. Selecione **Criar especificação de modelo**.
1. Forneça valores para o nome, a assinatura, o grupo de recursos, o local e a versão.
1. Selecione **Avançar: Editar modelo**.
1. Para o conteúdo do modelo, Cole o modelo que você copiou da Galeria de modelos.
1. Selecione **Examinar + criar**.
1. Depois que a validação for concluída com êxito, selecione **criar**.

Se você precisar compartilhar a especificação do modelo com outros usuários em sua organização, [defina o controle de acesso baseado em função](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) para o grupo ou os usuários que precisam de acesso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as especificações de modelo, consulte [criar e implantar especificações de modelo](template-specs.md).
