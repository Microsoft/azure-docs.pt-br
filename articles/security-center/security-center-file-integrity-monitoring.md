---
title: Monitoramento de Integridade de Arquivo na Central de Segurança do Azure | Microsoft Docs
description: " Saiba como habilitar o Monitoramento de Integridade de Arquivo na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: f8bc10edcdc31dd2ae3995dcb8321a5523e1e51c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705546"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoramento de integridade de arquivo na Central de Segurança do Azure
Saiba como configurar o FIM (Monitoramento de Integridade de Arquivo) na Central de Segurança do Azure usando este passo a passo.

## <a name="what-is-fim-in-security-center"></a>O que é FIM na Central de Segurança?
FIM (Monitoramento de Integridade de Arquivo), também conhecido como monitoramento de alterações, examina os arquivos e Registros do sistema operacional, aplicativos e outras alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do arquivo é diferente da última verificação do arquivo. Você pode aproveitar essa comparação para determinar se foram feitas modificações válidas ou suspeitas em seus arquivos.

O Monitoramento de Integridade de Arquivo da Central de Segurança valida a integridade dos arquivos do Windows, o Registro do Windows e os arquivos do Linux. Você seleciona os arquivos que deseja monitorar habilitando o FIM. A Central de Segurança monitora os arquivos com o FIM habilitado para atividades como:

- Criação e remoção de arquivo e Registro
- Modificações de arquivo (alterações de tamanho do arquivo, listas de controle de acesso e hash do conteúdo)
- Modificações de registro (alterações de tamanho, listas de controle de acesso, tipo e conteúdo)

A Central de Segurança recomenda entidades para serem monitoradas, nas quais você pode facilmente habilitar o FIM. Você também pode definir suas próprias políticas de FIM ou entidades para serem monitoradas. Este passo a passo mostra como fazer isso.

