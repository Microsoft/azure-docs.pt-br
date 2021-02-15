---
title: 'Início Rápido: Configurar sua solução'
description: Neste início rápido, saiba como configurar sua solução de IoT de ponta a ponta usando o Azure Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: f949d52d4ba461da94153cbbd17ca5fb3eb214d7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092079"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Início Rápido: Configurar sua solução do Azure Defender para IoT

Este artigo fornece uma explicação de como realizar a configuração inicial de sua solução de segurança da IoT usando o Defender para IoT.

## <a name="what-is-defender-for-iot"></a>O que é o Defender para IoT?

O Defender para IoT fornece segurança de ponta a ponta abrangente para soluções de IoT baseadas no Azure.

Com o Defender para IoT, você pode monitorar toda a solução de IoT em um painel, identificando todos os dispositivos IoT, as plataformas IoT e os recursos de back-end no Azure.

Uma vez habilitado em seu Hub IoT, o Defender para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu Hub IoT e relacionados à solução de IoT.

Além da detecção automática de relacionamento, também é possível selecionar e escolher outros grupos de recursos do Azure para marcar como parte da solução de IoT.

Suas seleções permitem adicionar assinaturas, grupos de recursos ou recursos únicos inteiros.

Após definir todas as relações de recursos, o Defender para IoT usa o Defender para fornecer a você recomendações de segurança e alertas relacionados a esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à solução de IoT

Para adicionar novos recursos à solução de IoT, faça o seguinte:

1. Abra o **Hub IoT** no portal do Azure.
1. Em **Segurança** selecione **Visão Geral** seguido de **Configurações** e escolha **Recursos Monitorados** .
1. Selecione **Editar** e escolha os recursos monitorados que pertencem à solução de IoT.
1. Clique em **Adicionar** .

Parabéns! Você adicionou um novo grupo de recursos à solução de IoT.

Como para da solução de IoT, o Defender para IoT agora monitora os recursos recém-adicionados e identifica recomendações e alertas de segurança relevantes.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender como criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar Módulos de Segurança](quickstart-create-security-twin.md)
