---
title: "Restringir o acesso por meio de pontos de extremidade para a Internet na Central de Segurança do Azure | Microsoft Docs"
description: "Este artigo mostrou como implementar a recomendação da Central de Segurança do Azure **Restringir o acesso por meio de ponto de extremidade para a Internet**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir o acesso por meio de pontos de extremidade para a Internet na Central de Segurança do Azure
A Central de Segurança do Azure recomendará que você restrinja o acesso por meio de pontos de extremidade para a Internet se qualquer um dos seus grupos de segurança de rede (NSGs) tiver uma ou mais regras de entrada que permitam acesso de "qualquer" endereço IP de origem. Abrir o acesso a "qualquer" um pode permitir que os invasores acessem seus recursos. A Central de Segurança recomendará que você edite essas regras de entrada para restringir o acesso a endereços IP de origem que realmente precisem de acesso.

Essa recomendação é gerada para qualquer porta que não seja da Web que tenha "qualquer" como fonte.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações**, selecione **Restringir o acesso por meio de ponto de extremidade para a Internet**.

   ![Restringir o acesso por meio de ponto de extremidade para a Internet][1]
2. Isso abre a folha **Restringir o acesso por meio de ponto de extremidade para a Internet**. Essa folha lista as VMs (máquinas virtuais) com as regras de entrada que criam um problema potencial de segurança. Selecione uma VM.

   ![Selecionar uma máquina virtual][2]
3. A folha **NSG** exibe informações de Grupo de Segurança de Rede, as regras de entrada relacionadas e a VM associada. Selecione **Editar regras de entrada** para prosseguir com a edição de uma regra de entrada.

   ![Folha Grupo de Segurança de Rede][3]
4. Na folha **Regras de segurança de entrada** , selecione a regra de entrada a editar. Neste exemplo, vamos selecionar **AllowWeb**.

   ![Regras de segurança de entrada][4]

   Observe que você também pode selecionar **Regras padrão** para ver o conjunto de regras padrão contidas em todos os NSGs. As regras padrão não podem ser excluídas, mas como recebem uma prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. Saiba mais sobre [regras padrão](../virtual-network/virtual-networks-nsg.md#default-rules).

   ![Regras padrão][5]
5. Na folha **AllowWeb**, edite as propriedades da regra de entrada para que a **Origem** seja um endereço IP ou bloco de endereços IP. Para saber mais sobre as propriedades da regra de entrada, consulte [Regras de NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

   ![Editar regra de entrada][6]

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Restringir o acesso por meio de ponto de extremidade para a Internet". Para saber mais sobre como habilitar NSGs e regras, confira o seguinte:

* [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
* [Como gerenciar NSGs usando o portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) : saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
