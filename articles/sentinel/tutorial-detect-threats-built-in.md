---
title: Detecte ameaças com regras de análise internas no Azure Sentinel | Microsoft Docs
description: Saiba como usar regras de detecção de ameaças prontas, com base em modelos internos, que o notificam quando algo suspeito acontece.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773548"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Tutorial: Detectar ameaças prontas para uso

Depois de [conectar suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, convém ser notificado quando algo suspeito ocorrer. É por isso que o Azure Sentinel fornece modelos internos prontos para uso para ajudá-lo a criar regras de detecção de ameaças. Esses modelos foram projetados pela equipe da Microsoft de especialistas em segurança e analistas com base em ameaças conhecidas, em vetores de ataque comuns e em cadeias de escalonamento de atividades suspeitas. As regras criadas com base nesses modelos pesquisarão automaticamente em todo o seu ambiente para qualquer atividade que pareça suspeita. Muitos dos modelos podem ser personalizados para pesquisar atividades ou filtrá-los de acordo com suas necessidades. Os alertas gerados por essas regras criarão incidentes que você pode atribuir e investigar em seu ambiente.

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel:

> [!div class="checklist"]
> * Usar as detecções de ameaças prontas para uso
> * Automatizar as respostas a ameaças

## <a name="about-out-of-the-box-detections"></a>Sobre detecções prontas para uso

Para exibir todas as detecções prontas para uso, acesse **Análise** e **Modelos de regra**. Esta guia contém todas as regras internas do Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Usar detecções internas para encontrar ameaças com o Azure Sentinel":::

Os seguintes tipos de modelo estão disponíveis:

- **Segurança da Microsoft**
   
   Os modelos de segurança da Microsoft criam automaticamente incidentes do Azure Sentinel a partir dos alertas gerados em outras soluções de segurança da Microsoft, em tempo real. Você pode usar as regras de segurança da Microsoft como um modelo para criar novas regras com lógica semelhante. Para obter mais informações sobre regras de segurança, consulte [criar automaticamente incidentes de alertas de segurança da Microsoft](create-incidents-from-alerts.md).

- **Fusão** 

    Com base na tecnologia de fusão, a detecção avançada de ataques de multiestágio no Azure Sentinel usa algoritmos de aprendizado de máquina escalonáveis que podem correlacionar muitos alertas e eventos de baixa fidelidade em vários produtos em incidentes acionáveis e de alta fidelidade. A fusão é habilitada por padrão. Como a lógica está oculta e, portanto, não é personalizável, você só pode criar uma regra com esse modelo.

    > [!IMPORTANT]
    > Algumas das detecções no modelo de regra de fusão estão atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
    >
    > Para ver quais detecções estão em versão prévia, confira [detecção avançada de ataque de multiestágio no Azure Sentinel](fusion.md).

- **Análise comportamental do aprendizado de máquina**

    Esses modelos são baseados em algoritmos proprietários do Microsoft Machine Learning, portanto, você não pode ver a lógica interna de como eles funcionam e quando eles são executados. Como a lógica está oculta e, portanto, não é personalizável, você só pode criar uma regra com cada modelo desse tipo.

    > [!IMPORTANT]
    > - Os modelos de regra de análise comportamental do Machine Learning estão atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
    >
    > - Ao criar e habilitar qualquer regra com base nos modelos de análise de comportamento de ML, **você concede à Microsoft permissão para copiar dados ingeridos fora da geografia do seu espaço de trabalho do Azure Sentinel** , conforme necessário para o processamento pelos mecanismos e modelos do Machine Learning.

- **Agendado**

    As regras de análise agendadas são baseadas em consultas internas escritas por especialistas em segurança da Microsoft. Você pode ver a lógica de consulta e fazer alterações nela. Você pode usar o modelo regras agendadas e personalizar a lógica de consulta e as configurações de agendamento para criar novas regras.

## <a name="use-out-of-the-box-detections"></a>Usar as detecções prontas para uso

1. Para usar um modelo interno, clique no nome do modelo e, em seguida, clique no botão **criar regra** no painel de detalhes para criar uma nova regra ativa com base nesse modelo. Cada modelo tem uma lista de fontes de dados necessárias. Quando você abre o modelo, as fontes de dados são verificadas automaticamente quanto à disponibilidade. Se houver um problema de disponibilidade, o botão **criar regra** poderá ser desabilitado ou você poderá ver um aviso para esse efeito.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Painel de visualização de regra de detecção":::
 
1. Clicar no botão **criar regra** abre o assistente de criação de regras com base no modelo selecionado. Todos os detalhes são preenchidos de forma automática e com os modelos de segurança **agendados** ou da **Microsoft** , você pode personalizar a lógica e outras configurações de regra para atender melhor às suas necessidades específicas. Você pode repetir esse processo para criar regras adicionais com base no modelo interno. Depois de seguir as etapas no assistente de criação de regra para o final, você terá terminado de criar uma regra com base no modelo. As novas regras serão exibidas na guia **regras ativas** .

    Para obter mais detalhes sobre como personalizar suas regras no assistente de criação de regras, consulte [tutorial: criar regras de análise personalizadas para detectar ameaças](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel. 

Para saber como automatizar suas respostas a ameaças, [Configure as respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).
