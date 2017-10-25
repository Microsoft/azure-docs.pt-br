---
title: "Guia de início rápido da Central de Segurança do Azure | Microsoft Docs"
description: "Este artigo ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure, pois o orienta em relação aos componentes de monitoramento da segurança e gerenciamento de políticas, além de conduzi-lo às próximas etapas."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Guia de início rápido da Central de Segurança do Azure
Este artigo ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure, pois o orienta em relação aos componentes de monitoramento da segurança e gerenciamento de políticas da Central de Segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

A camada Gratuita da Central de Segurança é habilitada automaticamente em todas as assinaturas do Azure e oferece política de segurança, avaliação de segurança contínua e recomendações de práticas de segurança para ajudar a proteger seus recursos do Azure.

Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Para saber mais sobre o portal do Azure, confira a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Permissões
Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence. Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

## <a name="data-collection"></a>Coleta de dados
A Central de Segurança coleta dados de suas VMs (máquinas virtuais) do Azure e dos computadores não Azure a fim de monitorar as ameaças e vulnerabilidades de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu espaço de trabalho para serem analisados. A Central de Segurança do Azure provisiona o Microsoft Monitoring Agent em todas as VMs do Azure existentes com suporte e em quaisquer novas VMs criadas. Consulte [Habilitar coleta de dados](security-center-enable-data-collection.md) para saber mais sobre como a coleta de dados funciona.

O provisionamento automático é altamente recomendável e é necessário para as assinaturas na camada Standard da Central de Segurança. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.

Veja [Tipos de preço da Central de Segurança](security-center-pricing.md) para saber mais sobre os tipos de preço Gratuito e Standard.

As etapas a seguir descrevem como acessar e usar os componentes da Central de Segurança.

> [!NOTE]
> Este artigo apresenta o serviço usando uma implantação de exemplo. Ele não é um guia passo a passo.
>
>

## <a name="access-security-center"></a>Acessar a Central de Segurança
No Portal, execute estas etapas para acessar a Central de Segurança:

1. No menu **Microsoft Azure**, selecione **Central de Segurança**.

   ![Menu do Azure][1]
2. Se você estiver acessando a Central de Segurança pela primeira vez, a folha **Bem-vindo** será aberta. Selecione **Iniciar Central de Segurança** para abrir a **Central de Segurança**.
   ![Tela de boas-vindas][10]
3. Depois que você iniciar a Central de Segurança na folha Bem-vindo ou selecionar Central de Segurança no menu do Microsoft Azure, a **Central de Segurança** será aberta. Para facilitar o acesso à folha **Central de segurança** no futuro, selecione opção **Fixar folha no painel** (canto superior direito).
   ![Fixar a folha à opção de painel][2]

## <a name="use-security-center"></a>Usar a Central de Segurança
Você pode configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure. Vamos configurar uma política de segurança para sua assinatura:

1. No menu principal da Central de Segurança, selecione **Política de segurança**.
2. Em **Central de segurança – Política de segurança**, selecione uma assinatura.
3. Em **Política de segurança – Coleção de Dados**, **o Provisionamento automático** está ativado. A Central de Segurança do Azure provisiona o Microsoft Monitoring Agent em todas as VMs do Azure existentes com suporte e em quaisquer novas VMs criadas.

    ![Política de segurança][12]

4. Na folha **Política de segurança**, selecione o componente de política **Política de segurança**.

     ![Política de segurança][11]

5. Em **Mostrar recomendações para**, ative as recomendações que você deseja ver como parte de sua política de segurança. Exemplos:

   * Definir as **Atualizações do sistema** como **Ligadas** verificará todas as VMs com suporte quanto às atualizações do SO ausentes.
   * Definir **Vulnerabilidade do SO** como **Ligado** verificará as VMs com suporte para identificar as configurações do SO que poderiam tornar a VM mais vulnerável a ataques.

6. Selecione **Salvar**.

### <a name="view-recommendations"></a>Exibir recomendações
1. Volte para a folha **Central de Segurança** e selecione o bloco **Recomendações**. A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações na folha **Recomendações** .
   ![Recomendações na Central de Segurança do Azure][5]
2. Selecione uma recomendação na folha **Recomendação** para exibir mais informações e/ou tomar medidas para resolver o problema.

### <a name="view-the-security-state-of-your-resources"></a>Exiba o estado de segurança de seus recursos
1. Volte para a folha **Central de segurança** . A seção **Prevenção** do painel contém indicadores do estado da segurança para VMs, rede, dados e aplicativos.
2. Selecione **Computação** para exibir mais informações. A folha **Computação** será aberta mostrando três guias:

  - **Visão geral** – contém recomendações de monitoramento e da VM.
  - **VMs e computadores** – lista o estado de segurança atual de todas as VMs e computadores.
  - **Serviços de nuvem** - Lista as funções da Web e de trabalho monitoradas pela Central de Segurança.

    ![Integridade da segurança de computação][6]

3. Na guia **Visão geral**, selecione uma recomendação para exibir mais informações e/ou tomar medidas para configurar os controles necessários.
4. Na guia **VMs e computadores**, selecione um recurso para exibir detalhes adicionais.

### <a name="view-security-alerts"></a>Exibir alertas de segurança
1. Volte para a folha **Central de Segurança** e selecione o bloco **Alertas de segurança**. A folha **Alertas de segurança** exibe uma lista de alertas. A análise da Central de Segurança dos seus logs de segurança e da atividade da rede gera esses alertas. Os alertas das soluções de parceiro integradas estão incluídos.
   ![Alertas na Central de Segurança do Azure][7]

2. Selecione um alerta para exibir mais informações. Neste exemplo, vamos selecionar **Binário do sistema modificado descoberto no filtro de despejo**. Isso abre folhas que fornecem detalhes adicionais sobre o alerta.
   ![Detalhes de alerta de segurança na Central de Segurança do Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Exiba a integridade de suas soluções de parceiros:
1. Volte para a folha **Central de segurança** . O bloco **Soluções de segurança** permite monitorar rapidamente o status da integridade de suas soluções de parceiros integradas em sua assinatura do Azure.
2. Selecione o bloco **Soluções de segurança**. Uma folha será aberta e exibirá uma lista de suas soluções de parceiros conectadas à Central de Segurança.
   ![Soluções de parceiros][9]
3. Selecione uma solução de parceiro. Uma folha será aberta e mostrará o status da solução de parceiro e dos recursos associados a ela. Selecione **Console da solução** para abrir a experiência de gerenciamento do parceiro para essa solução.

   ![Soluções de parceiros][13]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você foi apresentado aos componentes de monitoramento de segurança e de gerenciamento de políticas da Central de Segurança. Agora que você está familiarizado com a Central de Segurança, tente as seguintes etapas:

* Configure uma política de segurança para sua assinatura do Azure, consulte a seção [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md).
* Use as recomendações na Central de Segurança para ajudá-lo a proteger seus recursos do Azure, consulte [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).
* Analise e gerencie seus alertas de segurança atuais, consulte [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).
- Saiba mais sobre a integração com parceiros para aperfeiçoar a segurança geral, consulte [Integração de parceiro e soluções](security-center-partner-integration.md).
- Saiba como os dados são gerenciados e protegidos na Central de Segurança, consulte [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md).
* Saiba mais sobre os [recursos avançados de detecção de ameaça](security-center-detection-capabilities.md) que vêm com a [camada Standard](security-center-pricing.md) da Central de Segurança. A camada Standard é oferecida gratuitamente nos 60 primeiros dias.
* Se você tiver dúvidas sobre como usar a Central de Segurança, confira as [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
