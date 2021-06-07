---
title: Solução de problemas do Hub IoT do Azure erro 404001 DeviceNotFound
description: Entenda como corrigir o erro 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960822"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Este artigo descreve as causas e soluções para erros de **404001 DeviceNotFound** .

## <a name="symptoms"></a>Sintomas

Durante uma comunicação de C2D (nuvem para dispositivo), como mensagem C2D, atualização de conexão ou método direto, a operação falha com o erro **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

A operação falhou porque o dispositivo não foi encontrado pelo Hub IoT. O dispositivo não está registrado ou desabilitado.

## <a name="solution"></a>Solução

Registre a ID do dispositivo que você usou e tente novamente.