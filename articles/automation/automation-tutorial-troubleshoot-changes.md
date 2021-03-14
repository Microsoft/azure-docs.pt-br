---
title: Solucionar problemas de alterações em uma VM do Azure na Automação do Azure | Microsoft Docs
description: Este artigo informa como solucionar problemas com alterações em uma VM do Azure.
services: automation
ms.subservice: change-inventory-management
keywords: alteração, controle, controle de alterações, inventário, automação
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a223ac4296dd160bbdd904e1d3443552d4f49a23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587938"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Solucionar problemas com alterações em uma VM do Azure

Neste tutorial, você aprenderá como solucionar problemas de alterações em uma máquina virtual do Azure. Habilitando o Controle de Alterações e o Inventário, é possível controlar alterações em software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores.
Identificar essas alterações de configuração pode ajudar a detectar problemas operacionais em seu ambiente.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Habilitar o Controle de Alterações e Inventário para uma VM
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do log de atividades
> * Disparar um evento
> * Exibir alterações
> * Configurar alertas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [Conta de Automação](./index.yml) para manter os runbooks inspetor e de ação e a tarefa do Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) a ser habilitada para o recurso.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

Primeiro, é necessário habilitar o Controle de Alterações e Inventário para este tutorial. Se você já habilitou o recurso anteriormente, esta etapa não é necessária.

>[!NOTE]
>Caso os campos estejam esmaecidos, outra solução de Automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