> [!NOTE]
> O recurso de FIM (Monitoramento de Integridade de Arquivo) funciona para computadores e VMs Windows e Linux e está disponível na camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança. O FIM carrega dados no espaço de trabalho do Log Analytics. Encargos de dados se aplicam, com base na quantidade de dados que você carregar. Consulte [Preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.

O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Quando o monitoramento de integridade de arquivo está habilitado, você tem um **Change Tracking** recurso do tipo **solução**. Para detalhes de frequência da coleta de dados, consulte [detalhes de coleta de dados de controle de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) para controle de alterações do Azure.

> [!NOTE]
> Se você remover o **controle de alterações** recurso, você também desabilitará a integridade do arquivo de recurso na Central de segurança de monitoramento.

## <a name="which-files-should-i-monitor"></a>Quais arquivos devo monitorar?
Você deve pensar sobre os arquivos que são críticos para seu sistema e aplicativos ao escolher quais arquivos monitorar. Considere a possibilidade de escolher os arquivos que você não pretende alterar sem planejamento. Escolher arquivos que são alterados com frequência por aplicativos ou sistema operacional (como arquivos de log e arquivos de texto) cria muito ruído que torna difícil de identificar um ataque.

A Central de Segurança recomenda quais arquivos você deve monitorar como padrão de acordo com padrões de ataque conhecidos que incluem alterações de arquivo e do Registro.

## <a name="using-file-integrity-monitoring"></a>Usando o Monitoramento de Integridade de Arquivo
1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, em **Defesa da nuvem avançada**, selecione **Monitoramento de Integridade de Arquivo**.
![Painel da Central de Segurança][1]

O **Monitoramento de Integridade do Arquivo** é aberto.
  ![Painel da Central de Segurança][2]

As informações a seguir são fornecidas para cada workspace:

- Número total de alterações que ocorreram na última semana (você poderá ver um traço "-" se o FIM não estiver habilitado no workspace)
- Número total de computadores e VMs se reportando para o workspace
- Localização geográfica do workspace
- Assinatura do Azure sob a qual o workspace está

Os botões a seguir também podem ser mostrados para um workspace:

- ![Ícone Habilitar][3] Indica que o FIM não está habilitado para o workspace. Selecionar o workspace permite que você habilite o FIM em todos os computadores no workspace.
- ![Ícone de Atualizar plano][4] indica que o workspace ou a assinatura não está em execução na camada Standard da Central de Segurança. Para usar o recurso do FIM, sua assinatura deve estar em execução na Standard.  Selecionar o workspace permite que você faça a atualização para Standard. Para saber mais sobre a camada padrão e como atualizar, consulte [Atualizar para a camada Standard da Central de Segurança para segurança aprimorada](security-center-pricing.md).
- Um espaço em branco (não há nenhum botão) significa que o FIM já está habilitado no workspace.

Em **Monitoramento de Integridade de Arquivo**, você pode selecionar um workspace para habilitar o FIM para ele, exibir o painel do Monitoramento de Integridade de Arquivo para esse workspace ou [atualizar](security-center-pricing.md) o workspace para Standard.

## <a name="enable-fim"></a>Habilitar o FIM
Para habilitar o FIM em um workspace:

1. Em **Monitoramento de Integridade de Arquivo**, selecione um workspace com o botão **habilitar**.
2. **Habilitar o monitoramento de integridade de arquivo** é aberta exibindo o número de computadores Windows e Linux no workspace.

   ![Habilitar o monitoramento de integridade de arquivo][5]

   As configurações recomendadas para Windows e Linux também são listadas.  Expanda **Arquivos do Windows**, **Registro** e **Arquivos do Linux** para ver a lista completa de itens recomendados.

3. Desmarque qualquer entidade recomendada à qual não deseja aplicar o FIM.
4. Selecione **Aplicar o monitoramento de integridade de arquivo** para habilitar o FIM.

> [!NOTE]
> Você pode alterar as configurações a qualquer momento. Consulte Editar entidades monitoradas abaixo para saber mais.
>
>

## <a name="view-the-fim-dashboard"></a>Exibir o painel do FIM
O painel do **Monitoramento de integridade de arquivo** é exibido para workspaces em que o FIM está habilitado. O painel do FIM é aberto depois de habilitar o FIM em um workspace ou quando você seleciona um workspace na janela **Monitoramento de Integridade de Arquivo** que já tenha o FIM habilitado.

![Painel do Monitoramento de Integridade de Arquivo][6]

O painel do FIM de um workspace exibe o seguinte:

- Número total de computadores conectados ao workspace
- Número total de alterações que ocorreram durante o período selecionado
- Uma divisão do tipo de alteração (arquivos, Registro)
- Uma divisão da categoria da alteração (modificado, adicionado, removido)

Selecionar Filtro na parte superior do painel permite que você aplique o período para o qual deseja ver as alterações.

![Filtro de período][7]

A guia **Computadores** (mostrada acima) lista todos os computadores que se reportam para esse workspace. Para cada computador, o painel lista:

- Total de alterações que ocorreram durante o período selecionado
- Uma divisão do total de alterações como alterações de arquivo ou do Registro

**Pesquisa de Logs** abre quando você insere o nome de um computador no campo de pesquisa ou seleciona um computador listado na guia Computadores. A Pesquisa de Logs exibe todas as alterações feitas durante o período selecionado para o computador. Você pode expandir uma alteração para obter mais informações.

![Pesquisa de Log][8]

A guia **Alterações** (mostrada abaixo) lista todas as alterações no workspace durante o período selecionado. Para cada entidade que foi alterado, o painel lista:

- Computador em que a alteração ocorreu
- Tipo de alteração (Registro ou arquivo)
- Categoria da alteração (modificado, adicionado, removido)
- Data e hora da alteração

![Alterações no workspace][9]

**Detalhes da alteração** abre quando você insere uma alteração no campo de pesquisa ou seleciona uma entidade listada na guia **Alterações**.

![Detalhes da alteração][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitoradas

1. Volte para o **painel do Monitoramento de Integridade de Arquivo** e selecione **Configurações**.

   ![Configurações][11]

   **Configuração do Espaço de trabalho** é aberta exibindo três guias: **Registro do Windows**, **Arquivos do Windows**, e **Arquivos Linux**. Cada guia lista as entidades que você pode editar nessa categoria. Para cada entidade listada, a Central de Segurança identifica se o FIM está habilitado (true) ou não está habilitado (false).  Editar a entidade permite que você habilite ou desabilite o FIM.

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
1. Retorne para o painel do **Monitoramento de Integridade de Arquivo**.
2. Selecione um workspace em que o FIM está habilitado no momento. Um workspace estará habilitado para o FIM se o botão Habilitar ou o botão Atualizar Plano estiver ausente.

   ![Selecione um workspace em que o FIM está habilitado][16]

3. Em Monitoramento de Integridade de Arquivo, selecione **Configurações**.

   ![Selecionar configurações][17]

4. Em **Configuração do Workspace**, selecione um grupo em que **Habilitado** está definido como true.

   ![Configuração do Workspace][18]

5. Na janela **Editar para Controle de Alterações**, defina **Habilitado** como False.

   ![Definir Habilitado como false][19]

6. Clique em **Salvar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Pasta e o caminho de monitoramento usando caracteres curinga

Use caracteres curinga para simplificar o rastreamento em diretórios. As seguintes regras se aplicam quando você configura o monitoramento de pasta usando caracteres curinga:
-   Caracteres curinga são necessários para acompanhar vários arquivos.
-   Caracteres curinga só podem ser usados no último segmento de um caminho, como C:\folder\file ou /etc/*.conf
-   Se uma variável de ambiente incluir um caminho que não é válido, a validação terá êxito, mas o caminho falhará quando o inventário for executado.
-   Ao definir o caminho, evite caminhos gerais, como c:\*. * que resultarão em muitas pastas sendo percorridas.

## <a name="disable-fim"></a>Desabilitar o FIM
Você pode desabilitar o FIM. O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Desabilitando o FIM, você pode remover a solução de Controle de Alterações do workspace selecionado.

1. Para desabilitar o FIM, retorne para o painel do **Monitoramento de Integridade de Arquivo**.
2. Selecione um workspace.
3. Em **Monitoramento de Integridade de Arquivo**, selecione **Desabilitar**.

   ![Desabilitar o FIM][20]

4. Selecione **Remover** para desabilitar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o FIM (Monitoramento de Integridade de Arquivo) na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar recomendações de segurança](security-center-recommendations.md): saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento da integridade da segurança](security-center-monitoring.md): saiba como monitorar a integridade dos seus recursos do Azure.
* [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros](security-center-partner-solutions.md): saiba como monitorar o status da integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
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
