---
title: Solucionar problemas de alterações em uma máquina virtual do Azure | Microsoft Docs
description: Use o Controle de Alterações para solucionar problemas de alterações em uma máquina virtual do Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: alteração, controle, automação
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 92f25d956bc8f1f930ae6ebbf7ee48c144bf8a30
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476853"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Solucionar problemas de alterações em seu ambiente

Neste tutorial, você aprenderá como solucionar problemas de alterações em uma máquina virtual do Azure. Habilitando o Controle de alterações, é possível controlar alterações em software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores.
Identificar essas alterações de configuração pode ajudar a detectar problemas operacionais em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do log de atividades
> * Disparar um evento
> * Exibir alterações
> * Configurar alertas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os runbooks inspetor e de ação e a tarefa do observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser carregada.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de alterações e Inventário

Primeiro, é necessário habilitar o Controle de alterações e Inventário para sua VM neste tutorial. Se você já habilitou outra solução de automação para uma VM, esta etapa não é necessária.

1. No menu à esquerda, selecione **Máquinas virtuais** e selecione uma VM na lista
1. No menu à esquerda, na seção **OPERAÇÕES**, clique em **Inventário**. A página **Controle de alterações** será aberta.

A tela ![Habilitar alteração](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) **Controle de Alterações** é aberta. Configure o local, o espaço de trabalho do Log Analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

Um workspace do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) é usado para coletar dados gerados por recursos e serviços como Inventário.
O workspace fornece um único local para examinar e analisar dados de várias fontes.

Durante a integração, a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o hybrid worker.
Esse agente é usado para comunicar-se com a VM e obter informações sobre o software instalado.

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador.
Depois que a solução é habilitada, as informações sobre o software instalado e as alterações na VM fluem para os logs do Azure Monitor.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Usando o controle de alterações no Azure Monitor logs

