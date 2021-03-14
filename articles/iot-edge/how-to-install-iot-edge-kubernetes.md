---
title: Como instalar o IoT Edge no kubernetes | Microsoft Docs
description: Saiba como instalar o IoT Edge no kubernetes usando um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201665"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge no kubernetes (versão prévia)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge pode integrar com o kubernetes usando-o como uma camada de infraestrutura resiliente e altamente disponível. Aqui está o local em que esse suporte se encaixa em uma solução de IoT Edge de alto nível:

![Introdução ao K8S](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Um bom modelo mental para essa integração é considerar o kubernetes como outro ambiente operacional IoT Edge aplicativos podem ser executados além do Linux e do Windows.

## <a name="architecture"></a>Arquitetura 
No kubernetes, IoT Edge fornece *definição de recurso personalizado* (CRD) para implantações de carga de trabalho de borda. IoT Edge Agent assume a função de um  *controlador CRD* que reconcilia o estado desejado gerenciado pela nuvem com o estado do cluster local.

O tempo de vida do módulo é gerenciado pelo Agendador kubernetes, que mantém a disponibilidade do módulo e escolhe seu posicionamento. IoT Edge gerencia a plataforma de aplicativo de borda em execução na parte superior, reconciliando continuamente o estado desejado especificado no Hub IoT com o estado no cluster de borda. O modelo de aplicativo ainda é o conhecido modelo com base em módulos e rotas IoT Edge. O controlador do agente de IoT Edge executa o modelo de aplicativo IoT Edge de tradução *automática* para construções nativas kubernetes como pods, implantações, serviços etc.

Este é um diagrama de arquitetura de alto nível:

![arquear para kubernetes](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Cada componente da implantação de borda tem como escopo um namespace kubernetes específico para o dispositivo, tornando possível compartilhar os mesmos recursos de cluster entre vários dispositivos de borda e suas implantações.

>[!NOTE]
>IoT Edge em kubernetes está em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Tutoriais e referências 

Consulte o [IOT Edge no mini-site do kubernetes Preview docs](https://aka.ms/edgek8sdoc) para obter mais informações, incluindo tutoriais e referências detalhadas.
