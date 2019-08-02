---
title: Área de trabalho Virtual Windows solução de problemas de visão geral, comentários e suporte - Azure
description: Uma visão geral para solução de problemas ao configurar um ambiente de locatário de área de trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605202"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Visão geral da solução de problemas, comentários e suporte

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de locatário de área de trabalho Virtual do Windows e fornece maneiras de resolver os problemas.

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="escalation-tracks"></a>Faixas de escalonamento de bloqueios

Use a tabela a seguir para identificar e resolver problemas que você pode encontrar ao configurar um ambiente de locatário usando o cliente de área de trabalho remota. Depois de configurar seu locatário, você pode usar nosso novo [serviço de diagnóstico](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) para identificar problemas para cenários comuns.

>[!NOTE]
>Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Sempre que fazemos referência ao suporte da área de trabalho Virtual do Windows, acesse nosso fórum da comunidade de tecnologia por enquanto. Visite o [comunidade de tecnologia de área de trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir problemas com a equipe de produto e os membros da comunidade do Active Directory. Se você precisar resolver um problema de suporte, inclua a ID de atividade e o período de tempo aproximado para quando o problema ocorreu.

| **Problema**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criação de um locatário                                                    | Se houver uma interrupção do Azure, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/options/); caso contrário, entre em contato com **suporte remoto Desktop Windows/serviços de área de trabalho Virtual**.|
| Acessando modelos do Marketplace no portal do Azure       | Se houver uma interrupção do Azure, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/options/). <br> <br> Modelos de área de trabalho Virtual do Windows Marketplace do Azure estão disponíveis gratuitamente.|
| Acessando o Azure Resource Manager templates do GitHub                                  | Consulte a seção "Host de sessão da área de trabalho Virtual do Windows Criando VMs" [criação de pool de locatário e host](troubleshoot-set-up-issues.md). Se o problema ainda não foi resolvido, entre em contato com o [equipe de suporte do GitHub](https://github.com/contact). <br> <br> Se o erro ocorrer depois de acessar o modelo no GitHub, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/options/).|
| Configurações de rede Virtual do Azure (VNET) e a rota expressa de pool de host do sessão               | Entre em contato com **suporte do Azure (rede)** . |
| Pool de host de sessão criação de máquina Virtual (VM) quando os modelos do Azure Resource Manager fornecidos com a área de trabalho Virtual do Windows não estão sendo usados. | Entre em contato com **suporte do Azure (computação)** . <br> <br> Para problemas com os modelos do Azure Resource Manager que são fornecidos com a área de trabalho Virtual do Windows, consulte a seção de locatário de criação de Desktop Virtual do Windows do [criação de pool de locatário e host](troubleshoot-set-up-issues.md). |
| Gerenciando o ambiente de host de sessão de área de trabalho Virtual do Windows no portal do Azure    | Entre em contato com **suporte do Azure**. <br> <br> Para problemas de gerenciamento ao usar o Remote Desktop Services/Windows Virtual da área de trabalho PowerShell, consulte [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md) ou entre em contato com o **equipe de suporte ao Remote Desktop Services/Windows área de trabalho Virtual** . |
| Gerenciamento de configuração de área de trabalho Virtual do Windows vinculados aos pools de host e grupos de aplicativos (grupos de aplicativos)      | Ver [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md), ou entre em contato com o **equipe de suporte ao Remote Desktop Services/Windows área de trabalho Virtual**. <br> <br> Se os problemas estão vinculados a interface gráfica do usuário (GUI) exemplo, fale com a comunidade do Yammer.|
| Mau funcionamento de clientes de área de trabalho remota em Iniciar                                                 | Ver [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md) e, se isso não resolver o problema, contate **equipe de suporte ao Remote Desktop Services/Windows área de trabalho Virtual**.  <br> <br> Se for um problema de rede, os usuários precisam entrar em contato com seu administrador de rede. |
| Conexão, mas nenhum feed                                                                 | Solucionar problemas usando o "usuário se conecta, mas nada é exibido (Nenhum feed)" seção [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md). <br> <br> Se os usuários atribuídos a um grupo de aplicativos, escalone para o **equipe de suporte ao Remote Desktop Services/Windows área de trabalho Virtual**. |
| Problemas de descoberta de feeds devido à rede                                            | Os usuários precisam entrar em contato com seu administrador de rede. |
| Clientes de conexão                                                                    | Ver [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md) e se isso não resolver seu problema, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta dos aplicativos remotos ou área de trabalho                                      | Se os problemas estão vinculados a um aplicativo específico ou um produto, entre em contato com a equipe responsável por produto. |
| Erros ou mensagens de licenciamento                                                          | Se os problemas estão vinculados a um aplicativo específico ou um produto, entre em contato com a equipe responsável por produto. |

## <a name="next-steps"></a>Próximas etapas

- Para solucionar problemas durante a criação de um pool de locatário e o host em um ambiente de área de trabalho Virtual do Windows, consulte [criação de pool de locatário e host](troubleshoot-set-up-issues.md).
- Para solucionar problemas ao configurar uma máquina virtual (VM) na área de trabalho Virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho Virtual do Windows, consulte [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho Virtual do Windows, consulte [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente de visualização de área de trabalho do Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).