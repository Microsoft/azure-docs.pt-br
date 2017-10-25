---
title: "Controle as alterações nas suas máquinas virtuais do Azure | Microsoft Docs"
description: "Use o controle de alterações para controlar alterações em arquivos e registro nas suas máquinas virtuais."
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Controle as alterações nas suas máquinas virtuais do Azure

Ao habilitar o controle de alterações, você pode controlar as alterações a arquivos e chaves de registro do Windows nas suas máquinas virtuais. Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

Você pode habilitar o controle de alterações diretamente da sua máquina virtual do Azure.

Se não tiver uma máquina virtual do Azure, você poderá criar uma seguindo as instruções no arquivo [Início rápido do Windows](../virtual-machines/windows/quick-create-portal.md) ou [Início rápido do Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Habilitar o controle de alterações para uma máquina virtual do Azure

1. No Portal do Azure, no painel esquerdo, selecione **Máquinas virtuais**.
2. Na lista, selecione uma máquina virtual.
3. Na janela da máquina virtual, em **Operações**, selecione **Controle de alterações**. 

   ![VM de integração de controle de alterações](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    A janela **Habilitar Gerenciamento de Atualizações** é aberta.

    É feita uma validação para determinar se o controle de alterações está habilitado para essa máquina virtual. Se o controle de alterações não estiver habilitado, uma faixa será exibida dando a opção de habilitar a solução.

   ![Faixa de configuração integrada do controle de alterações](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Para habilitar a solução, clique na faixa. Se você não tiver os itens a seguir, eles serão adicionados automaticamente:

   * Espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md)
   * Conta de [automação](../automation/automation-offering-get-started.md)

5. Selecione um espaço de trabalho do Log Analytics para armazenar logs de dados de controle de alterações, selecione uma conta de Automação para controlar as alterações e, em seguida, selecione **Habilitar**.  
    Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos.

## <a name="configure-change-tracking"></a>Configurar o controle de alterações

Depois que o controle de alterações estiver habilitado, a janela **Controle de alterações** será exibida. 

Para escolher quais arquivos e chaves de registro controlar, selecione **Editar configurações**.

   ![Editar configurações do controle de alterações](./media/automation-vm-change-tracking/change-edit-settings.png)

   A janela **Configuração do Espaço de Trabalho** se abre. 

Na janela **Configuração do Espaço de Trabalho**, adicione as chaves de registro do Windows, os arquivos do Windows ou Linux a serem acompanhados, conforme descrito nas próximas três seções.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registro do Windows

1. Na guia **Registro do Windows**, selecione **Adicionar**.  
    A janela **Adicionar Registro do Windows para Controle de Alterações** se abre.

   ![Adicionar registro do controle de alterações](./media/automation-vm-change-tracking/change-add-registry.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Chave do Registro do Windows**, insira o nome da chave do registro que você deseja controlar.
6. Selecione **Salvar**.

### <a name="add-a-windows-file"></a>Adicionar um arquivo do Windows

1. Na guia **Arquivos do Windows**, selecione **Adicionar**.  
    A janela **Adicionar Arquivo do Windows para Controle de Alterações** se abre.

   ![Adicionar arquivo do Windows do controle de alterações](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Inserir Caminho**, insira o caminho completo e o nome do arquivo que você deseja rastrear.
6. Selecione **Salvar**.

### <a name="add-a-linux-file"></a>Adicionar um arquivo Linux

1. Na guia **Arquivos Linux**, selecione **Adicionar**.  
    A janela **Adicionar Arquivo Linux para Controle de Alterações** se abre.

   ![Adicionar arquivo do Linux do controle de alterações](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Em **Habilitado**, selecione **True**.
3. Na caixa **Nome do Item**, insira um nome amigável.
4. (Opcional) Na caixa **Grupo**, digite um nome de grupo.
5. Na caixa **Inserir Caminho**, insira o caminho completo e o nome do arquivo que você deseja rastrear.
6. Na caixa **Tipo de Caminho**, selecione **Arquivo** ou **Diretório**.
7. Em **Recursão**, selecione **Ativado** para controlar alterações para o caminho especificado e todos os arquivos e caminhos sob ele. Para controlar somente o caminho ou o arquivo selecionado, selecione **Desligado**.
8. Em **Use o Sudo**, para acompanhar arquivos que precisam do comando `sudo` para serem acessados, selecione **Ativado**. Caso contrário, selecione **Desligado**.
9. Selecione **Salvar**.

## <a name="view-changes"></a>Exibir alterações

Na janela **Controle de alterações**, você pode exibir as alterações em cada uma das várias categorias para a sua máquina virtual ao longo do tempo.

   ![Adicionar alterações de exibição do controle de alterações](./media/automation-vm-change-tracking/change-view-changes.png)

Você pode selecionar as categorias e o intervalo de tempo das alterações a exibir. Na parte superior da janela, você pode exibir uma representação gráfica das alterações ao longo do tempo. Na parte inferior da janela, você exibirá uma lista das alterações recentes.

## <a name="view-change-tracking-log-data"></a>Exibir dados de log do controle de alterações

O controle de alterações gera dados de log que são enviados para o Log Analytics. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da janela **Controle de alterações**.

   ![Log Analytics do controle de alterações](./media/automation-vm-change-tracking/change-log-analytics.png)

Para saber mais sobre como executar e pesquisar arquivos de log no Log Analytics, consulte [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o controle de alterações, consulte [Controlar alterações de software no ambiente com a solução Controle de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações para suas máquinas virtuais, consulte [Solução Gerenciamento de Atualizações no OMS](../operations-management-suite/oms-solution-update-management.md).
