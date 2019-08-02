---
title: Windows desktop virtual PowerShell-Azure
description: Como solucionar problemas com o PowerShell ao configurar um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 41c3c25962d5cb0d608a226ed77408460446bfa5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248191"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell da Área de Trabalho Virtual do Windows

Use este artigo para resolver erros e problemas ao usar o PowerShell com a área de trabalho virtual do Windows. Para obter mais informações sobre Serviços de Área de Trabalho Remota PowerShell, consulte [Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos do PowerShell usados durante a instalação da área de trabalho virtual do Windows

Esta seção lista os comandos do PowerShell que normalmente são usados durante a configuração da área de trabalho virtual do Windows e fornece maneiras de resolver problemas que podem ocorrer ao usá-los.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Erro: Comando Add-RdsAppGroupUser--o UserPrincipalName especificado já está atribuído a um grupo de aplicativos do RemoteApp no pool de hosts especificado

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Causa:** O nome de usuário usado já foi atribuído a um grupo de aplicativos de um tipo diferente. Os usuários não podem ser atribuídos a uma área de trabalho remota e a um grupo de aplicativos remotos no mesmo pool de hosts de sessão.

**Soluciona** Se o usuário precisar de aplicativos remotos e da área de trabalho remota, crie pools de hosts diferentes ou conceda acesso de usuário à área de trabalho remota, o que permitirá o uso de qualquer aplicativo na VM host da sessão.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Erro: Comando Add-RdsAppGroupUser--o UserPrincipalName especificado não existe no Azure Active Directory associado ao locatário Área de Trabalho Remota

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Causa:** O usuário especificado pelo-UserPrincipalName não pode ser encontrado no Azure Active Directory vinculado ao locatário da área de trabalho virtual do Windows.

**Soluciona** Confirme os itens na lista a seguir.

- O usuário está sincronizado com Azure Active Directory.
- O usuário não está vinculado ao B2C (Business to Consumer) ou ao Business-to-Business (B2B) Commerce.
- O locatário da área de trabalho virtual do Windows está vinculado ao Azure Active Directory correto.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities--o usuário não está autorizado a consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Causa:** parâmetro-tenantname

**Soluciona** Emita Get-RdsDiagnosticActivities com-tenantname \<locatárioname >.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: Get-RdsDiagnosticActivities--o usuário não está autorizado a consultar o serviço de gerenciamento

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Causa:** Usando a opção-Deployment.

**Correção:** -a opção de implantação pode ser usada somente por administradores de implantação. Normalmente, esses administradores são membros da equipe de área de trabalho virtual Serviços de Área de Trabalho Remota/Windows. Substitua a opção-Deployment por-tenantname \<locatárioname >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Erro: New-RdsRoleAssignment--o usuário não está autorizado a consultar o serviço de gerenciamento

**Causa 1:** A conta que está sendo usada não tem Serviços de Área de Trabalho Remota permissões de proprietário no locatário.

**Correção 1:** Um usuário com Serviços de Área de Trabalho Remota permissões de proprietário precisa executar a atribuição de função.

**Causa 2:** A conta que está sendo usada tem Serviços de Área de Trabalho Remota permissões de proprietário, mas não faz parte do Azure Active Directory do locatário ou não tem permissões para consultar a Azure Active Directory onde o usuário está localizado.

**Correção 2:** Um usuário com permissões de Active Directory precisa executar a atribuição de função.

>[!Note]
>New-RdsRoleAssignment não pode conceder permissões a um usuário que não existe no Azure Active Directory (AD).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um pool de locatários e de host em um ambiente de área de trabalho virtual do Windows, confira [criação de locatário e pool](troubleshoot-set-up-issues.md)de hosts.
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)de modelo do Resource Manager.
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
