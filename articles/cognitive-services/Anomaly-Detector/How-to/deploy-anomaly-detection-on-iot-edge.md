---
title: Executar o detector de anomalias no IoT Edge
titleSuffix: Azure Cognitive Services
description: Implante o módulo detector de anomalias para IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936280"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Implantar um módulo detector de anomalias para IoT Edge

Saiba como implantar o módulo detector de [anomalias](../anomaly-detector-container-howto.md) dos serviços cognitivas em um dispositivo IOT Edge. Depois de implantado no IoT Edge, o módulo é executado em IoT Edge junto com outros módulos como instâncias de contêiner. Ele expõe exatamente as mesmas APIs que uma instância de contêiner de detector de anomalias em execução em um ambiente de contêiner padrão do Docker. 

## <a name="prerequisites"></a>Pré-requisitos

* Use uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.
* Instale a [CLI do Azure](/cli/azure/install-azure-cli).
* Um [Hub IOT](../../../iot-hub/iot-hub-create-through-portal.md) e um dispositivo [IOT Edge](../../../iot-edge/quickstart-linux.md) .

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Implantar o módulo de detecção de anomalias na borda

1. Na portal do Azure, insira o **detector de anomalias em IOT Edge** na pesquisa e abra o resultado do Azure Marketplace.
2. Ele levará você para os dispositivos de [destino do portal do Azure para IOT Edge página do módulo](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Forneça as informações necessárias a seguir.

    1. Selecione sua assinatura.

    1. Selecione o Hub IoT.

    1. Selecione **Localizar dispositivo** e localizar um dispositivo IOT Edge.

3. Selecione o botão **Criar**.

4. Selecione o módulo **AnomalyDetectoronIoTEdge** .

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Imagem da interface do usuário dos módulos de IoT Edge com o link AnomalyDetectoronIoTEdge realçado com uma caixa vermelha para indicar que este é o item a ser selecionado.":::

5. Navegue pelas **Variáveis de Ambiente** e forneça as informações a seguir.

    1.  Mantenha o valor aceitar para **Eula**.

    1. Preencha a **Cobrança** com o ponto de extremidade dos Serviços Cognitivos.

    1. Preencha a **ApiKey** com a chave de API dos Serviços Cognitivos.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Variáveis de ambiente com caixas vermelhas em torno das áreas que precisam de valores a serem preenchidos para ponto de extremidade e chave de API":::

6. Selecione **Atualizar**

7. Selecione **Avançar: rotas** para definir sua rota. Você define que todas as mensagens de todos os módulos serão enviadas ao Hub IoT do Azure.

8. Selecione **Avançar: Examinar + criar**. Você pode visualizar o arquivo JSON que define todos os módulos implantados no dispositivo do IoT Edge.
    
9. Selecione **Criar** para iniciar a implantação do módulo.

10. Depois de concluir a implantação do módulo, você voltará para a página IoT Edge do Hub IoT. Selecione seu dispositivo na lista de dispositivos do IoT Edge para ver os detalhes.

11. Role para baixo e veja os módulos listados. Verifique se o status de tempo de execução está em execução para o novo módulo. 

Para solucionar o status de tempo de execução de seu dispositivo IoT Edge, consulte o [Guia de solução de problemas](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Testar o detector de anomalias em um dispositivo IoT Edge

Você fará uma chamada HTTP para o dispositivo do Azure IoT Edge no qual o contêiner dos Serviços Cognitivos do Azure está sendo executado. O contêiner fornece APIs de ponto de extremidade baseadas em REST. Use o host, `http://<your-edge-device-ipaddress>:5000` , para APIs de módulo.

Se o dispositivo de borda ainda não permitir a comunicação de entrada na porta 5000, será necessário criar uma nova **regra de porta de entrada**. 

Para uma VM do Azure, isso pode ser definido em configurações de **máquina virtual**  >    >    >  **regra de porta de entrada** rede  >  **Adicionar regra de porta de entrada**.

Há várias maneiras de validar se o módulo está em execução. Localize o endereço *IP externo* e a porta exposta do dispositivo de borda em questão e abra seu navegador da Web favorito. Use as várias URLs de solicitação abaixo para validar se o contêiner está em execução. As URLs de solicitação de exemplo listadas abaixo são `http://<your-edge-device-ipaddress:5000` , mas o seu contêiner específico pode variar. Tenha em mente que você precisa usar o endereço *IP externo* do seu dispositivo de borda.

| URL de Solicitação | Finalidade |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | O contêiner fornece uma home page. |
| `http://<your-edge-device-ipaddress>:5000/status` | Também solicitado com GET, isso verifica se a chave de API usada para iniciar o contêiner é válida sem causar uma consulta de ponto de extremidade. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | O contêiner fornece um conjunto completo de documentação para os pontos de extremidade e um recurso **Experimentar**. Com esse recurso, é possível inserir suas configurações em um formulário HTML baseado na Web e realizar a consulta sem precisar escrever nenhum código. Após a consulta ser retornada, um exemplo de comando CURL será fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |

![Home page do contêiner](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Próximas etapas

* Examine [instalar e executar contêineres](../anomaly-detector-container-configuration.md) para extrair a imagem de contêiner e executar o contêiner
* Revise [Configurar contêineres](../anomaly-detector-container-configuration.md) para configurações
* [Saiba mais sobre o serviço de API do detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
