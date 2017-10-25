---
title: "Aplicar atualizações do sistema na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar as recomendações da Central de Segurança do Azure **Aplicar atualizações do sistema** e **Reinicializar após as atualizações do sistema**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações do sistema na Central de Segurança do Azure
A Central de Segurança do Azure monitora diariamente VMs (máquinas virtuais) e computadores Windows e Linux para saber se faltam atualizações do sistema operacional. A Central de Segurança recupera uma lista de atualizações críticas e de segurança disponíveis no Windows Update ou no WSUS (Windows Server Update Services), dependendo de qual serviço está configurado em um computador Windows. A Central de Segurança também verifica as atualizações mais recentes em sistemas Linux. Se faltar uma atualização do sistema em sua VM ou seu computador, a Central de Segurança recomendará que você aplique as atualizações do sistema.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
A aplicação das atualizações de sistema é apresentada como uma recomendação na Central de Segurança. Se estiver faltando uma atualização do sistema em seu computador ou sua VM, essa recomendação será exibida em **Recomendações** e em **Computação**.  Ao selecionar a recomendação, abre-se o painel **Aplicar atualizações do sistema**.

Neste exemplo, usaremos **Computação**.

1. Selecione **Computação** no menu principal da Central de Segurança.

   ![Selecionar Computação][1]

2. Em **Computação**, selecione **Atualizações do sistema ausentes**. O painel **Aplicar atualizações do sistema** é aberto.

   ![Painel Aplicar atualizações do sistema][2]

   A parte superior do painel fornece:

    - O número total de VMs e computadores Windows e Linux com atualizações de sistema ausentes.
    - O número total de atualizações críticas ausentes em suas VMs e computadores.
    - O número total de atualizações de segurança ausentes em suas VMs e computadores.

  A parte inferior do painel lista todas as atualizações ausentes em suas VMs e computadores e a gravidade da atualização ausente.  A lista inclui:

    - NOME: nome da atualização ausente.
    - NÚMERO DE VMs E COMPUTADORES: número total de máquinas virtuais e computadores que não têm essa atualização.
    - ESTADO: o estado atual da recomendação:

      - Aberta: a recomendação ainda não foi resolvida.
      - Em Andamento: a recomendação está sendo aplicada atualmente aos recursos e não é necessário que você realize nenhuma ação.
      - Resolvido: a recomendação já foi concluída. (Quando o problema foi resolvido, a entrada será esmaecida).

    - GRAVIDADE: descreve a gravidade dessa recomendação específica:

      - Alta: existe uma vulnerabilidade em um recurso significativo (aplicativo, máquina virtual ou grupo de segurança de rede) e ela requer atenção.
      - Média: são necessárias etapas adicionais ou não críticas para concluir um processo ou eliminar uma vulnerabilidade.
      - Baixa: uma vulnerabilidade que deve ser resolvida, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje exibi-las.)

3. Selecione uma atualização ausente na lista para exibir detalhes.

   ![Atualização de segurança ausente][3]

4. Selecione o ícone **Pesquisar** na faixa de opções superior.  Uma consulta de pesquisa do Log Analytics é aberta, filtrada para os computadores sem a atualização.

   ![Pesquisa de Análise de Log][4]

5. Selecione um computador da lista para obter mais informações. Outro resultado da pesquisa é aberto com informações filtradas para esse computador.

    ![Pesquisa de Análise de Log][5]

## <a name="reboot-after-system-updates"></a>Reinicializar após as atualizações do sistema
1. Volte para a folha **Recomendações** . Uma nova entrada foi gerada depois que você aplicou atualizações do sistema, chamada **Reinicializar após as atualizações do sistema**. Essa entrada permite que você saiba que é necessário reinicializar a máquina virtual para concluir o processo de aplicação de atualizações de sistema.

   ![Reinicializar após as atualizações do sistema][6]
2. Selecione **Reinicializar após as atualizações do sistema**. Isso abre a folha **Uma reinicialização está pendente para concluir as atualizações do sistema** exibindo uma lista de máquinas virtuais que você precisa reiniciar para concluir o processo de aplicar atualizações d sistema.

   ![Reinicialização pendente][7]

Reinicie a VM do Azure para concluir o processo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
