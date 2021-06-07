---
title: Conectar os computadores não Azure à Central de Segurança do Azure
description: Aprender a conectar computadores não Azure à Central de Segurança
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 68fcf8a8feb046fca2c26041d92264dd8b3a638e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465490"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Conectar os computadores não Azure à Central de Segurança

A Central de Segurança pode monitorar a postura de segurança dos computadores não Azure, mas, antes, é necessário conectá-los ao Azure.

Você pode conectar seus computadores não Azure de qualquer uma das seguintes maneiras:

- Usando servidores habilitados para o Azure Arc (**recomendado**)
- Nas páginas da Central de Segurança no portal do Azure (**Introdução** e **Inventário**)

Cada uma delas é descrita nesta página.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Adicionar computadores não Azure com Azure Arc

Os servidores habilitados para o Azure Arc são a maneira mais recomendada de adicionar seus computadores que não são do Azure à Central de Segurança do Azure.

Um computador com o servidores habilitados para o Azure Arc torna-se um recurso do Azure e aparece na Central de Segurança com recomendações, assim como seus outros recursos do Azure.

Além disso, os servidores habilitados para o Azure Arc fornecem recursos aprimorados, como a opção de habilitar políticas de configuração de convidado no computador, implantar o agente do Log Analytics como extensão, simplificar a implantação com outros serviços do Azure e muito mais. Para ter uma visão geral dos benefícios, confira [Cenários com suporte](../azure-arc/servers/overview.md#supported-scenarios).

Saiba mais sobre os [servidores habilitados para o Azure Arc](../azure-arc/servers/overview.md).

**Para implantar o Azure Arc:**

- Em um computador, siga as instruções em [Início Rápido: Conectar computadores híbridos com servidores habilitados para Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Para conectar vários computadores em escala a servidores habilitados para o Arc, confira [Conectar computadores híbridos ao Azure em escala](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Se você estiver integrando computadores em execução na AWS, o conector da Central de Segurança para AWS lidará com a implantação do Azure Arc para você de maneira transparente. Saiba mais em [Conectar as contas do AWS à Central de Segurança do Azure](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Adicionar computadores não Azure do portal do Azure

1. No menu da Central de Segurança, abra a página **Introdução**.
1. Selecione a guia **Introdução**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Guia de introdução na página Introdução" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Abaixo de **Adicionar servidores não Azure**, selecione **Configurar**.

    > [!TIP]
    > Você também pode abrir a seção Adicionar computadores por meio da página **Estoque**, pressionando o botão **Adicionar servidores não Azure**.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Adição de computadores não Azure pela página de inventário de ativos":::

    É mostrada uma lista dos workspaces do Log Analytics. A lista inclui, se aplicável, o workspace padrão criado para você pela Central de Segurança quando o provisionamento automático foi habilitado. Selecione esse workspace ou outro que você queira usar.

    Você pode adicionar computadores a um workspace existente ou criar um novo workspace.

1. Opcionalmente, para criar um workspace, selecione **Criar workspace**.

1. Na lista de workspaces, selecione **Adicionar servidores** para o workspace relevante.
    A página **Gerenciamento de agentes** é exibida.

    Neste ponto, escolha o procedimento relevante abaixo, com base no tipo de computador que você está integrando:

    - [Integrar as VMs do Azure Stack Hub](#onboard-your-azure-stack-hub-vms)
    - [Integrar os computadores Linux](#onboard-your-linux-machines)
    - [Integrar os computadores Windows](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Integrar as VMs do Azure Stack Hub

Para adicionar as VMs do Azure Stack Hub, você precisará de informações da página **Gerenciamento de agentes** e configurar a extensão da máquina virtual **Azure Monitor, atualização e gerenciamento de configuração** nas máquinas virtuais em execução na instância do Azure Stack Hub.

1. Na página **Gerenciamento de agentes**, copie a **ID do Workspace** e a **Chave Primária** no Bloco de notas.
1. Faça logon no portal do **Azure Stack Hub** e abra a página **Máquinas virtuais**.
1. Selecione a máquina virtual que você deseja proteger com a Central de Segurança.
    >[!TIP]
    > Para saber mais sobre como criar uma máquina virtual no Azure Stack Hub, confira [este início rápido para Máquinas Virtuais do Windows](/azure-stack/user/azure-stack-quick-windows-portal) ou [este início rápido para Máquinas Virtuais do Linux](/azure-stack/user/azure-stack-quick-linux-portal).
1. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.
1. Selecione a guia **Adicionar**. O menu **Novo Recurso** mostra a lista de extensões da máquina virtual disponíveis.
1. Selecione a extensão **Azure Monitor, atualização e gerenciamento de configuração** e selecione **Criar**. A página de configuração **Instalar extensão** é aberta.
    >[!NOTE]
    > Caso você não veja a extensão **Gerenciamento de Configuração, de Atualização e do Azure Monitor** listada no marketplace, contate seu operador do Azure Stack Hub para disponibilizá-lo.
1. Na página de configuração **Instalar extensão**, cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou para o Bloco de Notas na etapa anterior.
1. Ao concluir a configuração, selecione **OK**. O status da extensão será exibido como **Provisionamento bem-sucedido**. Pode levar até uma hora para que a máquina virtual seja exibida na Central de Segurança.

### <a name="onboard-your-linux-machines"></a>Integrar os computadores Linux

Para adicionar computadores Linux, você precisa do comando WGET na página **Gerenciamento de agentes**.

1. Na página **Gerenciamento de agentes**, copie o comando **WGET** no bloco de notas. Salve esse arquivo em um local que possa ser acessado pelo computador Linux.
1. No computador Linux, abra o arquivo com o comando WGET. Selecione o conteúdo todo, copie e cole em um console do terminal.
1. Quando a instalação for concluída, você poderá confirmar se o *omsagent* está instalado executando o comando *pgrep*. O comando retornará a PID do *omsagent*.
    Os logs do agente podem ser encontrados em: */var/opt/microsoft/omsagent/\<workspace id>/log/* Pode levar até 30 minutos para que o novo computador Linux apareça na Central de Segurança.

### <a name="onboard-your-windows-machines"></a>Integrar os computadores Windows

Para adicionar computadores Windows, você precisará das informações contidas na página **Gerenciamento de agentes** e baixar o arquivo de agente apropriado (32/64 bits).
1. Selecione o link **Baixar Agente do Windows** aplicável a seu tipo de processador do computador para baixar o arquivo de configuração.
1. Na página **Gerenciamento de agentes**, copie a **ID do Workspace** e a **Chave Primária** no Bloco de notas.
1. Copie o arquivo de instalação baixado para o computador de destino e execute-o.
1. Siga o assistente de instalação (**Próxima**, **Eu concordo**, **Próximo**, **Próximo**).
    1. Na página **Azure Log Analytics**, cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou para o Bloco de Notas.
    1. Caso o computador deva se reportar a um workspace do Log Analytics na nuvem do Azure Government, selecione **Azure US Government** na lista suspensa da **Nuvem do Azure**.
    1. Caso o computador precise se comunicar por meio de um servidor proxy ao serviço Log Analytics, selecione **Avançado** e forneça a URL e o número da porta do servidor proxy.
    1. Quando você tiver inserido todas as definições de configuração, selecione **Próximo**.
    1. Na página **Pronto para Instalar**, examine as configurações a serem aplicadas e selecione **Instalar**.
    1. Na página **Configuração concluída com êxito**, selecione **Concluir**.

Após a conclusão, o **Microsoft Monitoring Agent** aparecerá no **Painel de Controle**. Você pode revisar sua configuração e verificar se o agente está conectado.

Para obter mais informações sobre como instalar e configurar o agente, confira [Conectar computadores Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Verificando

Parabéns! Agora você pode ver seus computadores Azure e não Azure juntos, em um só lugar. Abra a [página Estoque de ativos](asset-inventory.md) e filtre pelos tipos de recursos relevantes. Estes ícones distinguem os tipos:

  ![Ícone ASC para computador não Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computador não Azure

  ![Ícone ASC para computador Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure

  ![Ícone ASC para o servidor do Azure Arc](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Servidor habilitado para o Azure Arc

## <a name="next-steps"></a>Próximas etapas

Essa página mostrou como adicionar seus computadores não Azure à Central de Segurança do Azure. Para monitorar o status, use as ferramentas de estoque, conforme explicado na seguinte página:

- [Explore e gerencie seus recursos usando um inventário de ativos](asset-inventory.md)