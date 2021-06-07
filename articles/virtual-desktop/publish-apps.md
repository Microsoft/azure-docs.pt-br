---
title: Publicar aplicativos internos na Área de Trabalho Virtual do Windows – Azure
description: Como publicar aplicativos internos na Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 68db8e9d50dff0bb4580476bae990374830a2a1f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445815"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicar aplicativos internos na Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/publish-apps-2019.md).

Este artigo descreve como publicar aplicativos no seu ambiente de Área de Trabalho Virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicativos internos

Para publicar um aplicativo interno:

1. Conecte-se a uma das máquinas virtuais no seu pool de host.
2. Obtenha o **PackageFamilyName** do aplicativo que você deseja publicar seguindo as instruções [neste artigo](/powershell/module/appx/get-appxpackage).
3. Por fim, execute o seguinte cmdlet com `<PackageFamilyName>` substituído pelo **PackageFamilyName** encontrado na etapa anterior:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> A Área de Trabalho Virtual do Windows só dá suporte à publicação de aplicativos com locais de instalação que começam com `C:\Program Files\WindowsApps`.

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar um aplicativo, ele terá o ícone padrão de aplicativo do Windows em vez da imagem de ícone normal. Para alterar o ícone para normal, coloque a imagem do ícone desejado em um compartilhamento de rede. Os formatos de imagem com suporte são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar o Microsoft Edge

O processo usado para publicar o Microsoft Edge é um pouco diferente do processo de publicação para outros aplicativos. Para publicar o Microsoft Edge com a página inicial padrão, execute este cmdlet:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como configurar feeds para organizar como os aplicativos são exibidos para usuários em [Personalizar feed para usuários da Área de Trabalho Virtual do Windows](customize-feed-for-virtual-desktop-users.md).
- Saiba mais sobre o recurso de anexação de aplicativo MSIX em [Configurar anexação de aplicativo MSIX](app-attach.md).

