---
title: "Integração à Central de Segurança do Azure Standard para uma segurança aprimorada| Microsoft Docs"
description: " Saiba como para integrar-se à Central de Segurança do Azure Standard para uma segurança aprimorada. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Integração à Central de Segurança do Azure Standard para uma segurança aprimorada
Atualize para a Central de Segurança Standard para aproveitar o gerenciamento de segurança aprimorado e a proteção contra ameaças para suas cargas de trabalho de nuvem híbrida.  Experimente a versão Standard gratuitamente por 60 dias. Para saber mais, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.

A Central de Segurança Standard inclui:

- **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de uma variedade de fontes, inclusive firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** – Use análises avançadas e o Gráfico de segurança inteligente da Microsoft para obter uma vantagem sobre ataques cibernéticos em evolução.  Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Controles de acesso e de aplicativo** – Bloqueie malwares e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptada para suas cargas de trabalho específicas e baseada em aprendizado de máquina. Reduza a superfície de ataque da rede com acesso controlado Just-In-Time às portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à ataques de força bruta e outros ataques de rede.

## <a name="detecting-unprotected-resources"></a>Detectando recursos desprotegidos     
A Central de Segurança detecta automaticamente as assinaturas ou espaços de trabalho do Azure não habilitados para a Central de Segurança Standard. Isso inclui assinaturas do Azure usando a Central de Segurança Gratuita e espaços de trabalho que não têm a solução da Segurança habilitada.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que será herdada por todos os recursos na assinatura ou você pode definir uma política exclusiva para atualizar somente um grupo de recursos específico. Se as configurações de política do grupo de recursos forem exclusivas, a Central de Segurança não substituirá as políticas de preço quando você atualizar a assinatura para a camada Standard. A aplicação da camada Standard a uma assinatura só tem efeito em VMs da assinatura que estejam se relacionando com espaços de trabalho criados pela Central de Segurança. A aplicação da camada Standard ao espaço de trabalho terá efeito em todos os recursos que se relacionam com o espaço de trabalho.

> [!NOTE]
> Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes. O [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite que você aplique um escopo à solução e direcione a um subconjunto de computadores no espaço de trabalho.  Se você estiver usando o direcionamento de solução, a Central de Segurança listará o espaço de trabalho como não tendo uma solução.
>
>

## <a name="upgrade-an-azure-subscription"></a>Atualizar uma assinatura do Azure
Para atualizar todas as assinaturas para a camada Standard:
1. No menu principal da Central de Segurança, selecione **Integração**.
2. Em **Integração à segurança avançada**, a Central de Segurança lista as assinaturas qualificadas para a integração. Você pode atualizar todas as assinaturas listadas selecionando **Aplicar plano Standard**.

  ![Atualizar todas as assinaturas][1]

Para atualizar uma assinatura individual para Standard: você pode atualizar uma assinatura da **Integração** selecionando **Aplicar Camada Standard**. Para atualizar um grupo de recursos na assinatura para a camada Standard, selecione a assinatura:
1. Selecione uma assinatura.  A **política de segurança** fornece informações sobre o grupo de recursos contido na assinatura.
2. Selecione a assinatura ou um grupo de recursos.

  ![Atualizar todas as assinaturas][2]

3. Selecione **Standard** para atualizar de Gratuito para Standard.
4. Selecione **Salvar**.

> [!NOTE]
> A atualização de uma assinatura para Standard ativará o [provisionamento automático](security-center-enable-data-collection.md), caso ele tenha sido previamente desabilitado. É recomendável o provisionamento automático dos agentes de monitoramento.
>
>

## <a name="upgrade-a-workspace"></a>Atualizar um espaço de trabalho
Aplicar Standard ao espaço de trabalho terá efeito em todos os recursos que se relacionam com o espaço de trabalho.

1. Volte para a folha **Integração**.
2. Selecione um espaço de trabalho.

  ![Atualizar um espaço de trabalho][8]

3. Selecione **Standard** para atualizar.  
4. Selecione **Salvar**.

   > [!NOTE]
   > Há um cenário em que você poderá não ter Gratuito ou Standard aplicado ao seu espaço de trabalho. Se você selecionar Gratuito, os recursos Gratuitos da Central de Segurança serão aplicados somente às suas VMs do Azure. Os recursos Gratuitos não serão aplicados aos computadores não Azure. Se você selecionar Standard, os recursos Standard serão aplicados a todas as VMs do Azure, bem como aos computadores não Azure que se relacionam com o espaço de trabalho. É recomendável que você aplique a Standard para proporcionar segurança avançada tanto para seus recursos do Azure quanto para os não Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Integrar computadores não Azure
A Central de Segurança pode monitorar a postura de segurança dos computadores não Azure, mas, antes, é necessário que você integre esses recursos. Você pode adicionar computadores não Azure por meio da folha **Integração** ou da folha **Computação**. Vamos demonstrar os dois métodos.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Adicionar novos computadores não Azure por meio da Integração

1. Retorne à **Integração**.   
2. Selecione **Você deseja adicionar novos computadores não Azure**.

  ![Adicionar computador não Azure][3]

Se você tiver espaços de trabalho existentes, eles serão listados em **Adicionar novos computadores não Azure**. Você pode adicionar computadores a um espaço de trabalho existente ou criar um novo espaço de trabalho. Para criar um novo espaço de trabalho, selecione o link **adicionar um novo espaço de trabalho**.

Vamos demonstrar os dois métodos:

- Criar um novo espaço de trabalho e adicionar computador
- Selecionar um espaço de trabalho existente e adicionar computador

**Criar um novo espaço de trabalho e adicionar computador**

1. Em **Adicionar novos computadores não Azure**, selecione **adicionar um novo espaço de trabalho**.

   ![Adicionar um novo espaço de trabalho][4]

2. Em **Segurança e Auditoria**, selecione **Espaço de Trabalho OMS** para criar um novo espaço de trabalho.
3. Em **Espaço de Trabalho OMS**, insira as informações do seu espaço de trabalho.
4. Em **Espaço de Trabalho OMS**, selecione **OK**.  Depois de selecionar OK, você receberá um link para baixar um agente do Windows ou do Linux e as chaves da sua ID do espaço de trabalho para ser usada na configuração do agente.
5. Em **Segurança e Auditoria**, selecione **OK**.

**Selecionar um espaço de trabalho existente e adicionar computador**

Você pode adicionar um computador, seguindo o fluxo de trabalho de **Integração**, conforme mostrado acima. Você também pode adicionar um computador, seguindo o fluxo de trabalho de **Computação**. Neste exemplo, usamos **Computação**.

1. Retorne ao menu principal da Central de Segurança e ao painel **Visão geral**.

   ![Visão geral][5]

2. Selecione o bloco **Computação**.
3. Em **Computação**, selecione **Adicionar computadores**.

   ![Folha Computação][6]

4. Em **Adicionar novos computadores não Azure**, selecione um espaço de trabalho ao qual conectar seu computador e clique em **Adicionar computadores**.

   ![Adicionar computadores][7]

 A folha **Agente Direto** fornece um link para baixar um agente do Windows ou do Linux e as chaves da sua ID do espaço de trabalho para ser usada na configuração do agente.   

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como integrar recursos Azure e não Azure para se beneficiar da segurança avançada da Central de Segurança.  Para fazer mais com os recursos integrados, veja

- [Habilitar coleta de dados](security-center-enable-data-collection.md)
- [Relatório de inteligência de ameaças](security-center-threat-report.md)
- [Acesso Just-In-Time à VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
