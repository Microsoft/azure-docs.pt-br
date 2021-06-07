---
title: Programa Azure Certified Device – Tutorial – Como selecionar o programa de certificação
description: Guia passo a passo para selecionar os programas de certificação certos para seu dispositivo
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: b6ab8a4f971a065764731abddf72e26c628ba6cb
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975395"
---
# <a name="tutorial-select-your-certification-program"></a>Tutorial: Como selecionar o programa de certificação

Parabéns por escolher o programa Azure Certified Device! Estamos animados por ter você em nosso ecossistema de dispositivos certificados. Para começar, primeiro, você precisa determinar quais programas de certificação melhor se adaptam às funcionalidades do seu dispositivo.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Selecionar os melhores programas e certificação para seu dispositivo

## <a name="selecting-a-certification-program-for-your-device"></a>Como selecionar um programa de certificação para seu dispositivo

Todos os dispositivos são necessários para atender aos requisitos de linha de base descritos pela certificação do [**Azure Certified Device**](./program-requirements-azure-certified-device.md). Os outros três selos de certificação se baseiam nesse programa e oferecem um valor diferente do cliente. Escolha um ou mais dos três selos incrementais (IoT Plug and Play, Gerenciado por Borda e Núcleo Seguro de Borda *versão prévia).

1. Examine cada um dos requisitos dos programas de certificação na tabela abaixo. Os requisitos detalhados de cada programa podem ser selecionados nos cabeçalhos.

    |Requisito|[IoT Plug and Play](./program-requirements-edge-secured-core.md)|[Gerenciado por Borda](./program-requirements-edge-managed.md)|[Núcleo Seguro de Borda](./program-requirements-edge-secured-core.md)|
    ---|---|---|---
    | Processador | Qualquer|MPU/CPU|MPU/CPU|
    | Sistema operacional | Qualquer|[Sistema operacional da camada 1](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|[Sistema operacional da camada 1](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    | runtime do IoT Edge | Sem suporte |Obrigatório|Obrigatório|
    | Defender para IoT | Sem suporte|Obrigatório|Obrigatório|
    | ADU/Windows Update | Sem suporte|Obrigatório|Obrigatório|

1. Quando você achar que entende o que é necessário para o seu dispositivo, examine os requisitos técnicos do programa. Essa pode ser a certificação do Azure Certified Device ou uma combinação da certificação de linha de base com um dos três selos incrementais. 

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para começar a certificar seu dispositivo. Avance para o próximo artigo para começar seu projeto.
> [!div class="nextstepaction"]
>[Tutorial: Como criar seu projeto](tutorial-01-creating-your-project.md)
