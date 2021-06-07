---
title: 'Início rápido: adicionar recursos do Azure à solução de IoT'
description: Neste início rápido, saiba como configurar sua solução de IoT de ponta a ponta usando o Azure Defender para IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 6a90e8c3007f7b3448fd3f1b6e4fa46ba861081b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384573"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Início Rápido: Configurar sua solução do Azure Defender para IoT

Este artigo fornece uma explicação de como realizar a configuração inicial de sua solução de segurança da IoT usando o Defender para IoT.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum

## <a name="what-is-defender-for-iot"></a>O que é o Defender para IoT?

O Defender para IoT fornece segurança de ponta a ponta abrangente para soluções de IoT baseadas no Azure.

Com o Defender para IoT, você pode monitorar toda a solução de IoT em um só painel, expondo todos os dispositivos IoT, as plataformas IoT e os recursos de back-end no Azure.

Uma vez habilitado em seu Hub IoT, o Defender para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu Hub IoT e relacionados à solução de IoT.

Além da detecção automática de relacionamento, também é possível selecionar e escolher outros grupos de recursos do Azure para marcar como parte da solução de IoT.

Suas seleções permitem adicionar assinaturas, grupos de recursos ou recursos únicos inteiros.

Depois de definir todas as relações de recursos, o Defender para IoT usa o Defender para fornecer a você recomendações de segurança e alertas relacionados a esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à solução de IoT

Para adicionar um novo recurso à solução de IoT:

1. Abra o **Hub IoT** no portal do Azure.

1. Em **Segurança** selecione **Visão Geral** seguido de **Configurações** e escolha **Recursos Monitorados**.

1. Selecione **Editar** e escolha os recursos monitorados que pertencem à solução de IoT.

1. Selecione **Adicionar**.

Parabéns! Você adicionou um novo grupo de recursos à solução de IoT.

Como para da solução de IoT, o Defender para IoT agora monitora os recursos recém-adicionados e identifica recomendações e alertas de segurança relevantes.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como criar microagentes do Defender para IoT...

> [!div class="nextstepaction"]
> [Criar microagentes do Defender para IoT](quickstart-create-security-twin.md)
