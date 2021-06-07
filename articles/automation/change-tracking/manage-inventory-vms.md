---
title: Gerenciar a coleta de inventário da Automação do Azure em VMs | Microsoft Docs
description: Este artigo informa como gerenciar a coleta de inventário em VMs.
services: automation
ms.subservice: change-inventory-management
keywords: inventário, automação, alteração, acompanhamento
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209374"
---
# <a name="manage-inventory-collection-from-vms"></a>Gerenciar coleção de inventário de VMs

É possível habilitar o acompanhamento de inventário para uma VM do Azure na página de recursos do computador. É possível coletar e exibir as seguintes informações de inventário em seus computadores:

- Atualizações do Windows, aplicativos do Windows, serviços, arquivos e chaves do registro
- Pacotes de software do Linux, daemons e arquivos

O Controle de Alterações e Inventário da Automação do Azure fornece uma interface do usuário baseada em navegador para definir e configurar a coleta de inventário.

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

Este artigo pressupõe que você tenha uma VM para habilitar com Controle de Alterações e Inventário. Se você não tiver uma VM do Azure, poderá [criar uma VM](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Habilitar a coleta de inventário da página de recursos da VM

1. No Portal do Azure, no painel esquerdo, selecione **Máquinas virtuais**.
2. Na lista de VMs, escolha um computador.
3. No menu **Recurso**, em **Operações**, selecione **Inventário**.
4. Escolha um workspace do Log Analytics para armazenar seus logs de dados.
    Se nenhum workspace estiver disponível para essa região, será solicitado que você crie uma conta de automação e um workspace padrão.
5. Para iniciar a habilitação do computador, escolha **Habilitar**.

   ![Exibir opções de integração](./media/manage-inventory-vms/inventory-onboarding-options.png)

    Uma barra de status avisa que o recurso Controle de Alterações e Inventário está sendo habilitado. Esse processo pode levar até 15 minutos. Durante esse período, será possível fechar a janela, ou mantê-la aberta, e receber uma notificação quando o recurso estiver habilitado. Você pode monitorar o status da implantação do painel de notificações.

   ![Exibição do Inventário](./media/manage-inventory-vms/inventory-onboarded.png)

Após a conclusão da implantação, a barra de status desaparecerá. O sistema ainda está coletando dados de inventário, e talvez os dados ainda não estejam visíveis. Uma coleta de dados completa pode demorar 24 horas.

## <a name="configure-your-inventory-settings"></a>Defina as configurações de inventário

Por padrão, software, serviços do Windows e daemons Linux são configurados para coleta. Para coletar o inventário de arquivos e Registro do Windows, defina as configurações de coleta de inventário.

1. Na página Inventário, clique em **Editar Configurações** na parte superior da página.
2. Para adicionar uma nova configuração de coleta, vá até a categoria de configuração que você deseja adicionar selecionando as guias **Registro do Windows**, **Arquivos do Windows** ou **Arquivos do Linux**.
3. Escolha a categoria apropriada e clique em **Adicionar** na parte superior da página.

As seções a seguir mostram informações sobre cada propriedade que pode ser configurada para as várias categorias.

### <a name="windows-registry"></a>Registro do Windows

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Arquivos do Windows

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.       |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo: **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Arquivos do Linux

|Propriedade  |Descrição  |
|---------|---------|
|habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **/etc/*.conf**.       |
|Tipo de caminho     | O tipo de item a ser monitorado. Os valores são Arquivo e Diretório.        |
|Recursão     | True se a recursão for usada ao procurar o item a ser rastreado e, caso contrário, False.        |
|Usar sudo     | Verdadeiro se sudo for usado ao verificar o item; caso contrário, Falso.         |
|Links     | Valor que indica como os links simbólicos são tratados ao passar diretórios. Os valores possíveis são: <br> Ignorar: ignora os links simbólicos e não inclui os arquivos/diretórios referenciados<br>Seguir: segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados<br>Gerenciar: segue os links simbólicos e permite a alteração do tratamento do conteúdo retornado      |

## <a name="manage-machine-groups"></a>Gerenciar grupos de computadores

O inventário permite que você crie e exiba grupos de computadores nos logs do Azure Monitor. Grupos de computadores são coleções de computadores definidas por uma consulta nos logs do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para exibir seus grupos de computadores, selecione a guia **Grupos de computadores** na página Inventário.

![Exibir grupos de computadores na página de inventário](./media/manage-inventory-vms/inventory-machine-groups.png)

Selecionar um grupo de computadores na lista abre a página de grupos de Computadores. Esta página mostra detalhes sobre o grupo de computadores. Esses detalhes incluem a consulta de log de Azure Monitor que é usada para definir o grupo. Na parte inferior da página, há uma lista paginada dos computadores que fazem parte do grupo.

![Página Exibir grupo de computadores](./media/manage-inventory-vms/machine-group-page.png)

Clique em **+ Clonar** para clonar o grupo de computadores. Você precisa atribuir ao grupo um novo nome e um alias. A definição pode ser alterada neste momento. Depois de mudar a consulta, clique em **Validar Consulta** para ver os computadores que seriam selecionados. Quando estiver satisfeito com o grupo, clique em **Criar** para criar o grupo de computadores.

Se quiser criar um novo grupo de computadores, clique em **+ Criar um grupo de computadores**. Esse botão abre a página **Criar um grupo de computadores**, na qual é possível definir o novo grupo. Clique em **Criar** para criar o grupo.

![Criar novo grupo de computadores](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Desconectar sua VM do gerenciamento

Para remover sua VM do gerenciamento do Controle de Alterações e Inventário:

1. No painel esquerdo do portal do Azure, escolha **Log Analytics** e o espaço de trabalho que você usou ao habilitar sua VM para Controle de Alterações e Inventário.
2. Na página **log Analytics** , abra o menu de **recursos** .
3. Escolha **Máquinas Virtuais** em **Fontes de Dados do Workspace**.
4. Na lista, escolha a VM que você quer desconectar. O computador tem uma marca de seleção verde ao lado de **Este Workspace** na coluna **Conexão OMS**.

   >[!NOTE]
   >O Microsoft Operations Management Suite (OMS) agora é conhecido como logs de Azure Monitor.

5. Na parte superior da próxima página, clique em **Desconectar**.
6. Na janela de confirmação, clique em **Sim** para desconectar o computador do gerenciamento.

>[!NOTE]
>As máquinas ainda são mostradas depois que você tiver cancelado o registro porque relatamos em todos os computadores inventariados nas últimas 24 horas. Depois de desconectar o computador, você precisa aguardar 24 horas antes que eles não sejam mais listados.

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes do trabalho com o recurso, veja [Gerenciar Controle de Alterações e Inventário](manage-change-tracking.md).
* Para saber mais sobre como controlar alterações de software, veja [Rastrear alterações de software em seu ambiente com Controle de Alterações](overview.md).
* Para solucionar problemas gerais com o recurso, veja [Solucionar problemas de Controle de Alterações e Inventário](../troubleshoot/change-tracking.md).
