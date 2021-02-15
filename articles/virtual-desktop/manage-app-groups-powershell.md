---
title: Gerenciar grupos de aplicativos para a área de trabalho virtual do Windows PowerShell-Azure
description: Como gerenciar grupos de aplicativos de área de trabalho virtual do Windows com o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6f24dea00a174aa0276a9b30add0854c3694056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008637"
---
# <a name="manage-app-groups-using-powershell"></a>Gerenciar grupos de aplicativos usando o PowerShell

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

O grupo de aplicativos padrão criado para um novo pool de hosts da Área de Trabalho Virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos individuais do menu **Iniciar**.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo do RemoteApp.
> * Permitir acesso a programas do RemoteApps.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você seguiu as instruções em [Configurar o módulo do PowerShell](powershell-module.md) para configurar o módulo do PowerShell e entrar em sua conta do Azure.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

Para criar um grupo do RemoteApp com o PowerShell:

1. Execute o seguinte cmdlet do PowerShell para criar um grupo de aplicativos vazio do RemoteApp.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Opcional) Para verificar se o grupo de aplicativos foi criado, execute o cmdlet a seguir para ver uma lista de todos os grupos de aplicativos para o pool de hosts.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Execute o cmdlet a seguir para obter uma lista de aplicativos do menu **Iniciar** na imagem de máquina virtual do pool de hosts. Anote os valores de **FilePath**, **IconPath**, **IconIndex** e outras informações importantes do aplicativo que você deseja publicar.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   A saída deve mostrar todos os itens do menu iniciar em um formato como este:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Execute o cmdlet a seguir para instalar o aplicativo com base no `AppAlias`. O `AppAlias` fica visível quando você executa a saída da etapa 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (Opcional) Execute o cmdlet a seguir para publicar um novo programa RemoteApp no grupo de aplicativos criado na etapa 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Para verificar se o aplicativo foi publicado, execute o cmdlet a seguir.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Repita as etapas 1 a 5 para cada aplicativo que deseja publicar para esse grupo de aplicativos.
8. Execute o cmdlet a seguir para permitir aos usuários acesso aos programas RemoteApps no grupo de aplicativos.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Próximas etapas

Se você veio com este guia de instruções de nossos tutoriais, confira [criar um pool de hosts para validar atualizações de serviço](create-validation-host-pool.md). Você pode usar um pool de host de validação para monitorar a atualizações de serviço antes de implantá-las em seu ambiente de produção.