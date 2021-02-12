---
title: Conectar máquinas híbridas ao Azure por meio do centro de administração do Windows
description: Neste artigo, você aprende a instalar o agente e a conectar computadores ao Azure usando os servidores habilitados para Arc do Azure no centro de administração do Windows.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 8fa118a254689feade2897f083bb3aa2dc9db02c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183406"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Conectar máquinas híbridas ao Azure por meio do centro de administração do Windows

Você pode habilitar os servidores habilitados para Arc do Azure para um ou mais computadores Windows em seu ambiente executando um conjunto de etapas manualmente. Ou você pode usar o [centro de administração do Windows](/windows-server/manage/windows-admin-center/understand/what-is) para implantar o agente do computador conectado e registrar seus servidores locais sem precisar executar nenhuma etapa fora dessa ferramenta.

## <a name="prerequisites"></a>Pré-requisitos

* Servidores habilitados para Arc – revise os [pré-requisitos](agent-overview.md#prerequisites) e verifique se sua assinatura, sua conta do Azure e recursos atendem aos requisitos.

* Centro de administração do Windows – examine os requisitos para [preparar seu ambiente](/windows-server/manage/windows-admin-center/deploy/prepare-environment) para implantar e [Configurar a integração do Azure ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Os servidores Windows de destino que você deseja gerenciar devem ter conectividade com a Internet para acessar o Azure.

### <a name="security"></a>Segurança

Esse método de implantação requer que você tenha direitos de administrador no computador Windows de destino ou no servidor para instalar e configurar o agente. Você também precisa ser um membro da função de [**usuários de gateway**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Implantar

Execute as etapas a seguir para configurar o Windows Server com servidores habilitados para Arc.

1. Entre no centro de administração do Windows.

1. Na lista de conexões da página **visão geral** , na lista de servidores conectados do Windows, selecione um servidor na lista para se conectar a ele.

1. No painel esquerdo, selecione **Serviços híbridos do Azure**.

1. Na página **Serviços híbridos do Azure** , selecione **descobrir serviços do Azure**.

1. Na página **descobrir serviços do Azure** , em **aproveitar as políticas e soluções do Azure para gerenciar seus servidores com o Azure Arc**, selecione **Configurar**.

1. Na página **Settings\Azure Arc for Servers** , se solicitado a autenticar no Azure e, em seguida **, selecione introdução**.

1. Na página **conectar servidor ao Azure** , forneça o seguinte:

    1. Na lista suspensa **assinatura do Azure** , selecione a assinatura do Azure.
    1. Para o **grupo de recursos**, selecione **novo** para criar um novo grupo de recursos ou, na lista suspensa grupo de **recursos** , selecione um grupo de recursos existente para registrar e gerenciar o computador.
    1. Na lista suspensa **Região**, selecione a região do Azure que armazenará os metadados dos servidores.
    1. Se a máquina ou o servidor estiver se comunicando por meio de um servidor proxy para se conectar à Internet, selecione a opção **usar servidor proxy**. Especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy.

1. Selecione **Configurar** para continuar com a configuração do Windows Server com os servidores habilitados para Arc do Azure.

O Windows Server se conectará ao Azure, baixará o agente do computador conectado, o instalará e registrará com os servidores habilitados para Arc do Azure. Para acompanhar o progresso, selecione **notificações** no menu.

Para confirmar a instalação do agente do computador conectado, no centro de administração do Windows, selecione [**eventos**](/windows-server/manage/windows-admin-center/use/manage-servers#events) no painel esquerdo para examinar os eventos do *MsiInstaller* no log de eventos do aplicativo.

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Azure Arc, acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Exiba o seu computador no [portal do Azure](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Uma conexão com o computador bem-sucedida" border="false":::

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/insights/vminsights-enable-policy.md) e muito mais.

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento do sistema operacional e da carga de trabalho, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).