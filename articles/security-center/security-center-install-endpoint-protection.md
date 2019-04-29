---
title: Gerenciar problemas de proteção de ponto de extremidade com a Central de Segurança do Azure | Microsoft Docs
description: Saiba como gerenciar problemas de proteção de ponto de extremidade na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906427"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gerenciar problemas de proteção de ponto de extremidade com a Central de Segurança do Azure
A Central de Segurança do Azure monitora o status da proteção antimalware e relata isso na folha Problemas de proteção de ponto de extremidade. A Central de Segurança realça os problemas, como ameaças detectadas e proteção insuficiente, que podem tornar seus computadores e VMs (máquinas virtuais) vulneráveis a ameaças de antimalware. Ao usar as informações em **Problemas de proteção de ponto de extremidade**, você pode identificar um plano para solucionar os problemas identificados.

A Central de Segurança relata os seguintes problemas de proteção de ponto de extremidade:

- Proteção de ponto de extremidade não instalada em VMs do Azure – uma solução antimalware com suporte não está instalada nessas VMs do Azure.
- Proteção de ponto de extremidade não instalada em computadores não Azure – um antimalware com suporte não está instalado nesses computadores não Azure.
- Integridade da proteção do ponto de extremidade:

  - Assinatura desatualizada – uma solução antimalware está instalada nessas VMs e computadores, mas a solução não tem as assinaturas de antimalware mais recentes.
  - Sem proteção em tempo real – uma solução antimalware está instalada nessas VMs e computadores, mas ela não está configurada para oferecer proteção em tempo real.   O serviço pode estar desabilitado ou a Central de Segurança não conseguiu obter o status porque não há suporte para a solução. Consulte a [integração com parceiros](security-center-os-coverage.md#supported-endpoint-protection-solutions) para obter uma lista de soluções com suporte.
  - Sem relatório – uma solução antimalware está instalada, mas não está relatando dados.
  - Desconhecido – uma solução antimalware está instalada, mas seu status é desconhecido ou está relatando um erro desconhecido.

    > [!NOTE]
    > Consulte [Integrar soluções de segurança](security-center-os-coverage.md#supported-endpoint-protection-solutions) para obter uma lista de soluções de segurança da proteção de ponto de extremidade integradas com a Central de Segurança.
    >
    >

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Os problemas de proteção de ponto de extremidade são apresentado como uma recomendação na Central de Segurança.  Se seu ambiente estiver vulnerável a ameaças de antimalware, essa recomendação será exibida em **Recomendações** e em **Computação**. Para ver o **painel Problemas de proteção de ponto de extremidade**, você deve seguir o fluxo de trabalho de Computação.

Neste exemplo, usaremos **Computação**.  Examinaremos como instalar antimalware em VMs do Azure e em computadores não Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalar antimalware em VMs do Azure

1. Selecione **computação e aplicativos** sob o menu principal da Central de segurança ou **visão geral**.

   ![Selecionar Computação][1]

2. Em **Computação**, selecione **Problemas de proteção de ponto de extremidade**. O painel **Problemas de proteção de ponto de extremidade** é aberto.

   ![Selecionar Problemas de proteção de ponto de extremidade][2]

   A parte superior do painel fornece:

   - Provedores de proteção de ponto de extremidade instalados – lista os diferentes provedores identificados pela Central de Segurança.
   - Estado de integridade da proteção de ponto de extremidade instalada – mostra o estado de integridade das VMs e dos computadores que têm uma solução de proteção de ponto de extremidade instalada. O gráfico mostra o número de VMs e computadores que estão íntegros, bem como quantos estão com proteção insuficiente.
   - Malware detectado – mostra o número de VMs e computadores em que a Central de Segurança relata detecção de malware.
   - Computadores atacados – mostra o número de VMs e computadores em que a Central de segurança relata ataques por malware.

   Na parte inferior do painel há uma lista de problemas de proteção de ponto de extremidade que inclui as seguintes informações:  

   - **TOTAL** – o número de VMs e computadores afetados pelo problema.
   - Uma barra agregando o número de VMs e computadores afetados pelo problema. As cores na barra identificam a prioridade:

      - Vermelho – alta prioridade e deve ser resolvida imediatamente
      - Laranja – prioridade média e deve ser resolvida assim que possível

3. Selecione **Proteção de ponto de extremidade não instalada nas VMs do Azure**.

   ![Selecionar Proteção de ponto de extremidade não instalada nas VMs do Azure][3]

4. Em **Proteção de ponto de extremidade não instalada nas VMs do Azure** está uma lista de VMs do Azure que não têm um antimalware instalado.  Você pode optar por instalar antimalware em todas as VMs da lista ou selecionar VMs individuais nas quais instalar antimalware, clicando na VM específica.
5. Em **Selecionar proteção de ponto de extremidade**, selecione a solução de proteção de ponto de extremidade que você deseja usar. Neste exemplo, selecione **Microsoft Antimalware**.
6. Informações adicionais sobre a solução de proteção de ponto de extremidade são exibidas. Selecione **Criar**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalar antimalware em computadores não Azure

1. Volte para **Problemas de proteção de ponto de extremidade** e selecione **Proteção de ponto de extremidade não instalada em computadores não Azure**.

   ![Selecionar Proteção de ponto de extremidade não instalada em computadores não Azure][4]

2. Em **Proteção de ponto de extremidade não instalada em computadores não Azure**, selecione um workspace. Uma consulta de pesquisa de logs do Azure Monitor filtrada para o espaço de trabalho abre e lista computadores sem antimalware. Selecione um computador da lista para obter mais informações.

   ![Pesquisa de logs do Azure Monitor][5]

Outro resultado da pesquisa é aberto com informações filtradas para esse computador.

  ![Pesquisa de logs do Azure Monitor][6]

> [!NOTE]
> É recomendável que a proteção de ponto de extremidade seja provisionada para todas as VMs e computadores a fim de ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados.
>
>

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como implementar a recomendação da Central de Segurança "Instalar Endpoint Protection". Para saber mais sobre como habilitar o Microsoft Antimalware no Azure, consulte o seguinte documento:

* [Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais](../security/azure-security-antimalware.md) – saiba como implantar o Microsoft Antimalware.

Para saber mais sobre a Central de Segurança, confira os seguintes documentos:

* [Configuração de políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) – saiba como definir as políticas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
