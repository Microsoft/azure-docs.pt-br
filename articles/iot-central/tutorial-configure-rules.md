---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 56ced4f5e2fd0fbf829f72cff2413998398a7a09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066010"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Tutorial: Configurar regras e ações para o seu dispositivo no Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de ar-condicionado conectado excede 90&deg; F.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir o tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md) para criar o modelo do dispositivo de **Ar-condicionado conectado** que será usado.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma regra baseada em telemetria ao aplicativo, no menu de navegação esquerdo, selecione **Modelos de Dispositivo**:

    ![Página Modelos de Dispositivo](media/tutorial-configure-rules/templatespage1.png)

    Você verá o modelo de dispositivo **Ar-condicionado Conectado (1.0.0)** criado no tutorial anterior.

2. Para personalizar seu modelo de serviço, selecione o modelo de **Ar-Condicionado Conectado** criado no tutorial anterior.

3. Para adicionar uma regra baseada em telemetria no modo de exibição de **Regras**, selecione **Regras**, **+ Nova Regra** e depois selecione **Telemetria**:

    ![Modo de exibição de regras](media/tutorial-configure-rules/newrule.png)

5. Para definir a sua regra, use as informações na tabela a seguir:

    | Configuração                                      | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | NOME                                         | Alerta de temperatura do ar-condicionado |
    | Habilitar regra para todos os dispositivos deste modelo | Por                                |
    | Condição                                    | A temperatura é maior que 90    |
    | Agregação                                  | Nenhum                              |

    ![Condição de regra de temperatura](media/tutorial-configure-rules/temperaturerule.png)

    Em seguida, selecione **Salvar**.

## <a name="add-an-action"></a>Adicionar uma ação

Quando você definir uma regra, você também define uma ação a ser executada quando as condições da regra são atendidas. Neste tutorial, você criará uma regra com uma ação que envia uma notificação por email.

1. Para adicionar uma **Ação**, primeiro **Salve** a regra e, em seguida, role a tela para baixo no painel **Configurar Regra de Telemetria**. Escolha o **+** ao lado de **Ações** e, em seguida, escolha **Email**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules/addaction.png)

2. Para definir a sua ação, use as informações na tabela a seguir:

    | Configuração   | Valor                          |
    | --------- | ------------------------------ |
    | Para        | Seu endereço de email             |
    | Observações     | A temperatura do ar-condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer.md), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    ![Ação de temperatura](media/tutorial-configure-rules/temperatureaction.png)

3. Clique em **Salvar**. A regra é listada na página **Regras**.

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra são atendidas, o aplicativo envia uma mensagem para o endereço de email especificado na ação.

> [!NOTE]
> Depois que os testes forem concluídos, desligue a regra para interromper o recebimento de alertas na caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é [Personalizar as exibições do operador](tutorial-customize-operator.md).

Para saber mais sobre os diferentes tipos de regras no Azure IoT Central e como parametrizar a definição de regra, consulte:
* [Criar uma regra de telemetria e configurar as notificações](howto-create-telemetry-rules.md).
* [Criar uma regra de evento e configurar as notificações](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->