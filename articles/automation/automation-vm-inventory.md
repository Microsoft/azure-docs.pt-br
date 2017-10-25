---
title: "Gerenciar uma máquina virtual do Azure com a coleta de inventário | Microsoft Docs"
description: "Gerenciar uma máquina virtual com a coleta de inventário"
services: automation
keywords: "inventário, automação, alteração, acompanhamento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerenciar uma Máquina Virtual do Azure com a coleta de inventário

Você pode habilitar o acompanhamento de inventário para uma máquina virtual do Azure na página de recursos da máquina virtual. Esse método fornece uma interface do usuário baseada em navegador para definir e configurar a coleta de inventário.

## <a name="before-you-begin"></a>Antes de começar
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).
Se você não tiver uma máquina virtual do Azure, [crie uma máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitar a coleta de inventário de página de recursos da máquina virtual

1. No Portal do Azure, no painel esquerdo, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. No menu **Recurso**, sob **Operações**, selecione **Inventário (Versão prévia)**.  
    Uma faixa é exibida na parte superior da janela, notificando que a solução não está habilitada. 
4. Para habilitar a solução, clique na faixa.
5. Selecione um espaço de trabalho do Log Analytics para armazenar seus logs de dados.  
    Se nenhum espaço de trabalho estiver disponível para essa região, será solicitado que você crie uma conta de automação e um espaço de trabalho padrão. 
6. Para iniciar a integração de seu computador, selecione **Habilitar**.

   ![Exibir opções de integração](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos. Durante esse tempo, você pode fechar a janela, ou mantê-la aberta e receber uma notificação quando a solução estiver habilitada. Você pode monitorar o status da implantação do painel de notificações.

   ![Exibir a solução de inventário imediatamente após a integração](./media/automation-vm-inventory/inventory-onboarded.png)

Após a conclusão da implantação, a barra de status desaparecerá. O sistema ainda está coletando dados de inventário, e talvez os dados ainda não estejam visíveis. Uma coleta de dados completa pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Defina as configurações de inventário

Por padrão, software, serviços do Windows e daemons Linux são configurados para coleta. Para coletar o inventário de arquivos e Registro do Windows, defina as configurações de coleta de inventário.

1. Na exibição **Inventário (Versão prévia)**, selecione o botão **Editar Configurações** na parte superior da janela.
2. Para adicionar uma nova configuração de coleta, vá até a categoria de configuração que você deseja adicionar selecionando as guias **Registro do Windows**, **Arquivos do Windows** e **Arquivos do Linux**. 
3. Selecione **Adicionar** na parte superior da janela.
4. Para exibir os detalhes e as descrições de cada propriedade de configuração, visite a [Página de documentação do inventário](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Desconectar a máquina virtual do gerenciamento

Para remover sua máquina virtual do gerenciamento de inventário:

1. No painel esquerdo do Portal do Azure, selecione **Log Analytics** e, em seguida, selecione o espaço de trabalho que você usou ao realizar a integração de sua máquina virtual.
2. Na janela **Log Analytics**, no menu **Recurso**, sob a categoria **Fontes de Dados de Espaço de Trabalho**, selecione **Máquinas virtuais**. 
3. Na lista, selecione a máquina virtual que você deseja desconectar. A máquina virtual tem uma marca de seleção verde ao lado de **Este espaço de trabalho** na coluna **Conexão OMS**. 
4. Na parte superior da página seguinte, selecione **Desconectar**.
5. Na janela de confirmação, selecione **Sim**.  
    Essa ação desconecta a máquina do gerenciamento.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gerenciamento de alterações em configurações de arquivos e do Registro em suas máquinas virtuais, consulte [Controlar alterações de software em seu ambiente com a solução Controle de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações de pacote e do Windows para as suas máquinas virtuais, consulte [A solução de Gerenciamento de Atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).
