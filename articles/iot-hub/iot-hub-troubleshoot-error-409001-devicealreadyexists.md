---
title: Solução de problemas do Hub IoT do Azure erro 409001 DeviceAlreadyExists
description: Entenda como corrigir o erro 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960783"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Este artigo descreve as causas e soluções para erros de **409001 DeviceAlreadyExists** .

## <a name="symptoms"></a>Sintomas

Ao tentar registrar um dispositivo no Hub IoT, a solicitação falha com o erro **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Já existe um dispositivo com a mesma ID de dispositivo no Hub IoT. 

## <a name="solution"></a>Solução

Use uma ID de dispositivo diferente e tente novamente.