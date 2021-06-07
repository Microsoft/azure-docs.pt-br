---
title: Visão geral da solução de problemas da área de trabalho virtual do Windows (clássica)-Azure
description: Uma visão geral para solucionar problemas durante a configuração de um ambiente de locatário da área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b2220ffe92446338e1b9db9b329847d93830334
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005424"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Visão geral, comentários e suporte da área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../troubleshoot-set-up-overview.md).

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de locatário de área de trabalho virtual do Windows e fornece maneiras de resolver os problemas.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="escalation-tracks"></a>Faixas de escalonamento

Use a tabela a seguir para identificar e resolver os problemas que você pode encontrar ao configurar um ambiente de locatário usando o Área de Trabalho Remota Client. Depois de configurar o locatário, você pode usar nosso novo [serviço de diagnóstico](diagnostics-role-service-2019.md) para identificar problemas em cenários comuns.

>[!NOTE]
> Temos um fórum da comunidade técnica que você pode visitar para discutir seus problemas com a equipe de produto e os membros ativos da Comunidade. Visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para iniciar uma discussão.

| **Problema**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criando um locatário de área de trabalho virtual do Windows                                                    | Se houver uma interrupção do Azure, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/); caso contrário, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **implantação** para o tipo de problema e, em seguida, selecione **problemas criando um locatário de área de trabalho virtual do Windows** para o subtipo de problema.|
| Acessando modelos do Marketplace no portal do Azure       | Se houver uma interrupção do Azure, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Os modelos de área de trabalho virtual do Windows do Azure Marketplace estão disponíveis gratuitamente.|
| Acessando modelos de Azure Resource Manager do GitHub                                  | Consulte a seção [criando VMs de host de sessão de área de trabalho virtual do Windows](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) de [locatário e criação de pool de hosts](troubleshoot-set-up-issues-2019.md). Se o problema ainda não for resolvido, entre em contato com a [equipe de suporte do GitHub](https://github.com/contact). <br> <br> Se o erro ocorrer depois de acessar o modelo no GitHub, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/create-ticket/).|
| Configurações de rede virtual (VNET) do pool de hosts da sessão e de rota expressa               | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione o serviço apropriado (na categoria rede). |
| Criação de VM (máquina virtual) do pool de hosts da sessão quando Azure Resource Manager modelos fornecidos com a área de trabalho virtual do Windows não estão sendo usados | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione **máquina virtual executando o Windows** para o serviço. <br> <br> Para problemas com os modelos de Azure Resource Manager que são fornecidos com a área de trabalho virtual do Windows, consulte a seção criando locatário da área de trabalho virtual do Windows do [locatário e criação do pool de hosts](troubleshoot-set-up-issues-2019.md). |
| Gerenciando o ambiente de host de sessão de área de trabalho virtual do Windows no portal do Azure    | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gerenciamento ao usar o Serviços de Área de Trabalho Remota/Windows Virtual Desktop PowerShell, consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md) ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **configuração e gerenciamento** para o tipo de problema e, em seguida, selecione **problemas ao configurar o locatário usando o PowerShell** para o subtipo de problema. |
| Gerenciando a configuração da área de trabalho virtual do Windows vinculada a pools de hosts e grupos de aplicativos      | Consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantando e gerenciando contêineres de perfil FSLogix | Consulte [Guia de solução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Problemas de clientes de área de trabalho remota em iniciar                                                 | Consulte [solucionar problemas do cliente área de trabalho remota](../troubleshoot-client.md) e, se isso não resolver o problema,  [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema.  <br> <br> Se for um problema de rede, os usuários precisarão entrar em contato com o administrador da rede. |
| Conectado, mas sem feed                                                                 | Solucionar problemas usando o [usuário se conecta, mas nada é exibido (sem feed)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) seção de [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection-2019.md). <br> <br> Se os usuários tiverem sido atribuídos a um grupo de aplicativos,  [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema. |
| Problemas de descoberta de feed devido à rede                                            | Os usuários precisam entrar em contato com o administrador da rede. |
| Conectando clientes                                                                    | Consulte [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection-2019.md) e, se isso não resolver o problema, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration-2019.md). |
| Capacidade de resposta de aplicativos remotos ou área de trabalho                                      | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Mensagens de licenciamento ou erros                                                          | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Problemas ao usar as ferramentas de área de trabalho virtual do Windows no GitHub (modelos de Azure Resource Manager, ferramenta de diagnóstico, ferramenta de gerenciamento) | Consulte [modelos de Azure Resource Manager para serviços de área de trabalho remota](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatar problemas. |

## <a name="next-steps"></a>Próximas etapas

- Veja como solucionar problemas ao criar um pool de locatários e de hosts em um ambiente da Área de Trabalho Virtual do Windows em [Criação de pool de locatários e de hosts](troubleshoot-set-up-issues-2019.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration-2019.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection-2019.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](../troubleshoot-client.md)
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup-2019.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../../azure-resource-manager/management/view-activity-logs.md).
- Para saber mais sobre as ações para determinar erros durante a implantação, consulte [Exibir operações de implantação](../../azure-resource-manager/templates/deployment-history.md).
