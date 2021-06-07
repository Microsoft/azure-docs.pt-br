---
title: Criar um dispositivo simulado personalizado – Azure | Microsoft Docs
description: Neste tutorial, você usará a Simulação de Dispositivo a fim de criar um dispositivo simulado personalizado para uso em simulações.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: ed83fdde4e005b0db76b9edf30faef5e8c732998
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714030"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Tutorial: Criar um dispositivo simulado personalizado

Neste tutorial, você usará a Simulação de Dispositivo a fim de criar um dispositivo simulado personalizado para uso em simulações. Para começar a usar a Simulação de Dispositivo, use um dos dispositivos simulados de exemplo incluídos aqui. Também é possível criar dispositivos simulados personalizados seguindo a descrição deste artigo. Para obter mais opções de personalização, confira [Criar um modelo de dispositivo avançado](iot-accelerators-device-simulation-advanced-device.md).

Neste tutorial, você:

>[!div class="checklist"]
> * Exibir uma lista de seus modelos de dispositivo simulado
> * Criar um dispositivo simulado personalizado
> * Clonar um modelo de dispositivo
> * Excluir um modelo de dispositivo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para seguir este tutorial, você precisará de uma instância implantada da Simulação de Dispositivo em sua assinatura do Azure.

Se você ainda não implantou a Simulação de Dispositivo, confira [Implantação da Simulação de Dispositivo](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) no GitHub.

## <a name="view-your-device-models"></a>Exibir seus modelos de dispositivo

Selecione **Modelos de dispositivo** na barra de menus. A página **Modelos de dispositivo** lista os modelos de dispositivo disponíveis nesta instância da Simulação de Dispositivo:

![Modelos de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Criar um modelo de dispositivo

Clique em **+ Adicionar Modelos de Dispositivo** no canto superior direito da página:

![Adicionar modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

Neste tutorial, você criará um refrigerador simulado que envia dados de temperatura e umidade.

Preencha o formulário com as seguintes informações:

| Configuração             | Valor                                                |
| ------------------- | ---------------------------------------------------- |
| Nome do modelo do dispositivo   | Refrigerador                                         |
| Descrição do modelo   | Um refrigerador com sensores de temperatura e umidade |
| Versão             | 1.0                                                  |

> [!NOTE]
> O nome do modelo do dispositivo deve ser exclusivo.

Clique em **+ Adicionar ponto de dados** para adicionar pontos de dados de temperatura e umidade com os seguintes valores:

| Ponto de dados          | Comportamento        | Valor mínimo | Valor máximo | Unidade |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatura         | Aleatório          | -50       | 100       | F    |
| Umidade            | Aleatório          | 0         | 100       | %    |

Clique em **Salvar** para salvar o modelo do dispositivo.

![Criar modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Agora, o refrigerador está incluído na lista de modelos de dispositivo. Talvez seja necessário clicar em **Avançar** para acessar outra página e ver seu refrigerador.

## <a name="clone-a-device-model"></a>Clonar um modelo de dispositivo

A clonagem de um modelo de dispositivo permite a criação de uma cópia de um modelo de dispositivo existente. Em seguida, você pode editar a cópia a fim de atender às suas necessidades específicas. A clonagem economiza tempo quando você precisa criar modelos de dispositivos semelhantes.

Para clonar um modelo de dispositivo, marque a caixa ao lado do modelo e clique em **Clonar** na barra de ações:

![Captura de tela que realça o modelo selecionado e o botão Clonar.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Excluir um modelo de dispositivo

Você pode excluir qualquer modelo de dispositivo personalizado. Para excluir um modelo de dispositivo, marque a caixa ao lado do modelo e clique em **Excluir** na barra de ações:

![Excluir modelo de dispositivo](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar, clonar e excluir modelos de dispositivo personalizados. Para saber mais sobre modelos de dispositivo, consulte o seguinte artigo de instruções:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo avançado](iot-accelerators-device-simulation-advanced-device.md)