---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060549"
---
Este tutorial requer os seguintes recursos do Azure:

* Hub IoT
* Conta de armazenamento
* Conta dos Serviços de Mídia do Azure
* VM do Linux no Azure, com o [runtime do IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) instalado

Para este início rápido, recomendamos que você use o [Script de instalação de recursos da Análise Dinâmica de Vídeo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar os recursos necessários em sua assinatura do Azure. Para fazer isso, siga estas etapas:

1. Abra o [Azure Cloud Shell](https://shell.azure.com).
1. Se estiver usando Cloud Shell pela primeira vez, você receberá um prompt para selecionar uma assinatura a fim de criar uma conta de armazenamento e um compartilhamento de Arquivos do Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para suas informações de sessão do Cloud Shell. Essa conta de armazenamento é separada da conta que o script criará para usar com sua conta dos Serviços de Mídia do Azure.
1. No menu suspenso no lado esquerdo da janela do Cloud Shell, selecione **Bash** como seu ambiente.

    ![Seletor de ambiente](../../../media/quickstarts/env-selector.png)
1. Execute o comando a seguir.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Após a conclusão bem-sucedida do script, você deverá ver todos os recursos necessários em sua assinatura.
1. Após a conclusão do script, clique nas chaves para expor a estrutura de pastas. Você verá alguns arquivos no diretório *~/clouddrive/lva-sample*. Os itens de interesse neste guia de início rápido são:

     * * **~/clouddrive/lva-sample/edge-deployment/.env** _ – Esse arquivo contém propriedades que o Visual Studio Code usa para implantar módulos em um dispositivo de borda.
     _ * **~/clouddrive/lva-sample/appsetting.json** _ – o Visual Studio Code usa esse arquivo para executar o código de exemplo.
     
    Você precisará desses arquivos para configurar o ambiente de desenvolvimento no Visual Studio Code na próxima seção. Talvez você queira copiá-los para um arquivo local por enquanto.
    
    ![Configurações do aplicativo](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Se você tiver problemas com os recursos do Azure que são criados, confira nosso _ *[guia de solução de problemas](../../../troubleshoot-how-to.md#common-error-resolutions)* * para resolver alguns problemas encontrados com frequência.