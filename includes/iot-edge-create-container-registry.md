---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563016"
---
## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usa a extensão do Azure IoT Tools para criar um módulo e uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](../articles/container-registry/index.yml) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure.

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor |
   | ----- | ----- |
   | Subscription | Selecione uma assinatura na lista suspensa. |
   | Resource group | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome do registro | Forneça um nome exclusivo. |
   | Location | Escolha um local perto de você. |
   | SKU | Selecione **Basic**. |

3. Selecione **Criar**.

4. Depois que o registro de contêiner for criado, navegue até ele e, no painel esquerdo, selecione **Chaves de acesso** no menu localizado em **Configurações**. 

5. Clique para Habilitar que o usuário administrador veja o **Nome de usuário** e a **Senha** do seu registro de contêiner.

6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha** e salve-os em um local conveniente. É possível usar esses valores neste tutorial para fornecer acesso ao registro de contêiner.

   ![Copie o servidor de logon, o nome de usuário e a senha para o registro de contêiner](./media/iot-edge-create-container-registry/registry-access-key.png)