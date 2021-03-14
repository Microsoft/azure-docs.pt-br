---
title: Implantar módulos de portal do Azure Azure IoT Edge
description: Use o Hub IoT no portal do Azure para enviar por push um módulo de IoT Edge do Hub IoT para o dispositivo IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9248c9578d94b000c04c82b33eeeb089e55a26ef
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200306"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implantar módulos do Azure IoT Edge do portal do Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os.

Este artigo mostra como o portal do Azure guia você por meio da criação de um manifesto de implantação e de empurrar a implantação a um dispositivo IoT Edge. Para obter informações sobre como criar uma implantação que se destina a vários dispositivos com base em suas marcas compartilhadas, consulte [implantar e monitorar módulos de IOT Edge em escala](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure.
* Um dispositivo IoT Edge.

  Se você não tiver um dispositivo IoT Edge configurado, poderá criar um em uma máquina virtual do Azure. Siga as etapas em um dos artigos de início rápido para [criar um dispositivo Linux Virtual](quickstart-linux.md) ou [criar um dispositivo virtual do Windows](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

O portal do Azure tem um assistente que ajuda você por meio da criação de manifesto de implantação, em vez de compilar manualmente o documento JSON. Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

>[!NOTE]
>As etapas neste artigo refletem a versão de esquema mais recente do IoT Edge Agent e Hub. A versão do esquema 1,1 foi lançada junto com IoT Edge versão 1.0.10 e habilita os recursos de ordem de inicialização do módulo e priorização de rota.
>
>Se você estiver implantando em um dispositivo que executa a versão 1.0.9 ou anterior, edite as **configurações de tempo de execução** na etapa **módulos** do assistente para usar a versão de esquema 1,0.

### <a name="select-device-and-add-modules"></a>Selecionar dispositivo e adicionar módulos

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
1. No painel esquerdo, selecione **IOT Edge** no menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Na barra superior, selecione **Definir Módulos**.
1. Na seção **configurações de registro de contêiner** da página, forneça as credenciais para acessar qualquer registro de contêiner privado que contenha suas imagens de módulo.
1. Na seção **módulos IOT Edge** da página, selecione **Adicionar**.
1. Escolha um dos três tipos de módulos no menu suspenso:

   * **Módulo IOT Edge** -você fornece o nome do módulo e o URI da imagem de contêiner. Por exemplo, o URI da imagem para o módulo SimulatedTemperatureSensor de exemplo é `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se a imagem do módulo estiver armazenada em um registro de contêiner privado, adicione as credenciais nesta página para acessar a imagem.
   * **Módulo do Marketplace** -módulos hospedados no Azure Marketplace. Alguns módulos do Marketplace exigem configuração adicional, portanto, examine os detalhes do módulo na lista de [módulos de IOT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Módulo Azure Stream Analytics** -módulos gerados de uma carga de trabalho de Azure Stream Analytics.

1. Depois de adicionar um módulo, selecione o nome do módulo na lista para abrir as configurações do módulo. Preencha os campos opcionais se necessário.

   Para obter mais informações sobre as configurações de módulo disponíveis, consulte [configuração e gerenciamento de módulo](module-composition.md#module-configuration-and-management).

   Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](module-composition.md#define-or-update-desired-properties).

1. Repita as etapas de 6 a 8 para adicionar mais módulos à sua implantação.
1. Selecione **Avançar: rotas** para continuar na seção rotas.

### <a name="specify-routes"></a>Especificar Rotas

Na guia **Rotas**, defina como as mensagens são transmitidas entre os módulos e o Hub IoT. As mensagens são construídas com pares nome/valor. Por padrão, a primeira implantação para um novo dispositivo inclui uma rota chamada **rota** e definida como **de/messages/ \* para $upstream**, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Os parâmetros **Priority** e **time to Live** são parâmetros opcionais que você pode incluir em uma definição de rota. O parâmetro Priority permite que você escolha quais rotas devem ter suas mensagens processadas primeiro ou quais rotas devem ser processadas por último. A prioridade é determinada pela definição de um número 0-9, em que 0 é a prioridade mais alta. O parâmetro vida útil permite que você declare por quanto tempo as mensagens nessa rota devem ser mantidas até que sejam processadas ou removidas da fila.

Para obter mais informações sobre como criar rotas, consulte [declarar rotas](module-composition.md#declare-routes).

Depois que as rotas forem definidas, selecione **Avançar: revisar + criar** para continuar na próxima etapa do assistente.

### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Note que há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Estes dois módulos enganam o [runtime do IoT Edge](iot-edge-runtime.md) e são padrões requeridos em toda implantação.

Examine as informações de implantação e, em seguida, selecione **criar**.

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na página de detalhes do dispositivo do Hub IoT. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O [Azure Marketplace](https://azuremarketplace.microsoft.com/) é um Marketplace de aplicativos e serviços online, no qual você pode navegar por uma ampla variedade de aplicativos e soluções empresariais que são certificados e otimizados para serem executados no Azure, incluindo [módulos IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Você pode implantar um módulo IoT Edge do Azure Marketplace e de seu hub IoT.

### <a name="deploy-from-azure-marketplace"></a>Implantar do Azure Marketplace

Examinar os módulos de IoT Edge no Marketplace e quando você encontrar o que você deseja implantar, selecionando **criar** ou **obtê-lo agora**. Prossiga com as etapas do assistente de implantação que podem variar dependendo do módulo de IoT Edge selecionado:

1. Confirme os termos de uso e a política de privacidade do provedor selecionando **Continuar**. Você pode primeiro precisar fornecer informações de contato.
1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.
1. Selecione **Implantar em um dispositivo**.
1. Insira o nome do dispositivo ou selecione **Localizar dispositivo** para navegar entre os dispositivos registrados com o hub.
1. Selecione **Criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos se desejado. Os detalhes para o novo módulo são predefinidos, como a imagem do URI, as opções de criação e as propriedades desejadas, mas podem ser alterados.

Verifique se o módulo está implantado em seu hub IoT no portal do Azure. Selecione seu dispositivo, selecione **definir módulos** e o módulo deve estar listado na seção **módulos de IOT Edge** .

### <a name="deploy-from-azure-iot-hub"></a>Implantar do Hub IoT do Azure

Você pode implantar rapidamente um módulo do Azure Marketplace em seu dispositivo no Hub IoT no portal do Azure.

1. No portal do Azure, navegue até o Hub IoT.
1. No painel esquerdo, em **Gerenciamento de dispositivo automático**, selecione **IOT Edge**.
1. Selecione o dispositivo IoT Edge que deve receber a implantação.
1. Na barra superior, selecione **Definir Módulos**.
1. Na seção **módulos IOT Edge** , clique em **Adicionar** e selecione **módulo do Marketplace** no menu suspenso.

![Adicionar módulo no Hub IoT](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Escolha um módulo na página **IOT Edge do Marketplace do módulo** . O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece na lista de módulos de IoT Edge. Alguns módulos podem exigir configuração adicional.

> [!TIP]
> As informações sobre os módulos de IoT Edge do Hub IoT do Azure são limitadas. Primeiro, você pode aprender mais sobre os [módulos de IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) no Azure Marketplace.

Selecione **Avançar: rotas** e continuar com a implantação conforme descrito por [especificar rotas](#specify-routes) e [examinar a implantação](#review-deployment) anteriormente neste artigo.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-at-scale.md)
