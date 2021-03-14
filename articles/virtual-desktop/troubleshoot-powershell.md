---
title: Windows desktop virtual PowerShell-Azure
description: Como solucionar problemas com o PowerShell ao configurar um ambiente de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367615"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Use este artigo para resolver erros e problemas ao usar o PowerShell com a área de trabalho virtual do Windows. Para obter mais informações sobre Serviços de Área de Trabalho Remota PowerShell, consulte [Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandos do PowerShell usados durante a instalação da área de trabalho virtual do Windows

Esta seção lista os comandos do PowerShell que normalmente são usados durante a configuração da área de trabalho virtual do Windows e fornece maneiras de resolver problemas que podem ocorrer ao usá-los.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Erro: New-AzRoleAssignment: as informações fornecidas não são mapeadas para uma ID de objeto do AD

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Causa:** O usuário especificado pelo parâmetro *-SignInName* não pode ser encontrado no Azure Active Directory vinculado ao ambiente de área de trabalho virtual do Windows.

**Correção:** Certifique-se das ações a seguir.

- O usuário deve ser sincronizado com Azure Active Directory.
- O usuário não deve estar vinculado ao Business-to-Consumer (B2C) ou ao Business-to-Business (B2B) Commerce.
- O ambiente de área de trabalho virtual do Windows deve estar vinculado ao Azure Active Directory correto.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Erro: New-AzRoleAssignment: "o cliente com a ID de objeto não tem autorização para executar a ação sobre o escopo (código: AuthorizationFailed)"

**Causa 1:** A conta que está sendo usada não tem permissões de proprietário na assinatura.

**Correção 1:** Um usuário com permissões de proprietário precisa executar a atribuição de função. Como alternativa, o usuário precisa ser atribuído à função Administrador de acesso do usuário para atribuir um usuário a um grupo de aplicativos.

**Causa 2:** A conta que está sendo usada tem permissões de proprietário, mas não faz parte do Azure Active Directory do ambiente ou não tem permissões para consultar a Azure Active Directory onde o usuário está localizado.

**Correção 2:** Um usuário com permissões de Active Directory precisa executar a atribuição de função.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Erro: New-AzWvdHostPool--o local não está disponível para o tipo de recurso

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Causa: a área de trabalho virtual do Windows dá suporte à seleção do local de pools de hosts, grupos de aplicativos e espaços de trabalho para armazenar metadados de serviço em determinados locais. Suas opções estão restritas a onde esse recurso está disponível. Esse erro significa que o recurso não está disponível no local escolhido.

Correção: na mensagem de erro, uma lista de regiões com suporte será publicada. Em vez disso, use uma das regiões com suporte.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Erro: New-AzWvdApplicationGroup deve estar no mesmo local que o pool de hosts

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Causa:** Há uma incompatibilidade de local. Todos os pools de hosts, grupos de aplicativos e espaços de trabalho têm um local para armazenar metadados de serviço. Todos os objetos que você criar que estão associados entre si devem estar no mesmo local. Por exemplo, se um pool de hosts estiver em `eastus` , você também precisará criar os grupos de aplicativos no `eastus` . Se você criar um espaço de trabalho para registrar esses grupos de aplicativos, esse espaço de trabalho também precisará estar `eastus` .

**Correção:** Recupere o local em que o pool de hosts foi criado e, em seguida, atribua o grupo de aplicativos que você está criando para o mesmo local.

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a configuração de seu ambiente de área de trabalho virtual do Windows e pools de hosts, consulte [ambiente e criação de pool de hosts](troubleshoot-set-up-issues.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
