---
title: Monitoramento de integridade de arquivo na central de segurança do Azure | Microsoft Docs
description: Saiba como configurar o FIM (monitoramento de integridade de arquivo) na central de segurança do Azure usando este passo a passos.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 834df2387d037c19672e3287970300f4e84ca34b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047976"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoramento de integridade de arquivo na central de segurança do Azure
Saiba como configurar o FIM (monitoramento de integridade de arquivo) na central de segurança do Azure usando este passo a passos.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|Requer o [Azure defender para servidores](defender-for-servers-introduction.md).<br>O FIM carrega dados no espaço de trabalho do Log Analytics. Encargos de dados se aplicam, com base na quantidade de dados que você carregar. Consulte [Preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.|
|Funções e permissões necessárias:|O **proprietário do espaço de trabalho** pode habilitar/desabilitar o fim (para obter mais informações, consulte [funções do Azure para log Analytics](/services-hub/health/azure-roles#azure-roles)).<br>O **leitor** pode exibir os resultados.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim ](./media/icons/yes-icon.png) nacional/soberanas (US gov, China gov, outros gov)<br>Com suporte apenas em regiões em que a solução de controle de alterações da automação do Azure está disponível.<br>![Sim, ](./media/icons/yes-icon.png) dispositivos habilitados para [Arc do Azure](../azure-arc/servers/overview.md) .<br>Consulte [regiões com suporte para o espaço de trabalho log Analytics vinculado](../automation/how-to/region-mappings.md).<br>[Saiba mais sobre o controle de alterações](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-security-center"></a>O que é FIM na Central de Segurança?
O FIM (monitoramento de integridade de arquivo), também conhecido como monitoramento de alterações, examina arquivos do sistema operacional, registros do Windows, software de aplicativo, arquivos do sistema Linux e muito mais, para alterações que possam indicar um ataque. 

A central de segurança recomenda as entidades para monitorar com o FIM e você também pode definir suas próprias políticas ou entidades do FIM para monitorar. O FIM alerta você sobre atividades suspeitas, como:

- Criação ou remoção de chave de arquivo e registro
- Modificações de arquivo (alterações de tamanho do arquivo, listas de controle de acesso e hash do conteúdo)
- Modificações de registro (alterações de tamanho, listas de controle de acesso, tipo e conteúdo)

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Examine a lista de entidades sugeridas para monitorar com o FIM
> * Definir suas próprias regras personalizadas do FIM
> * Auditar alterações em suas entidades monitoradas
> * Usar curingas para simplificar o rastreamento em diretórios


## <a name="how-does-fim-work"></a>Como funciona o FIM?

Ao comparar o estado atual desses itens com o estado durante a verificação anterior, o FIM alertará se forem feitas modificações suspeitas.

O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Quando o monitoramento de integridade de arquivo estiver habilitado, você terá um recurso de **controle de alterações** do tipo **solução**. Para obter detalhes de frequência de coleta de dados, consulte [controle de alterações detalhes da coleta de dados](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Se você remover o recurso de **controle de alterações** , desabilitará também o recurso de monitoramento de integridade de arquivo na central de segurança.

## <a name="which-files-should-i-monitor"></a>Quais arquivos devo monitorar?
Ao escolher quais arquivos monitorar, considere quais arquivos são essenciais para seu sistema e aplicativos. Monitore arquivos que você não espera alterar sem planejamento. Se você escolher arquivos que são frequentemente alterados por aplicativos ou sistema operacional (como arquivos de log e arquivos de texto), ele criará muitos ruídos, dificultando a identificação de um ataque.

A central de segurança fornece a seguinte lista de itens recomendados para monitorar com base em padrões de ataque conhecidos.

|Arquivos do Linux|Arquivos do Windows|Chaves do registro do Windows (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|Pastas HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|/sbin|C:\Windows\System32\userinit.exe|Pastas do Shell HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Arquivos de Programas\microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||Pastas HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|||Pastas do Shell HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Habilitar o monitoramento de integridade de arquivo 

O FIM só está disponível nas páginas da central de segurança na portal do Azure. Atualmente, não há uma API REST para trabalhar com o FIM.

1. Na área de **proteção avançada** do painel do **Azure defender** , selecione **monitoramento de integridade de arquivo**.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Iniciando o FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    A página configuração de **monitoramento de integridade de arquivo** é aberta.

    As informações a seguir são fornecidas para cada workspace:

    - Número total de alterações que ocorreram na última semana (você poderá ver um traço "-" se o FIM não estiver habilitado no workspace)
    - Número total de computadores e VMs se reportando para o workspace
    - Localização geográfica do workspace
    - Assinatura do Azure sob a qual o workspace está

1. Use esta página para:

    - Acessar e exibir o status e as configurações de cada espaço de trabalho

    - ![Ícone do plano ][4] de atualização atualize o espaço de trabalho para usar o Azure defender. Esse ícone indica que o espaço de trabalho ou a assinatura não está protegido pelo Azure defender. Para usar os recursos do FIM, sua assinatura deve ser protegida pelo Azure defender. Para obter mais informações, consulte a [central de segurança do Azure gratuita vs Azure defender habilitado](security-center-pricing.md).

    - ![Ícone Habilitar][3] Habilite o FIM em todos os computadores no espaço de trabalho e configure as opções do FIM. Este ícone indica que o FIM não está habilitado para o espaço de trabalho.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="Habilitando o FIM para um espaço de trabalho específico":::


    > [!TIP]
    > Se não houver nenhum botão habilitar ou atualizar e o espaço estiver em branco, isso significará que o FIM já está habilitado no espaço de trabalho.


1. Selecione **habilitar**. Os detalhes do espaço de trabalho, incluindo o número de computadores Windows e Linux, no espaço de trabalho, são mostrados.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="Página de detalhes do espaço de trabalho do FIM":::

   As configurações recomendadas para Windows e Linux também são listadas.  Expanda **Arquivos do Windows**, **Registro** e **Arquivos do Linux** para ver a lista completa de itens recomendados.

1. Desmarque as caixas de seleção de todas as entidades recomendadas que você não deseja que sejam monitoradas pelo FIM.

1. Selecione **Aplicar o monitoramento de integridade de arquivo** para habilitar o FIM.

> [!NOTE]
> Você pode alterar as configurações a qualquer momento. Consulte [Editar entidades monitoradas](#edit-monitored-entities) abaixo para saber mais.



## <a name="audit-monitored-workspaces"></a>Auditar espaços de trabalho monitorados 

O painel do **Monitoramento de integridade de arquivo** é exibido para workspaces em que o FIM está habilitado. O painel do FIM é aberto depois que você habilita o FIM em um espaço de trabalho ou quando seleciona um espaço de trabalho na janela **monitoramento de integridade de arquivo** que já tem o fim habilitado.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="O painel do FIM e seus vários painéis informativos":::

O painel do FIM de um espaço de trabalho exibe os seguintes detalhes:

- Número total de computadores conectados ao workspace
- Número total de alterações que ocorreram durante o período selecionado
- Uma divisão do tipo de alteração (arquivos, Registro)
- Uma divisão da categoria da alteração (modificado, adicionado, removido)

Selecione **Filtrar** na parte superior do painel para alterar o período de tempo para o qual as alterações são mostradas.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="Filtro de período de tempo para o painel do FIM":::

A guia **servidores** lista os computadores que se reportam a este espaço de trabalho. Para cada computador, o painel lista:

- Total de alterações que ocorreram durante o período selecionado
- Uma divisão do total de alterações como alterações de arquivo ou do Registro

A **pesquisa de logs** é aberta quando você insere um nome de computador no campo de pesquisa ou seleciona um computador listado na guia computadores. a pesquisa de log exibe todas as alterações feitas durante o período de tempo selecionado para o computador. Você pode expandir uma alteração para obter mais informações.

![Pesquisa de log][8]

A guia **Alterações** (mostrada abaixo) lista todas as alterações no workspace durante o período selecionado. Para cada entidade que foi alterado, o painel lista:

- Computador em que a alteração ocorreu
- Tipo de alteração (Registro ou arquivo)
- Categoria da alteração (modificado, adicionado, removido)
- Data e hora da alteração

![Alterações no workspace][9]

**Detalhes da alteração** abre quando você insere uma alteração no campo de pesquisa ou seleciona uma entidade listada na guia **Alterações**.

![Detalhes da alteração][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitoradas

1. Retorne ao **painel Monitoramento de integridade de arquivo** e selecione **configurações**.

   ![Configurações][11]

   **Configuração do Workspace** abre exibindo três guias: **Registro do Windows**, **Arquivos do Windows** e **Arquivos do Linux**. Cada guia lista as entidades que você pode editar nessa categoria. Para cada entidade listada, a Central de Segurança identifica se o FIM está habilitado (true) ou não está habilitado (false).  Editar a entidade permite que você habilite ou desabilite o FIM.

   ![Configuração do workspace][12]

2. Selecione uma proteção de identidade. Neste exemplo, selecionamos um item no Registro do Windows. **Editar para Controle de Alterações** é aberto.

   ![Editar ou controlar alterações][13]

Em **Editar para Controle de Alterações** você pode:

- Habilitar (True) ou desabilitar (false) o monitoramento de integridade de arquivo
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Excluir a entidade, descartar a alteração ou salvar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicionar uma nova entidade a ser monitorada
1. Volte para o **painel do Monitoramento de integridade de arquivo** e selecione **Configurações** na parte superior. **Configuração do Workspace** é aberto.
2. Em **Configuração do Workspace**, selecione a guia para o tipo de entidade que você deseja adicionar: Registro do Windows, Arquivos do Windows ou Arquivos do Linux. Neste exemplo, selecionamos **Arquivos do Linux**.

   ![Adicionar um novo item a ser monitorado][14]

3. Selecione **Adicionar**. **Adicionar para Controle de Alterações** é aberto.

   ![Inserir as informações solicitadas][15]

4. Na página **Adicionar**, digite as informações solicitadas e selecione **Salvar**.

## <a name="disable-monitored-entities"></a>Desabilitar entidades monitoradas
1. Retorne ao painel **monitoramento de integridade de arquivo** .
2. Selecione um workspace em que o FIM está habilitado no momento. Um workspace estará habilitado para o FIM se o botão Habilitar ou o botão Atualizar Plano estiver ausente.

   ![Selecione um workspace em que o FIM está habilitado][16]

3. Em monitoramento de integridade de arquivo, selecione **configurações**.

   ![Selecionar configurações][17]

4. Em **Configuração do Workspace**, selecione um grupo em que **Habilitado** está definido como true.

   ![Configuração do Workspace][18]

5. Na janela **Editar para Controle de Alterações**, defina **Habilitado** como False.

   ![Definir Habilitado como false][19]

6. Selecione **Salvar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Pasta e o caminho de monitoramento usando caracteres curinga

Use caracteres curinga para simplificar o rastreamento em diretórios. As seguintes regras se aplicam quando você configura o monitoramento de pasta usando caracteres curinga:
-   Caracteres curinga são necessários para acompanhar vários arquivos.
-   Caracteres curinga só podem ser usados no último segmento de um caminho, como C:\folder\file ou /etc/*.conf
-   Se uma variável de ambiente incluir um caminho que não é válido, a validação terá êxito, mas o caminho falhará quando o inventário for executado.
-   Ao definir o caminho, evite caminhos gerais, como c:\*. * que resultarão em muitas pastas sendo percorridas.

## <a name="disable-fim"></a>Desabilitar o FIM
Você pode desabilitar o FIM. O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Desabilitando o FIM, você pode remover a solução de Controle de Alterações do workspace selecionado.

1. Para desabilitar o FIM, retorne ao painel de **monitoramento de integridade de arquivo** .
2. Selecione um workspace.
3. Em **monitoramento de integridade de arquivo**, selecione **desabilitar**.

   ![Desabilitar o FIM][20]

4. Selecione **Remover** para desabilitar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o FIM (monitoramento de integridade de arquivo) na central de segurança. Para saber mais sobre a Central de Segurança, confira as páginas seguintes:

* [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar recomendações de segurança](security-center-recommendations.md): saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Blog de Segurança do Azure](/archive/blogs/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png