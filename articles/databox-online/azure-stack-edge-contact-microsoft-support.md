---
title: Log de tíquete de suporte para Azure Stack Edge pro, Gateway do Azure Data Box | Microsoft Docs
description: Saiba como registrar em log a solicitação de suporte para problemas relacionados às suas ordens do Azure Stack Edge pro ou Gateway do Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 411757f4ef4e33ccbc8a69ca2b40598b51c6d13b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790826"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Abra um tíquete de suporte para Azure Stack Edge pro e Gateway do Azure Data Box

Este artigo se aplica a Azure Stack o Edge pro e Gateway do Azure Data Box ambos são gerenciados pelo serviço pro/Gateway do Azure Data Box do Azure Stack Edge. Se você encontrar algum problema com sua solução, é possível criar uma solicitação de serviço para suporte técnico. Este artigo orienta você sobre:

* Como criar uma solicitação de suporte.
* Como gerenciar o ciclo de vida de uma solicitação de suporte de dentro do portal.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Execute as seguintes etapas para criar uma solicitação de suporte:

1. Acesse a ordem do Azure Stack Gateway do Data Box do Edge pro ou do. Navegue para a seção **Suporte + Solução de problemas** e depois selecione **Nova solicitação de suporte**.

2. Em **Nova solicitação de suporte** na guia **Básico**, execute as etapas a seguir:

    1. Na lista suspensa **Tipo de problema**, selecione **Técnico**.
    2. Escolha sua **Assinatura**.
    3. Em **Serviço**, verifique **Meus serviços**. Na lista suspensa, selecione **Azure Stack Edge pro e gateway do data Box**.
    4. Selecione seu **recurso**. Isso corresponde ao nome do seu pedido.
    5. Dê um breve **Resumo** do problema que você está enfrentando. 
    6. Selecione o **tipo de problema**.
    7. Com base no tipo de problema que você selecionou, escolha um **Subtipo de problema** correspondente.
    8. Selecione **Avançar: Soluções >>** .

        ![Noções básicas](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Na guia **Detalhes**, execute as seguintes etapas:

    1. Forneça a data de início e a hora para o problema.
    2. Escreva uma **Descrição** do seu problema.
    3. Em **Upload do arquivo**, selecione o ícone de pasta para procurar outros arquivos que você deseja carregar.
    4. Marque **Compartilhar informações de diagnóstico**.
    5. Com base na sua assinatura, um **Plano de suporte** é preenchido automaticamente.
    6. Na lista suspensa, selecione **Severidade**.
    7. Especifique um **Método de contato preferencial**.
    8. O **Tempo de resposta** é selecionado automaticamente com base em seu plano de assinatura.
    9. Forneça o idioma de sua preferência para o suporte.
    10. Nas **Informações de contato**, informe seu nome, email, telefone, contato opcional e país/região. O Suporte da Microsoft usará essas informações para entrar em contato com você para obter mais informações, diagnóstico e resolução. 
    11. Selecione **Avançar: Revisar + Criar >>** .

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Na guia **Revisar + Criar**, revise as informações relacionadas ao tíquete de suporte. Selecione **Criar**. 

    ![Problema 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Depois de criar o tíquete de suporte, um engenheiro de suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## <a name="get-hardware-support"></a>Obter suporte para hardware

Essas informações se aplicam somente ao dispositivo do Azure Stack. O processo para relatar problemas de hardware é o seguinte:

1. Abra um tíquete de suporte no portal do Azure para um problema de hardware. Em **Tipo de problema**, selecione **Hardware do Azure Stack**. Escolha o **Subtipo do problema** como **Falha de hardware**.

    ![Problema de hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Depois que criar um tíquete de suporte, um engenheiro de suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

2. Se Suporte da Microsoft determinar que esse é um problema de hardware, uma das seguintes ações ocorrerá:

    * Uma FRU (unidade de substituição de campo) para a peça do hardware com falha é enviada. Atualmente, as unidades de fonte de alimentação e unidades de estado sólido são as únicas FRUs com suporte.
    * Somente as FRUs são substituídas no próximo dia útil. Todo o resto exige o envio de uma FSR (substituição completa do sistema).

3. Se for determinado que uma substituição de FRU é necessária em um horário local de 1 hora (de segunda-feira a sexta-feira), um técnico no local será expedido no próximo dia útil para o seu local para executar uma substituição de FRU. Normalmente, uma substituição completa do sistema levará muito mais tempo porque as peças são enviadas de nossa fábrica e podem estar sujeitas a atrasos de transporte e alfandegário.

## <a name="manage-a-support-request"></a>Gerenciar uma solicitação de suporte

Depois de criar um tíquete de suporte, você pode gerenciar o ciclo de vida do tíquete de dentro do portal.

### <a name="to-manage-your-support-requests"></a>Para gerenciar suas solicitações de suporte

1. Para acessar a página de ajuda e suporte navegue até **Procurar -> Ajuda + suporte**.

    ![Gerenciar solicitações de suporte](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. Uma lista tabular de **Solicitações recentes de suporte** é exibida em **Ajuda + suporte**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Selecione e clique em uma solicitação de suporte. Você pode exibir o status e os detalhes para esta solicitação. Clique em **+ Nova mensagem** se desejar acompanhar esta solicitação.

## <a name="next-steps"></a>Próximas etapas

Saiba como [solucionar problemas relacionados ao Azure Stack Edge pro](azure-stack-edge-troubleshoot.md).
Saiba como [Solucionar problemas relacionados ao serviço de Data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).