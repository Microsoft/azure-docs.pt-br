---
title: Adicionar um Firewall de Última Geração na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar as recomendações da Central de Segurança do Azure **Adicionar um Firewall de Próxima Geração** e **Rotear tráfego através de NGFW apenas**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706914"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar um Firewall de Última Geração na Central de Segurança do Azure
A Central de Segurança do Azure pode recomendar que você adicione um firewall de última geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança. Este documento guiará você por um exemplo de como fazer isso.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações** , selecione **Adicionar um Firewall de Última Geração**.
   ![Adicionar um Firewall de Última Geração][1]
2. Na folha **Adicionar um Firewall de Última Geração** , selecione um ponto de extremidade.
   ![Selecionar um ponto de extremidade][2]
3. Uma segunda folha **Adicionar um Firewall de Última Geração** será aberta. Você pode optar por usar uma solução existente, se disponível, ou criar uma nova. Neste exemplo não existem soluções disponíveis, por isso, criaremos um novo NGFW.
   ![Criar um novo Firewall de Última Geração][3]
4. Para criar um NGFW, selecione uma solução da lista de parceiros integrados. Neste exemplo, selecionamos **Check Point**.
   ![Selecionar uma solução de Firewall de Última Geração][4]
5. A folha **Check Point** se abre e fornece informações sobre a solução do parceiro. Selecione **Criar** na folha de informações.
   ![Folha Informações do firewall][5]
6. A folha **Criar máquina virtual** é aberta. Nessa folha, é possível inserir as informações necessárias para criar uma VM (máquina virtual) que execute o NGFW. Siga as etapas e forneça as informações do NGFW necessárias. Selecione OK para aplicar.
   ![Criar uma máquina virtual para executar o NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Rotear o tráfego apenas através do NGFW
Volte para a folha **Recomendações** . Uma nova entrada foi gerada após a adição de um NGFW por meio da Central de Segurança, chamada **Rotear o tráfego somente por meio do NGFW**. Essa recomendação será criada somente se você tiver instalado o NGFW por meio da Central de Segurança. Se você tem pontos de extremidade para a Internet, a Central de Segurança recomenda que você configure regras de Grupo de Segurança de Rede que forcem o tráfego de entrada para sua VM por meio do NGFW.

1. Na **folha Recomendações**, selecione **Rotear o tráfego apenas através do NGFW**.
   ![Rotear o tráfego apenas através do NGFW][7]
2. Isso abrirá a folha **Rotear o tráfego somente por meio do NGFW**, que lista as VMs pelas quais você pode rotear o tráfego. Selecione uma VM na lista.
   ![Selecionar uma máquina virtual][8]
3. Uma folha para a VM selecionada é aberta, exibindo as regras de entrada relacionadas. Uma descrição fornece a você mais informações sobre as próximas etapas possíveis. Selecione **Editar regras de entrada** para prosseguir com a edição de uma regra de entrada. A expectativa é que **Fonte** não esteja definido como **Qualquer** para os pontos de extremidade voltados para a Internet vinculados com o NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte [regras de segurança](../virtual-network/security-overview.md#security-rules).
   ![Configurar regras para limitar o acesso][9]
   ![Editar regra de entrada][10]

## <a name="see-also"></a>Consulte também
Este documento mostrou como implementar a recomendação da Central de Segurança para "Adicionar um Firewall de Última Geração". Para saber mais sobre NGFWs e a solução de parceiro da Check Point, consulte:

* [Firewall de Última Geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configuração de políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como definir as políticas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
