---
title: Adicionar um Firewall do Aplicativo Web na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar as recomendações da Central de Segurança do Azure **Adicionar um firewall do aplicativo Web** e **Finalizar a proteção do aplicativo**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706151"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adicionar um Firewall do Aplicativo Web na Central de Segurança do Azure
A Central de Segurança do Azure pode recomendar que você adicione um WAF (Firewall do Aplicativo Web) de um parceiro da Microsoft para proteger seus aplicativos Web. Este documento guiará você por um exemplo de como realizar essa recomendação.

Uma recomendação WAF é mostrada para qualquer IP voltado para uso público (IP de nível de instância ou IP de balanceamento de carga) que tem um grupo de segurança de rede associado com portas de entrada da Web abertas (80,443).

A Central de Segurança recomenda que você provisione um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos Web em máquinas virtuais e nos Ambientes externos do Serviço de Aplicativo implantados no plano de serviço [Isolado](https://azure.microsoft.com/pricing/details/app-service/windows/). O plano Isolado hospeda aplicativos em um ambiente particular e dedicado do Azure, ideal para aplicativos que exigem conexões seguras a sua rede local ou desempenho e escala adicionais. Além de seu aplicativo estar em um ambiente isolado, seu aplicativo precisa ter um balanceador de carga de endereço IP externo. Para saber mais sobre o ASE, consulte a [Documentação do Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md).

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Em **Recomendações**, selecione **Proteger o aplicativo Web usando o firewall do aplicativo Web**.
   ![Aplicativo Web protegido][1]
2. Em **Proteger seus aplicativos Web usando o firewall do aplicativo Web**, selecione um aplicativo Web. A janela **Adicionar um Firewall do Aplicativo Web** é exibida.
   ![Adicione um firewall do aplicativo Web][2]
3. Você pode optar por usar um Firewall do Aplicativo Web existente, se disponível, ou criar um novo. Neste exemplo, não existem WAFs disponíveis, por isso vamos criar um novo.
4. Para criar um WAF, selecione uma solução da lista dos parceiros integrados. Neste exemplo, selecionamos **Barracuda Web Application Firewall**.
5. O **Firewall do Aplicativo Web da Barracuda** é aberto, fornecendo informações sobre a solução do parceiro. Selecione **Criar**.

   ![Folha Informações do firewall][3]

6. O **Novo Firewall do Aplicativo Web** é aberto e você pode executar as etapas de **Configuração da VM** e fornecer **Informações do WAF**. Selecione **Configuração da VM**.
7. Em **Configuração da VM**, você insere as informações necessárias para criar a máquina virtual que executará o WAF.

   ![Configuração da VM][4]
   
8. Volte para **Novo Firewall do Aplicativo Web** e selecione **Informações do WAF**. Em **Informações do WAF**, você configura o WAF. A Etapa 7 permite configurar a máquina virtual na qual o WAF será executado e a Etapa 8 permite provisionar o WAF em si.

## <a name="finalize-application-protection"></a>Finalizar a proteção do aplicativo
1. Retorne a **Recomendações**. Uma nova entrada foi gerada depois que você criou o WAF, chamada **Finalizar a proteção do aplicativo**. Essa entrada informa o que é necessário para concluir o processo de conectar o WAF dentro da Rede Virtual do Azure para que ele possa proteger o aplicativo.

   ![Finalizar a proteção do aplicativo][5]

2. Selecione **Finalizar a proteção do aplicativo**. Uma nova lâmina é aberta. Você pode ver que há um aplicativo Web que precisa ter seu tráfego redirecionado.
3. Selecione o aplicativo Web. Uma folha será aberta com etapas para concluir a configuração de firewall do aplicativo Web. Conclua as etapas e selecione **Restringir o tráfego**. A Central de Segurança criará então as estruturas para você.

   ![Restringir o tráfego][6]

> [!NOTE]
> Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes.
>
>

Os logs daquele WAF agora estão totalmente integrados. A Central de Segurança pode iniciar a coleta e a análise dos logs automaticamente para revelar alertas de segurança importantes para você.

## <a name="next-steps"></a>Próximas etapas
Este documento mostrou como implementar a recomendação da Central de Segurança "Adicionar um aplicativo Web". Para saber mais sobre como configurar um Firewall do Aplicativo Web, consulte o seguinte:

* [Configurando um WAF (Firewall do Aplicativo Web) para Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