1. Selecione **Máquinas virtuais** e selecione uma VM na lista.
2. No menu esquerdo, selecione **Inventário** em **Operações**. A página Inventário se abre.

    ![Habilitar alteração](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Escolha o workspace do [Log Analytics](../azure-monitor/logs/log-query-overview.md). Esse workspace coleta dados gerados por recursos como o Controle de Alterações e o Inventário. O workspace fornece um único local para examinar e analisar dados de várias fontes.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Selecione a conta de Automação a ser usada.

5. Configure o local da implantação.

5. Clique em **Habilitar** para implantar o recurso para sua VM. 

Durante a instalação, a VM é provisionada com o agente do Log Analytics para Windows e um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Pode levar até 15 minutos para habilitação o Controle de Alterações e o Inventário. Durante esse tempo, não feche a janela do navegador.

Depois que o recurso é habilitado, as informações sobre o software instalado e as alterações na VM fluem para os logs do Azure Monitor.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Usar o Controle de Alterações e o Inventário em logs do Azure Monitor

O Controle de Alterações e Inventário gera dados de log que são enviados para os logs do Azure Monitor. Para pesquisar os logs executando consultas, selecione **Log Analytics** na parte superior da página Controle de alterações. Os dados do Controle de alterações são armazenados com o tipo `ConfigurationChange`.

A consulta de exemplo do Log Analytics retorna todos os serviços do Windows que foram interrompidos.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre como executar e pesquisar arquivos de log nos logs do Azure Monitor, veja [logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurar o controle de alterações

Com o controle de alterações, você escolhe quais arquivos e chaves do Registro coletar e rastrear usando **Editar configurações** na parte superior da página Controle de alterações em sua VM. Você pode adicionar chaves do Registro do Windows, arquivos do Windows ou arquivos do Linux para rastrear na página de Configuração do Workspace.

> [!NOTE]
> O inventário e o controle de alterações usam as mesmas configurações de coleta, e as definições são configuradas no nível do workspace.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registro do Windows

1. Na guia **Registro do Windows**, selecione **Adicionar**. 

1. Na página Adicionar Registro do Windows para Controle de Alterações, insira as informações para a chave rastrear e clique em **Salvar**

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | Determina se a configuração é aplicada        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
    |Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Adicionar um arquivo do Windows

1. Na guia **Arquivos do Windows**, selecione **Adicionar**. 

1. Na página Adicionar o Arquivo do Windows para o Controle de Alterações, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | Determina se a configuração é aplicada        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
    |Inserir o Caminho     | O caminho para verificar em busca do arquivo. Por exemplo: "c:\temp\\\*.txt"<br>Você também pode usar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"         |
    |Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
    |Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **True** ou **False**.|

### <a name="add-a-linux-file"></a>Adicionar um arquivo Linux

1. Na guia **Arquivos Linux**, selecione **Adicionar**. 

1. Na página Adicionar o Arquivo do Linux para o Controle de Alterações, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | Determina se a configuração é aplicada        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos        |
    |Inserir o Caminho     | O caminho para verificar o arquivo. Por exemplo: "/etc/*.conf"       |
    |Tipo de caminho     | Tipo de item a ser rastreado; possíveis valores são: Arquivo e Diretório        |
    |Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
    |Usar o Sudo     | Essa configuração determina se o Sudo será usado durante a verificação do item.         |
    |Links     | Essa configuração determina como os links simbólicos lidam ao passar diretórios.<br> **Ignorar** - Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados<br>**Seguir** - Segue os links simbólicos durante a recursão e inclui também os arquivos/diretórios referenciados<br>**Gerenciar** - Segue os links simbólicos e permite a alteração do tratamento do conteúdo retornado      |
    |Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: Verdadeiro ou falso.|

   > [!NOTE]
   > O valor **Gerenciar** para a propriedade **Links** não é recomendado. Não há suporte para a recuperação de conteúdo do arquivo.

## <a name="enable-activity-log-connection"></a>Habilitar conexão do log de atividades

1. Na página Controle de alterações em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. 

2. Na página do Log de atividades do Azure, clique em **Conectar** para conectar o Controle de Alterações e Inventário ao log de atividades do Azure para a VM.

3. Navegue até a página Visão geral para a VM e selecione **Parar** a fim de interromper a VM. 

4. Quando solicitado, selecione **Sim** para interromper a VM. 

5. Quando que a VM for desalocada, selecione **Iniciar** para reiniciá-la. Interromper e iniciar uma VM registra um evento em seu Log de Atividades. 

## <a name="view-changes"></a>Exibir alterações

1. Navegue de volta à página do Controle de alterações e selecione a guia **Eventos** na parte inferior do painel. 

2. Após algum tempo, os eventos do controle de alterações são exibidos no gráfico e na tabela. O gráfico mostra as alterações que ocorreram ao longo do tempo. O grafo de linha na parte superior exibe eventos do Log de Atividades do Azure. Cada linha de grafos de barras representa um tipo de alteração controlável diferente. Esses tipos são daemons do Linux, arquivos, chaves do Registro do Windows, software e serviços Windows. A guia de alterações mostra os detalhes das alterações exibidas, com a alteração mais recente exibida primeiro.

    ![Exibir eventos no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Observe que houve várias alterações no sistema, incluindo alterações em serviços e software. É possível usar os filtros na parte superior da página para filtrar os resultados por **Tipo de alteração** ou por um intervalo de tempo.

    ![Lista de alterações na VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Selecione uma alteração **WindowsServices**. Selecioná-la fará com que a página Detalhes da Alteração seja aberta, mostrando detalhes sobre a alteração e os valores antes e após a alteração. Nesse caso, o serviço de Proteção de Software foi interrompido.

    ![Exibindo detalhes de alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurar alertas

Pode ser útil exibir as alterações no portal do Azure, mas poder ser alertado quando uma alteração ocorrer, como um serviço interrompido, é mais útil. Vamos adicionar um alerta para um serviço interrompido. 

1. No portal do Azure, vá para **Monitor**. 

2. Em **Serviços Compartilhados**, selecione **Alertas** e clique em **+ Nova regra de alerta**.

3. Clique em **Selecionar** para escolher um recurso. 

4. Na página Selecionar um recurso, escolha **Log Analytics** no menu suspenso **Filtrar por tipo de recurso**. 

5. Selecione seu workspace do Log Analytics e clique em **Concluído**.

    ![Selecionar um recurso](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Clique em **Adicionar condição**.

7. Na tabela na página Configurar lógica de sinal, selecione **Pesquisa de logs personalizada**. 

8. Insira a seguinte consulta na caixa de texto Consulta de pesquisa:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Essa consulta retorna os computadores que tiveram o serviço W3SVC interrompido no período de tempo especificado.

9. Para o **Limite**, em **Lógica de alerta**, digite **0**. Quando tiver terminado, clique em **Concluído**.

    ![Configurar sinal lógico](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Selecione **Criar** em **Grupos de Ações**. Um grupo de ação é um grupo de ações que você pode usar através de vários alertas. As ações podem incluir, dentre outras, notificações email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/alerts/action-groups.md).

11. Em **Detalhes do alerta**, insira um nome e uma descrição para o alerta. 

12. Defina **Severidade** como **Informativo(Sev 2)** , **Aviso(Sev 1)** ou **Crítico(Sev 0)** .

13. Na caixa **Nome do grupo de ação**, digite um nome para o alerta e um nome curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

14. Para **Ações**, insira um nome para a ação, como **Administradores de Email**. 

15. Para **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**. 

16. Para **DETALHES**, selecione **Editar detalhes**.

    ![Adicionar grupo de ações](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. No Email/SMS/Push/Voz, insira um nome, marque a caixa de seleção **Email** e insira um endereço de email válido. Quando terminar, clique em **OK** no painel e depois clique em **OK** na página Adicionar grupo de ações.

18. Para personalizar o assunto do email de alerta, selecione **Personalizar Ações**. 

19. Para **Criar regra**, selecione **Assunto do email** e escolha **Criar regra de alerta**. O alerta indica quando uma implantação de atualização for bem-sucedida e quais computadores fazem parte da execução de implantação de atualização. A imagem a seguir é um exemplo de email recebido quando o serviço W3SVC é interrompido.

    ![A captura de tela mostra uma notificação recebida por email quando os serviços W3SVC são interrompidos.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Habilitar o Controle de Alterações e Inventário para uma VM
> * Pesquisar serviços interrompidos nos logs de alterações
> * Configurar o controle de alterações
> * Habilitar conexão do Log de Atividades
> * Disparar um evento
> * Exibir alterações
> * Configurar alertas

Passe para a visão geral do recurso de Controle de Alterações e Inventário para saber mais sobre ela.

> [!div class="nextstepaction"]
> [Visão geral do Controle de Alterações e Inventário](change-tracking/overview.md)
