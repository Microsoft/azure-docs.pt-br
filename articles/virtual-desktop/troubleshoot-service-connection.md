---
title: Solucionar problemas de conexão de serviço da Área de Trabalho Virtual do Windows – Azure
description: Como resolver problemas durante a configuração de conexões de serviço em um ambiente de locatário de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539082"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Use este artigo para resolver problemas com as conexões de cliente da Área de Trabalho Virtual do Windows.

## <a name="provide-feedback"></a>Fornecer comentários

Forneça comentários e troque informações sobre o Serviço da Área de Trabalho Virtual do Windows com a equipe do produto e outros membros ativos da comunidade na [Tech Community da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário consegue iniciar clientes da Área de Trabalho Remota e se autenticar, mas não vê nenhum ícone no feed de descoberta da Web.

1. Confirme se o usuário que está informando os problemas foi atribuído a grupos de aplicativos por meio desta linha de comando:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Confirme se o usuário está entrando com as credenciais corretas.

3. Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

4. Se o usuário fizer parte de um grupo de usuários Azure Active Directory (AD), verifique se o grupo de usuários é um grupo de segurança em vez de um grupo de distribuição. A área de trabalho virtual do Windows não dá suporte a grupos de distribuição do Azure AD.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>O usuário perde o feed existente e nenhum recurso remoto é exibido (sem feed)

Esse erro geralmente aparece depois que um usuário moveu sua assinatura de um locatário do Azure AD para outro. Como resultado, o serviço perde o controle de suas atribuições de usuário, pois eles ainda estão vinculados ao locatário antigo do Azure AD.

Para resolver isso, tudo o que você precisa fazer é reatribuir os usuários aos seus grupos de aplicativos.

Isso também pode ocorrer se um provedor CSP criou a assinatura e, em seguida, transferiu para o cliente. Para resolver isso, registre novamente o provedor de recursos.

1. Entre no portal do Azure.
2. Acesse **assinatura** e selecione sua assinatura.
3. No menu no lado esquerdo da página, selecione **provedor de recursos**.
4. Localize e selecione **Microsoft. DesktopVirtualization** e, em seguida, selecione **registrar novamente**.

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um ambiente de Área de Trabalho Virtual do Windows e pool de host em um ambiente da Área de Trabalho Virtual do Windows, consulte [Criação de ambiente e pool de host](troubleshoot-set-up-issues.md).
- Veja como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas relacionados ao agente de área de trabalho virtual do Windows ou conectividade de sessão, consulte [solucionar problemas comuns do agente de área de trabalho virtual do Windows](troubleshoot-agent.md).
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
