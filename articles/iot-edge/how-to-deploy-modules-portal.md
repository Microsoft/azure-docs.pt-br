---
title: Implantar módulos do portal do Azure - Azure IoT Edge | Microsoft Docs
description: Usar o portal do Azure para implantar módulos em um dispositivo do IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8337c8672eb886d79b38b2a38a74037f88604497
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448554"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implantar módulos do Azure IoT Edge do portal do Azure

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os.

Este artigo mostra como o portal do Azure guia você por meio da criação de um manifesto de implantação e de empurrar a implantação a um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação voltada a vários dispositivos com base nas marcações compartilhadas, consulte [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
* Um [Dispositivo do IoT Edge](how-to-register-device-portal.md) com o tempo de execução do IoT Edge instalado.

## <a name="select-your-device"></a>Selecionar o dispositivo

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **Definir Módulos**.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que ajuda você por meio da criação de manifesto de implantação, em vez de compilar manualmente o documento JSON. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. No **configurações de registro de contêiner** seção da página, forneça as credenciais para acessar quaisquer registros de contêiner privado que contêm suas imagens de módulo.

1. No **módulos de implantação** seção da página, selecione **Add**.

1. Veja os tipos de módulo na lista suspensa:

   * **Módulo IoT Edge** - a opção padrão.
   * **Módulo Azure Stream Analytics** - apenas módulos gerados da carga de trabalho do Azure Stream Analytics.
   * **Módulo do Azure Machine Learning** - somente imagens de modelo geradas a partir de um espaço de trabalho do Azure Machine Learning.

1. Selecione o **Módulo IoT Edge**.

1. Forneça um nome para o módulo, então especifique a imagem do contêiner. Por exemplo:

   * **Nome** - tempSensor
   * **Imagem URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](module-composition.md#define-or-update-desired-properties).

1. Clique em **Salvar**.

1. Repita etapas 2-6 para adicionar módulos adicionais à sua implantação.

1. Selecione **Avançar** para continuar para a seção de rotas.

### <a name="specify-routes"></a>Especificar Rotas

Por padrão, o assistente fornece uma rota chamada **rota** e definida como **FROM /\* INTO $upstream**, que significa que quaisquer mensagens de saída por quaisquer módulos são enviadas para o seu Hub IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Note que há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [tempo de execução IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acessado pelo portal do Azure em **Criar um Recurso**.

Você pode instalar um módulo do IoT Edge por meio do Azure Marketplace ou do portal do Azure:

1. Localize um módulo e inicie o processo de implantação.

   * Portal do Azure: Encontre um módulo e selecione **Criar**.

   * Azure Marketplace:

     1. Encontre um módulo e selecione **Obter agora**.
     1. Confirme os termos de uso e a política de privacidade do provedor selecionando **Continuar**.

1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.

1. Selecione **Implantar em um dispositivo**.

1. Insira o nome do dispositivo ou selecione **Localizar dispositivo** para navegar entre os dispositivos registrados com o hub.

1. Selecione **Criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos se desejado. Os detalhes para o novo módulo são predefinidos, como a imagem do URI, as opções de criação e as propriedades desejadas, mas podem ser alterados.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)
