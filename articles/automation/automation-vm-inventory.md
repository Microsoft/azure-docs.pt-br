---
title: Gerenciar uma máquina virtual do Azure com a coleta de inventário | Microsoft Docs
description: Gerenciar uma máquina virtual com a coleta de inventário
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: inventário, automação, alteração, acompanhamento
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59f36595e0b6cc8b9d9ea0669c9ecb5be1e74b42
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304061"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gerenciar uma Máquina Virtual do Azure com a coleta de inventário

Você pode habilitar o acompanhamento de inventário para uma máquina virtual do Azure na página de recursos da máquina virtual. É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Esse método fornece uma interface do usuário baseada em navegador para definir e configurar a coleta de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo pressupõe que você tem uma VM para configurar a solução. Se você não tiver uma máquina virtual do Azure, [crie uma máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitar a coleta de inventário de página de recursos da máquina virtual

1. No Portal do Azure, no painel esquerdo, selecione **Máquinas virtuais**.
2. Na lista de máquinas virtuais, selecione uma máquina virtual.
3. No menu **Recurso**, em **Operações**, selecione **Inventário**.
4. Selecione um espaço de trabalho do Log Analytics para armazenar seus logs de dados.
    Se nenhum workspace estiver disponível para essa região, será solicitado que você crie uma conta de automação e um workspace padrão.
5. Para iniciar a integração de seu computador, selecione **Habilitar**.

   ![Exibir opções de integração](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Uma barra de status o notifica de que a solução está sendo habilitada. Esse processo pode levar até 15 minutos. Durante esse período, será possível fechar a janela, ou mantê-la aberta, e receber uma notificação quando a solução estiver habilitada. Você pode monitorar o status da implantação do painel de notificações.

   ![Exibir a solução de inventário imediatamente após a integração](./media/automation-vm-inventory/inventory-onboarded.png)

Após a conclusão da implantação, a barra de status desaparecerá. O sistema ainda está coletando dados de inventário, e talvez os dados ainda não estejam visíveis. Uma coleta de dados completa pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Defina as configurações de inventário

Por padrão, software, serviços do Windows e daemons Linux são configurados para coleta. Para coletar o inventário de arquivos e Registro do Windows, defina as configurações de coleta de inventário.

1. Na exibição **Inventário**, selecione o botão **Editar Configurações** na parte superior da janela.
2. Para adicionar uma nova configuração de coleta, vá até a categoria de configuração que você deseja adicionar selecionando as guias **Registro do Windows**, **Arquivos do Windows** e **Arquivos do Linux**.
3. Selecione a categoria apropriada e clique em **Adicionar** na parte superior da janela.

As tabelas a seguir fornecem informações sobre cada propriedade que pode ser configurada para as várias categorias.

### <a name="windows-registry"></a>Registro do Windows

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Arquivos do Windows

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Insira o Caminho     | O caminho para verificar o arquivo, por exemplo: "c:\temp\myfile.txt"

### <a name="linux-files"></a>Arquivos do Linux

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Insira o Caminho     | O caminho para verificar o arquivo. Por exemplo: "/etc/*.conf"       |
|Tipo de caminho     | Tipo de item a ser rastreado; possíveis valores são: Arquivo e Diretório        |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Usar o Sudo     | Essa configuração determina se o Sudo será usado durante a verificação do item.         |
|Links     | Essa configuração determina como os links simbólicos lidam ao passar diretórios.<br> **Ignorar** - Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados<br>**Seguir** - Segue os links simbólicos durante a recursão e inclui também os arquivos/diretórios referenciados<br>**Gerenciar** - Segue os links simbólicos e permite a alteração do tratamento do conteúdo retornado      |

## <a name="manage-machine-groups"></a>Gerenciar grupos de computadores

Inventário permite que você crie e exiba grupos de computadores nos logs do Azure Monitor. Grupos de computadores são coleções de computadores definidos por uma consulta nos logs do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para exibir seus grupos de computadores, selecione a guia **Grupos de computadores** na página Inventário.

![Exibir grupos de computadores na página de inventário](./media/automation-vm-inventory/inventory-machine-groups.png)

Selecionar um grupo de computadores na lista abre a página de grupos de Computadores. Esta página mostra detalhes sobre o grupo de computadores. Esses detalhes incluem a consulta da análise de logs que é usada para definir o grupo. Na parte inferior da página, há uma lista paginada dos computadores que fazem parte do grupo.

![Página Exibir grupo de computadores](./media/automation-vm-inventory/machine-group-page.png)

Clique no botão **+ Clonar** para clonar o grupo de computadores. Aqui, você precisa atribuir ao grupo um novo nome e um alias para o grupo. A definição pode ser alterada neste momento. Depois de alterar a consulta, pressione **Validar consulta** para visualizar os computadores que seriam selecionados. Quando estiver satisfeito com o grupo, clique em **Criar** para criar o grupo de computadores

Se você quiser criar um novo grupo de computadores, selecione **+ criar um grupo de computadores**. Esse botão abre a **página Criar um grupo de computadores**, na qual é possível definir o novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de computadores](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Desconectar a máquina virtual do gerenciamento

Para remover sua máquina virtual do gerenciamento de inventário:

1. No painel esquerdo do Portal do Azure, selecione **Log Analytics** e, em seguida, selecione o workspace que você usou ao realizar a integração de sua máquina virtual.
2. Na janela **Log Analytics**, no menu **Recurso**, sob a categoria **Fontes de Dados de Workspace**, selecione **Máquinas virtuais**.
3. Na lista, selecione a máquina virtual que você deseja desconectar. A máquina virtual tem uma marca de seleção verde ao lado de **Este workspace** na coluna **Conexão OMS**.

   >[!NOTE]
   >OMS agora é chamado de logs do Azure Monitor.
   
4. Na parte superior da página seguinte, selecione **Desconectar**.
5. Na janela de confirmação, selecione **Sim**.
    Essa ação desconecta a máquina do gerenciamento.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gerenciamento de alterações em configurações de arquivos e do Registro em suas máquinas virtuais, consulte [Controlar alterações de software em seu ambiente com a solução Controle de Alterações](../log-analytics/log-analytics-change-tracking.md).
* Para saber mais sobre como gerenciar atualizações de pacote e do Windows para máquinas virtuais, consulte [A solução de Gerenciamento de Atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).

