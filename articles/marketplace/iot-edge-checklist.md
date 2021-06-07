---
title: Lista de verificação de pré-certificação para ofertas de módulo IoT Edge no Azure Marketplace
description: Saiba mais sobre os requisitos específicos de certificação para publicar IoT Edge ofertas de módulo no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562691"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Lista de verificação de pré-certificação para módulos de IoT Edge

> [!NOTE]
> Os editores altamente recomendados analisam essa lista de verificação e validam a funcionalidade do módulo antes de enviar para certificações. Isso acelerará seu processo de certificação, reduzindo a necessidade de alterações e reenvios.

## <a name="validation-of-image"></a>Validação da imagem

Depois que a imagem do módulo do Edge estiver pronta para envio, execute estas etapas para garantir que a imagem funcione da forma esperada pela Microsoft.

### <a name="steps-to-perform-in-the-azure-portal"></a>Etapas a serem executadas no portal do Azure

1. Abra o [portal do Azure](https://partner.microsoft.com/).
1. Crie um grupo de recursos.
1. Crie um Hub IoT.
1. Criar um dispositivo IoT Edge.
1. Copie a cadeia de conexão e salve-a no bloco de notas.
1. Selecione o conjunto de **módulos no dispositivo de borda criado**.
1. Adicione os detalhes do ACR onde a versão mais recente da imagem reside.
1. Selecione **adicionar IOT Edge módulo** e forneça:
    - O URI da imagem na configuração do módulo
    - A variável de ambiente (o mesmo que é adicionado no Partner Center)
    - As opções de criação de contêiner (as mesmas que são adicionadas no Partner Center)
    - A configuração de MyModule (o mesmo que é adicionado no Partner Center)
1. Adicionar rotas (o mesmo que é adicionado no Partner Center).
1. Selecione **Examinar + criar**.

Os módulos de borda são implantados no dispositivo de borda criado no Azure.

### <a name="steps-to-perform-on-the-device"></a>Etapas a serem executadas no dispositivo

#### <a name="device-details"></a>Detalhes do dispositivo

A equipe de certificação usa o seguinte hardware para validar imagens em diferentes arquiteturas:

- Para imagens x64, uma VM do Azure com tamanho de configuração como Standard D2s v3 executando o Ubuntu Server 18.04/Ubuntu Server 16, 4.
- Para imagens Azure Resource Manager 32, um modelo B do Raspberry Pi 3.
- Para imagens Azure Resource Manager 64, um NVIDIA Jetson Nano 4.

#### <a name="steps"></a>Etapas

1. Certifique-se de que dispositivos/VM criados possam ser acessados por meio de recriação.
1. Baixe [IOT Edge tempo de execução](../iot-edge/how-to-install-iot-edge.md) no dispositivo.
1. Atualize a cadeia de conexão copiada na etapa 5 para o arquivo config. YAML.
1. Reinicie o módulo do Edge com `sudo systemctl restart iotedge` .
1. Verifique se o módulo está implantado no dispositivo com `sudo iotedge list` ; ele deve estar em estado de execução.
1. Verifique se os logs do módulo implantados com não `sudo iotedge logs “Module Name“ -f` têm erros. Se houver erros conhecidos, descreva isso nas notas do Partner Center **para revisor** antes de enviar a oferta.

## <a name="metadata-validation"></a>Validação de metadados

Verifique o seguinte:

- A marca mais recente está listada no Partner Center e no registro de contêiner do Azure.
- O requisito mínimo de hardware é adicionado na descrição da oferta.
- O nome de usuário e a senha do registro de contêiner do Azure são atualizados e adicionados no Partner Center.
- Precisão da **Propriedade** bidesejada, *se aplicável*.
- Precisão das **variáveis ambientais** desejadas, *se aplicável*.
- Precisão das **Opções de criação** desejadas, *se aplicável*.
- A cadeia de conexão de gerenciamento de leads está presente.
- Política de privacidade presente
- Termos de uso presente
- Adicionar link de dispositivo IoT Edge com suporte do [Catálogo de dispositivos do Azure IOT](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Próximas etapas

- [Implantar módulos do Marketplace comercial](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Publicar o módulo de borda no Partner Center](./partner-center-portal/azure-iot-edge-module-creation.md)
- [Implantar o módulo IoT Edge](../iot-edge/quickstart-linux.md)