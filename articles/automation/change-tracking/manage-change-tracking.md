---
title: Controle de Alterações e Inventário na Automação do Azure
description: Este artigo informa como usar o Controle de Alterações e Inventário para controlar as alterações de software e de serviço da Microsoft em seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: 636dbf95567f761aee19bd567b0835173ce36ccc
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093614"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerenciar Controle de Alterações e Inventário

Quando você adiciona um novo arquivo ou chave do registro para acompanhar, a automação do Azure permite que ele [controle de alterações e inventário](overview.md). Este artigo descreve como configurar o controle, examinar os resultados do controle e lidar com alertas quando alterações forem detectadas.

Antes de usar os procedimentos neste artigo, verifique se você habilitou Controle de Alterações e inventário em suas VMs usando uma destas técnicas:

* [Habilitar Controle de Alterações e Inventário de uma conta de Automação](enable-from-automation-account.md)
* [Habilite Controle de Alterações e inventário navegando na portal do Azure](enable-from-portal.md)
* [Habilitar Controle de Alterações e Inventário de um runbook](enable-from-runbook.md)
* [Habilitar Controle de Alterações e Inventário de uma VM do Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o escopo para a implantação

O Controle de Alterações e Inventário usa uma configuração de escopo dentro do workspace a fim de definir os computadores que receberão alterações. Para obter mais informações, consulte [limitar controle de alterações e escopo de implantação de estoque](manage-scope-configurations.md).

## <a name="track-files"></a>Controlar arquivos

Você pode usar Controle de Alterações e inventário para controlar alterações em arquivos e pastas/diretórios. Esta seção informa como configurar o rastreamento de arquivos no Windows e no Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurar o controle de arquivos no Windows

Use as etapas a seguir para configurar o controle de arquivos em computadores Windows:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Automação**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione **Contas de Automação**.

3. Na lista de contas de automação, selecione a conta que você escolheu quando habilitou Controle de Alterações e inventário.

4. Na sua Conta de Automação, selecione **Controle de alterações** em **Gerenciamento de configuração**.

5. Selecione **Editar configurações** (o símbolo de engrenagem).

6. Na página Configuração do Workspace, selecione **Arquivos do Windows** e clique em **+ Adicionar** para adicionar um novo arquivo a ser controlado.

7. No painel Adicionar arquivo do Windows para Controle de Alterações, insira as informações do arquivo ou da pasta a ser rastreada e clique em **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **c:\temp\\\*. txt**. Você também pode usar variáveis de ambiente, como `%winDir%\System32\\\*.*`.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são arquivo e pasta.        |    
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado e, caso contrário, False.        |    
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas e, caso contrário, False.|

    Se você planeja configurar o monitoramento de arquivos e pastas usando curingas, considere o seguinte:

    - Caracteres curinga são necessários para acompanhar vários arquivos.
    - Os curingas só podem ser usados no último segmento de um caminho, como *C:\folder\file* ou */etc/*. conf *
    - Se uma variável de ambiente incluir um caminho que não é válido, a validação terá êxito, mas o caminho falhará quando o inventário for executado.
    - Ao definir o caminho, evite caminhos gerais como *c:*. * *, que resultará em muitas pastas sendo percorridas.

8. Certifique-se de especificar True para **Carregar o conteúdo do arquivo**. Essa configuração habilita o controle de conteúdo do arquivo para o caminho de arquivo indicado.

### <a name="configure-file-tracking-on-linux"></a>Configurar o controle de arquivos no Linux

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Selecione **Editar configurações** (o símbolo de engrenagem).

2. Na página configuração do espaço de trabalho, selecione **arquivos do Linux** e, em seguida, selecione **+ Adicionar** para adicionar um novo arquivo a ser acompanhado.

3. Na página **Adicionar arquivo do Linux para controle de alterações** , insira as informações para o arquivo ou diretório a ser acompanhado e, em seguida, selecione **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se a configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
    |Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
    |Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo, **/etc/*.conf**.       |
    |Tipo de caminho     | O tipo de caminho. Os valores possíveis são Arquivo e Diretório.        |
    |Recursão     | True se a recursão for usada ao procurar o item a ser rastreado e, caso contrário, False.        |
    |Usar o Sudo     | True para usar sudo ao verificar o item e, caso contrário, False.         |
    |Links     | Essa configuração determina como os links simbólicos lidam ao percorrer diretórios. Os valores possíveis são:<br> Ignorar – Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>Seguir – Segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>Gerenciar-segue os links simbólicos e permite a alteração de conteúdo retornado.<br>**Observação:** A opção Manage não é recomendada, pois não dá suporte à recuperação de conteúdo do arquivo.    |
    |Carregar conteúdo do arquivo | True para carregar o conteúdo do arquivo em alterações controladas e, caso contrário, False. |

4. Certifique-se de especificar **true** para **carregar o conteúdo do arquivo**. Essa configuração habilita o controle de conteúdo do arquivo para o caminho de arquivo indicado.

   ![Adicionar arquivo Linux](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Controlar o conteúdo do arquivo

O controle de conteúdo de arquivo permite que você exiba o conteúdo de um arquivo antes e depois de uma alteração controlada. O recurso salva o conteúdo do arquivo em uma [conta de armazenamento](../../storage/common/storage-account-overview.md) após a ocorrência de cada alteração. Aqui estão algumas regras a serem seguidas para controlar o conteúdo do arquivo:

* Uma conta de armazenamento Standard usando o modelo de implantação do Resource Manager é necessária para armazenar o conteúdo do arquivo.
* Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Se você protegeu sua conta de armazenamento para permitir apenas determinado tráfego, precisará modificar suas regras de configuração para permitir que sua conta de automação se conecte a ela. Consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md).
* Não use contas de armazenamento do modelo de implantação Premium e Clássico. Consulte [Sobre as contas de Armazenamento do Azure](../../storage/common/storage-account-create.md).
* Você pode conectar a conta de armazenamento a apenas uma conta de automação.
* Controle de Alterações e inventário devem ser habilitados em sua conta de automação.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitar o controle de alterações de conteúdo de arquivo

Use as etapas a seguir para habilitar o rastreamento de alterações no conteúdo do arquivo:

1. Selecione **Editar configurações** (o símbolo de engrenagem).

2. Selecione **conteúdo do arquivo** e, em seguida, selecione **vincular**. Essa seleção abre a página **Adicionar local de conteúdo para controle de alterações** .

   ![Adicionar local de conteúdo](./media/manage-change-tracking/enable.png)

3. Selecione a assinatura e a conta de armazenamento a serem usadas para armazenar o conteúdo do arquivo.

5. Se você deseja habilitar o controle de conteúdo de arquivo para todos os arquivos controlados existentes, selecione **Habilitado** para **Carregar conteúdo do arquivo para todas as configurações**. Você pode alterar essa configuração para cada caminho de arquivo mais tarde.

   ![Definir a conta de armazenamento](./media/manage-change-tracking/storage-account.png)

6. O Controle de Alterações e Inventário mostra os URIs de conta de armazenamento e a SAS (Assinatura de Acesso Compartilhado) quando ele habilita o controle de alterações de conteúdo de arquivo. As assinaturas expiram após 365 dias e você pode recriá-las selecionando **regenerar**.

   ![Listar chaves de conta](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Exibir o conteúdo de um arquivo rastreado

Quando o Controle de Alterações e o inventário detectar uma alteração de um arquivo rastreado, você pode exibir o conteúdo do arquivo no painel Detalhes da Alteração.  

![Listar alterações](./media/manage-change-tracking/change-list.png)

1. Na página **controle de alterações** da sua conta de automação, escolha um arquivo na lista de alterações e, em seguida, selecione **Exibir alterações de conteúdo do arquivo** para ver o conteúdo do arquivo. O painel alterar detalhes mostra as informações padrão antes e depois do arquivo para cada propriedade.

   ![Detalhes da alteração](./media/manage-change-tracking/change-details.png)

2. Você está exibindo o conteúdo do arquivo em uma exibição lado a lado. Você pode selecionar **Embutido** para ver uma exibição embutida das alterações.

## <a name="track-registry-keys"></a>Rastrear chaves do registro

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Na página **controle de alterações** da sua conta de automação, selecione **Editar configurações** (o símbolo de engrenagem).

2. Na página Configuração do Workspace, selecione **Registro do Windows**.

3. Selecione **+ Adicionar** para adicionar uma nova chave do registro a ser controlada.

4. Na página **adicionar registro do Windows para controle de alterações** , insira as informações da chave a ser controlada e, em seguida, selecione **salvar**. A tabela a seguir define as propriedades que você pode usar para as informações. Ao especificar um caminho de registro, ele deve ser a chave e não um valor.

    |Propriedade  |Descrição  |
    |---------|---------|
    |habilitado     | True se uma configuração for aplicada e, caso contrário, False.        |
    |Nome do Item     | Nome amigável da chave de registro a ser rastreada.        |
    |Agrupar     | Um nome de grupo para agrupar chaves de registro logicamente.        |
    |Chave de Registro do Windows   | Nome da chave com caminho, por exemplo, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Pesquisar registros de alteração nos logs

Você pode fazer várias pesquisas por registros de alteração em relação aos logs do Azure Monitor. Com a página controle de alterações aberta, clique em **Log Analytics** para abrir a página logs. A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações.

|Consulta  |Descrição  |
|---------|---------|
|`ConfigurationData`<br> &#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br> &#124; `where SvcState == "Stopped"`<br> &#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra os registros de inventário mais recentes para os serviços da Microsoft que foram configurados para Automático, mas foram relatados como Parados. Os resultados são limitados ao registro mais recente do nome de software e do computador especificados.    |
|`ConfigurationChange`<br> &#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br> &#124; `order by TimeGenerated desc`|Mostra registros de alterações do software removido.|

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre configurações de escopo, consulte [limitar controle de alterações e escopo de implantação de estoque](manage-scope-configurations.md).
* Se você precisar pesquisar logs armazenados em logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../../azure-monitor/log-query/log-query-overview.md).
* Se terminar com implantações, consulte [remover controle de alterações e inventário](remove-feature.md).
* Para excluir suas VMs de Controle de Alterações e inventário, consulte [remover VMs de controle de alterações e inventário](remove-vms-from-change-tracking.md).
* Para solucionar problemas de erros de recurso, confira [Solucionar problemas do Controle de Alterações e Inventário](../troubleshoot/change-tracking.md).
