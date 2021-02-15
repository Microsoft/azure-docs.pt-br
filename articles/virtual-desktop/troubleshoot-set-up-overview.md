---
title: Visão geral da solução de problemas de área de trabalho virtual do Windows – Azure
description: Uma visão geral para solucionar problemas durante a configuração de um ambiente de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539102"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Visão geral de solução de problemas, comentários e suporte para área de trabalho virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de área de trabalho virtual do Windows e fornece maneiras de resolver os problemas.

## <a name="report-issues"></a>Relatar problemas

Para relatar problemas ou sugerir recursos para a área de trabalho virtual do Windows com a integração Azure Resource Manager, visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Você pode usar a comunidade de tecnologia para discutir as práticas recomendadas ou sugerir e votar em novos recursos.

Quando você fizer uma postagem solicitando ajuda ou propor um novo recurso, certifique-se de descrever seu tópico com o máximo de detalhes possível. Informações detalhadas podem ajudar outros usuários a responder sua pergunta ou entender o recurso para o qual você está propondo um voto.

## <a name="escalation-tracks"></a>Faixas de escalonamento

Antes de fazer qualquer outra coisa, certifique-se de verificar a [página de status](https://status.azure.com/status) do Azure e a [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) para verificar se o serviço do Azure está sendo executado corretamente.

Use a tabela a seguir para identificar e resolver os problemas que você pode encontrar ao configurar um ambiente usando o Área de Trabalho Remota Client. Após a configuração do seu ambiente, você pode usar nosso novo [serviço de diagnóstico](diagnostics-role-service.md) para identificar problemas em cenários comuns.

| **Problema**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Configurações de rede virtual (VNET) do pool de hosts da sessão e de rota expressa               | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione o serviço apropriado (na categoria rede). |
| Criação de VM (máquina virtual) do pool de hosts da sessão quando Azure Resource Manager modelos fornecidos com a área de trabalho virtual do Windows não estão sendo usados | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione **área de trabalho virtual do Windows** para o serviço. <br> <br> Para problemas com os modelos de Azure Resource Manager fornecidos com a área de trabalho virtual do Windows, consulte a seção Azure Resource Manager de erros de modelo da [criação do pool de hosts](troubleshoot-set-up-issues.md). |
| Gerenciando o ambiente de host de sessão de área de trabalho virtual do Windows no portal do Azure    | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gerenciamento ao usar o Serviços de Área de Trabalho Remota/Windows Virtual Desktop PowerShell, consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **configuração e gerenciamento** para o tipo de problema e, em seguida, selecione **problemas ao configurar o ambiente usando o PowerShell** para o subtipo de problema. |
| Gerenciando a configuração da área de trabalho virtual do Windows vinculada a pools de hosts e grupos de aplicativos      | Consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantando e gerenciando contêineres de perfil FSLogix | Consulte [Guia de solução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Problemas de clientes de área de trabalho remota em iniciar                                                 | Consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md) e, se isso não resolver o problema,  [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema.  <br> <br> Se for um problema de rede, os usuários precisarão entrar em contato com o administrador da rede. |
| Conectado, mas sem feed                                                                 | Solucionar problemas usando o [usuário se conecta, mas nada é exibido (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) seção de [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection.md). <br> <br> Se os usuários tiverem sido atribuídos a um grupo de aplicativos,  [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema. |
| Problemas de descoberta de feed devido à rede                                            | Os usuários precisam entrar em contato com o administrador da rede. |
| Conectando clientes                                                                    | Consulte [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection.md) e, se isso não resolver o problema, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta de aplicativos remotos ou área de trabalho                                      | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Mensagens de licenciamento ou erros                                                          | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Problemas com métodos ou ferramentas de autenticação de terceiros | Verifique se o provedor de terceiros dá suporte a cenários de área de trabalho virtual do Windows e aborde-os em relação a problemas conhecidos. |
| Problemas ao usar o Log Analytics para área de trabalho virtual do Windows | Para problemas com o esquema de diagnóstico, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Para consultas, visualização ou outros problemas no Log Analytics, selecione o tipo de problema apropriado em Log Analytics. |
| Problemas ao usar aplicativos M365 | Contate o centro de administração do M365 com uma das [Opções de ajuda do centro de administração do M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Próximas etapas

- Para solucionar problemas ao criar um pool de hosts em um ambiente de área de trabalho virtual do Windows, consulte [criação de pool de hosts](troubleshoot-set-up-issues.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas relacionados ao agente de área de trabalho virtual do Windows ou conectividade de sessão, consulte [solucionar problemas comuns do agente de área de trabalho virtual do Windows](troubleshoot-agent.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber mais sobre as ações para determinar erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