O controle de alterações gera dados de log que são enviados para os logs do Azure Monitor.
Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da janela **Controle de alterações**.
Os dados do Controle de alterações são armazenados com o tipo **ConfigurationChange**.
A consulta de exemplo do Log Analytics retorna todos os serviços do Windows que foram interrompidos.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre como executar e pesquisar arquivos de log nos logs do Azure Monitor, veja [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurar o Controle de alterações

O Controle de alterações oferece a capacidade de controlar alterações de configuração em sua VM. As etapas a seguir mostrarão como configurar o controle de arquivos e de chaves do Registro.

Para escolher quais arquivos e chaves do Registro coletar e rastrear, selecione **Editar configurações** na parte superior da página **Controle de alterações**.

> [!NOTE]
> O Inventário e o Controle de alterações usam as mesmas configurações de coleta, e as definições são configuradas no nível do workspace.

Na janela **Configuração do Workspace**, adicione as chaves do Registro do Windows, os arquivos do Windows ou Linux a serem controlados, conforme descrito nas próximas três seções.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registro do Windows

1. Na guia **Registro do Windows**, selecione **Adicionar**.
    A janela **Adicionar Registro do Windows para Controle de Alterações** se abre.

1. Em **Adicionar o Registro do Windows para o Controle de Alterações**, insira as informações para a chave rastrear e clique em **Salvar**

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Adicionar um arquivo do Windows

1. Na guia **Arquivos do Windows**, selecione **Adicionar**. A janela **Adicionar Arquivo do Windows para Controle de Alterações** se abre.

1. Em **Adicionar o Arquivo do Windows para o Controle de Alterações**, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
|Inserir o Caminho     | O caminho para verificar em busca do arquivo. Por exemplo: "c:\temp\\\*.txt"<br>Você também pode usar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"         |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

### <a name="add-a-linux-file"></a>Adicionar um arquivo Linux

1. Na guia **Arquivos Linux**, selecione **Adicionar**. A janela **Adicionar Arquivo Linux para Controle de Alterações** se abre.

1. Em **Adicionar o Arquivo do Linux para o Controle de Alterações**, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**

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
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

   > [!NOTE]
   > A opção "Gerenciar" links não é recomendada. Não há suporte para a recuperação de conteúdo do arquivo.

## <a name="enable-activity-log-connection"></a>Habilitar conexão do log de atividades

Na página **Controle de alterações** em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. Essa tarefa abre a página **Log de Atividades do Azure**. Selecione **Conectar** para conectar o Controle de alterações ao log de atividades do Azure para sua VM.

Com essa configuração habilitada, navegue até a página **Visão geral** para a VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Quando ele for desalocado, selecione **Iniciar** para reiniciar a VM.

Interromper e iniciar uma VM registra um evento em seu log de atividades. Navegue de volta para a página **Controle de alterações**. Selecione a guia **Eventos** na parte inferior da página. Após algum tempo, os eventos são exibidos no gráfico e na tabela. Como na etapa anterior, cada evento pode ser selecionado para exibir informações detalhadas sobre ele.

![Exibindo detalhes de alteração no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Exibir alterações

Depois que a solução Controle de alterações e Inventário estiver habilitada, será possível exibir os resultados na página **Controle de alterações**.

Na sua VM, selecione **Controle de alterações** em **OPERAÇÕES**.

![Captura de tela que mostra a lista de alterações para a VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

O gráfico mostra as alterações que ocorreram ao longo do tempo.
Depois de adicionar uma conexão do Log de Atividades, o grafo da linha na parte superior exibe eventos do Log de Atividades do Azure.
Cada linha de gráficos de barras representa um tipo de alteração controlável diferente.
Esses tipos são daemons do Linux, arquivos, chaves do Registro do Windows, software e serviços do Windows.
A guia de alteração mostra os detalhes para as alterações mostradas na visualização em ordem decrescente de hora em que a alteração ocorreu (mais recentes primeiro).
A guia **Eventos**, a tabela exibe os eventos do Log de Atividades conectados e os detalhes correspondentes com os mais recentes primeiro.

É possível ver nos resultados, que houve várias alterações no sistema, incluindo alterações em serviços e software. É possível usar os filtros na parte superior da página para filtrar os resultados por **Tipo de alteração** ou por um intervalo de tempo.

Selecione uma alteração **WindowsServices**. Isso abre a janela **Detalhes da alteração**. A janela detalhes da alteração mostra detalhes sobre a alteração e os valores antes e após a alteração. Nesse caso, o serviço de Proteção de Software foi interrompido.

![Exibindo detalhes de alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurar alertas

Pode ser útil exibir as alterações no portal do Azure, mas poder ser alertado quando uma alteração ocorrer, como um serviço interrompido, é mais útil.

Para adicionar um alerta para um serviço parado, no portal do Azure, vá para **Monitor**. E então, em **Serviços Compartilhados**, selecione **Alertas** e clique em **+ Nova regra de alerta**

Clique em **Selecionar** para escolher um recurso. Na página **Selecionar um recurso**, selecione **Log Analytics** na lista suspensa **Filtrar por tipo de recurso**. Selecione seu espaço de trabalho do Log Analytics e selecione **Concluído**.

![Selecionar um recurso](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Clique em **Adicionar condição** e, na página **Configurar lógica de sinal**, na tabela, selecione **Pesquisa de logs personalizada**. Insira a seguinte consulta na caixa de texto Consulta de pesquisa:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Essa consulta retorna os computadores que tiveram o serviço W3SVC interrompido no período de tempo especificado.

Em **Lógica de alerta**, para **Limite**, digite **0**. Quando tiver terminado, selecione **Concluído**.

![Configurar sinal lógico](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Em **Grupos de Ações**, selecione **Criar Novo**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir, dentre outras, notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

Em **Detalhes do alerta**, insira um nome e uma descrição para o alerta. Defina **Severidade** como **Informativo(Sev 2)** , **Aviso(Sev 1)** ou **Crítico(Sev 0)** .

Na caixa **Nome do grupo de ação**, digite um nome para o alerta e um nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

Em **Ações**, insira um nome para a ação, como **Administradores de Email**. Em **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**. Em **DETALHES**, selecione **Editar detalhes**.

![Adicionar grupo de ações](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

No painel **Email/SMS/Push/Voz**, digite um nome. Marque a caixa de seleção **Email** e digite um endereço de email válido. Clique em **OK** na página **Email/SMS/Push/Voz** e, em seguida, clique em **OK** na página **Adicionar grupo de ações**.

Para personalizar o assunto do email de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do email**. Quando terminar, selecione **Criar regra de alerta**. O alerta indica quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização.

A imagem a seguir é um email de exemplo recebido quando o serviço W3SVC é interrompido.

![email](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Carregar uma VM para Controle de alterações e Inventário
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do log de atividades
> * Disparar um evento
> * Exibir alterações
> * Configurar alertas

Passe para a visão geral da solução de Controle de alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Solução de gerenciamento de alterações e inventário](automation-change-tracking.md)

