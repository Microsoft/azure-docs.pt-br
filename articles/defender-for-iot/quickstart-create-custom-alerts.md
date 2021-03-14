---
title: Criar alertas personalizados
description: Entenda, crie e atribua alertas de dispositivo personalizados para o serviço de segurança Azure Defender para IoT.
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
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: e59bcca9561c33610170f4bc3674eef307cf0de9
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809014"
---
# <a name="create-custom-alerts"></a>Criar alertas personalizados

Usando alertas e grupos de segurança personalizados, aproveite ao máximo as informações de segurança de ponta a ponta e o conhecimento categórico do dispositivo para garantir a melhor segurança em solução de IoT.

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados?

Você conhece melhor seus dispositivos de IoT.

Para clientes que compreendem totalmente o comportamento esperado de seu dispositivo, o Defender para IoT permite traduzir essa compreensão em uma política de comportamento do dispositivo e alertar sobre qualquer desvio do comportamento normal esperado.

## <a name="security-groups"></a>Grupos de segurança

Grupos de segurança permitem que você defina grupos lógicos de dispositivos e gerencie seu estado de segurança de forma centralizada.

Esses grupos podem representar dispositivos com o hardware específico, implantados em um determinado local, ou qualquer outro grupo adequado às suas necessidades específicas de dispositivos.

Grupos de segurança são definidos por uma propriedade de marca do dispositivo gêmeo denominada **SecurityGroup**. Por padrão, cada solução de IoT no Hub IoT tem um grupo de segurança chamado **padrão**. Altere o valor da propriedade **SecurityGroup** para alterar o grupo de segurança de um dispositivo.

Por exemplo:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Use grupos de segurança para agrupar seus dispositivos em categorias lógicas. Depois de criar os grupos, atribua-os para os alertas personalizados de sua escolha, para a solução de segurança da IoT de ponta a ponta mais eficaz.

## <a name="customize-an-alert"></a>Personalizar um alerta

1. Abra o Hub IoT e selecione **Configurações** no menu **Segurança**.

1. Selecione **Alertas personalizados**.

1. Escolha um grupo de segurança que você deseja aplicar a personalização.

1. Selecione **Adicionar um alerta personalizado**.

1. Selecione um alerta personalizado na lista suspensa.

1. Edite as propriedades necessárias e selecione **OK**.

1. Escolha **SALVAR**. Sem salvar o novo alerta, o alerta é excluído na próxima vez que você fechar o Hub IoT.

## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

O Defender para IoT oferece muitos alertas que podem ser personalizados de acordo com as suas necessidades específicas. Examine a [tabela de alertas personalizáveis](concept-customizable-security-alerts.md) para obter a severidade do alerta, a fonte de dados, a descrição e as nossas etapas de correção sugeridas se e quando cada alerta for recebido.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como implantar um agente de segurança...

> [!div class="nextstepaction"]
> [Implantar um agente de segurança](how-to-deploy-agent.md)
