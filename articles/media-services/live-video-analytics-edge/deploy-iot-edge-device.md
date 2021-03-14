---
title: Implantar a análise de vídeo ao vivo em um dispositivo IoT Edge – Azure
description: Este artigo lista as etapas que ajudarão você a implantar a análise de vídeo ao vivo em seu dispositivo IoT Edge. Você faria isso, por exemplo, se tiver acesso a um computador Linux local e/ou tiver criado anteriormente uma conta dos serviços de mídia do Azure.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 01b98c7a1f4073adcd8dea7cbfbfc57abc3787c1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718923"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Implantar a análise de vídeo ao vivo em um dispositivo IoT Edge

Este artigo lista as etapas que ajudarão você a implantar a análise de vídeo ao vivo em seu dispositivo IoT Edge. Você faria isso, por exemplo, se tiver acesso a um computador Linux local e/ou tiver criado anteriormente uma conta dos serviços de mídia do Azure.

> [!NOTE]
> O suporte para dispositivos ARM64 está disponível em análise de vídeo ao vivo em IoT Edge Builds `1.0.4` e mais recentes.
> O suporte para executar o runtime do Azure IoT Edge em dispositivos ARM64 está em [versão prévia pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo x86-64 ou ARM64 executando um dos [sistemas operacionais Linux com suporte](../../iot-edge/support.md#operating-systems)
* Assinatura do Azure para a qual você tem [privilégios de proprietário](../../role-based-access-control/built-in-roles.md#owner)
* [Criar e configurar o Hub IoT](../../iot-hub/iot-hub-create-through-portal.md)
* [Registrar IoT Edge dispositivo](../../iot-edge/how-to-register-device.md)
* [Instalar o tempo de execução de Azure IoT Edge em sistemas Linux baseados em Debian](../../iot-edge/how-to-install-iot-edge.md)
* [Criar uma conta dos serviços de mídia do Azure](../latest/create-account-howto.md)

    * Use uma destas regiões: leste dos EUA 2, leste dos EUA, EUA Central, norte EUA Central, leste do Japão, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central, leste do Canadá, Sul do Reino Unido, França central, sul da França, Norte da Suíça, Oeste da Suíça e oeste do Japão.
    * É recomendável que você use contas de armazenamento de uso geral v2 (GPv2)

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Configurando recursos do Azure para usar a análise de vídeo ao vivo

### <a name="create-custom-azure-resource-manager-role"></a>Criar função personalizada do Azure Resource Manager

Consulte [criar função de Azure Resource Manager personalizada](create-custom-azure-resource-manager-role-how-to.md) e atribuí-la a uma entidade de serviço para uso de análise de vídeo ao vivo.

### <a name="set-up-a-premium-streaming-endpoint"></a>Configurar um ponto de extremidade de streaming Premium

Se você pretende usar a análise de vídeo ao vivo para gravar vídeo continuamente na nuvem e, subsequentemente, usar [APIs de consulta](playback-recordings-how-to.md#query-api) antes de reproduzi-lo novamente, é recomendável atualizar seu serviço de mídia para usar um [ponto de extremidade de streaming Premium](../latest/streaming-endpoint-concept.md#types).  

Esta é uma etapa opcional. Você pode usar este CLI do Azure comando para fazer isso:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Você pode usar este comando para iniciar o ponto de extremidade de streaming 

> [!IMPORTANT]
> Sua assinatura começará a ser cobrada neste ponto.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Siga as etapas neste artigo para obter credenciais para acessar as APIs do serviço de mídia: [acesse as APIs do serviço de mídia](../latest/access-api-howto.md?tabs=portal) e selecione a guia Portal.

## <a name="create-and-use-local-user-account-for-deployment"></a>Criar e usar a conta de usuário local para implantação
Para executar o módulo análise de vídeo ao vivo no IoT Edge, crie uma conta de usuário local com o menor número de privilégios possível. Por exemplo, execute os seguintes comandos em seu computador Linux:

```
sudo groupadd -g 1010 localusergroup
sudo useradd --home-dir /home/edgeuser --uid 1010 --gid 1010 lvaedgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Concedendo permissões ao armazenamento do dispositivo

Agora que você criou uma conta de usuário local, 

* Você precisará de uma pasta local para armazenar os dados de configuração do aplicativo. Crie uma pasta e conceda permissões para a conta LocalUser Write nessa pasta usando os seguintes comandos:

```
sudo mkdir -p /var/lib/azuremediaservices
sudo chown -R lvaedgeuser /var/lib/azuremediaservices
```

* Você também precisará de uma pasta para [gravar vídeos em um arquivo local](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Use os comandos a seguir para criar uma pasta local para o mesmo:

```
sudo mkdir -p /var/media
sudo chown -R lvaedgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Implantar módulo de borda de análise de vídeo ao vivo

A análise de vídeo ao vivo em IoT Edge expõe as propriedades de módulo '/' que estão documentadas no [esquema de configuração do módulo](module-twin-configuration-schema.md)/. 

### <a name="deploy-using-the-azure-portal"></a>Implantar usando o portal do Azure

O portal do Azure orienta você durante a criação de um manifesto de implantação e o envio por push da implantação para um dispositivo IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Selecione seu dispositivo e defina os módulos

1. Entre no [Portal do Azure](https://ms.portal.azure.com/) e navegue até o Hub IoT.
1. Selecionar **IoT Edge** do menu.
1. Clique no ID do dispositivo alvo da lista de dispositivos.
1. Selecione **definir módulos**.

#### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal do Azure tem um assistente que orienta você durante a criação de um manifesto de implantação. Ele tem três etapas organizadas em guias: **módulos**, **rotas** e **revisão + criar**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de IOT Edge** da página, clique na lista **suspensa adicionar** e selecione **IOT Edge módulo** para exibir a página **Adicionar módulo de IOT Edge** .
1. Na guia **configurações do módulo** , forneça um nome para o módulo e, em seguida, ESPECIFIQUE o URI da imagem de contêiner:   
    Exemplos:
    
    * **Nome do módulo de IOT Edge**: lvaEdge
    * **URI da imagem**: MCR.Microsoft.com/Media/Live-Video-Analytics:2.0    
    
    ![Captura de tela mostra a guia Configurações do módulo.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Não selecione **Adicionar** até que você tenha especificado valores nas guias **configurações do módulo**, opções de criação do **contêiner** e configurações do **módulo de configuração** , conforme descrito neste procedimento.
    
    > [!WARNING]
    > Azure IoT Edge diferencia maiúsculas de minúsculas quando você faz chamadas para módulos. Anote a cadeia de caracteres exata usada como o nome do módulo.

1. Abra a guia **variáveis de ambiente** .
   
   Adicione os seguintes valores nas caixas de entrada que você vê ![ variáveis de ambiente](./media/deploy-iot-edge-device/environment-variables.png) 

1. Abra a guia **Opções de criação do contêiner** .

    ![Opções de criação de contêiner](./media/deploy-iot-edge-device/container-create-options.png)
 
    Copie e cole o JSON a seguir na caixa, para limitar o tamanho dos arquivos de log produzidos pelo módulo.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   A seção "Bindes" no JSON tem duas entradas:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": é usado para associar os dados de configuração de aplicativo persistentes do contêiner e armazená-los no dispositivo de borda.
   1. "/var/media:/var/media": isso associa as pastas de mídia entre o dispositivo de borda e o contêiner. Isso é usado para armazenar as gravações de vídeo quando você executa uma topologia de mídia gráfica que dá suporte ao armazenamento de clipes de vídeo no dispositivo de borda.
   
1. Na guia **configurações** de cópia do módulo, copie o JSON a seguir e cole-o na caixa.
 
    ![Configurações de configuração de entrelaçar](./media/deploy-iot-edge-device/twin-settings.png)

    A análise de vídeo ao vivo em IoT Edge requer um conjunto de propriedades de FileUp obrigatórias para ser executado, conforme listado no [esquema de configuração do módulo](module-twin-configuration-schema.md)"r".  

    O JSON que você precisa inserir na caixa de edição de configurações do módulo inserir terá a seguinte aparência:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Essas são propriedades **obrigatórias** e para o JSON acima,  
    * {SubscriptionId}-esta é sua ID de assinatura do Azure
    * {resourceGroupName}-este grupo de recursos ao qual a conta do serviço de mídia pertence
    * {AMS-Account-Name}-este é o nome da sua conta de serviços de mídia
    
    Para obter os outros valores, consulte [acessar a API dos serviços de mídia do Azure](../latest/access-api-howto.md?tabs=portal) e selecione a guia Portal.  
    * aadTenantId-esta é a ID do seu locatário e é o mesmo que "AadTenantId" do link acima.
    * aadServicePrincipalAppId-esta é a ID do aplicativo da entidade de serviço para sua conta de serviço de mídia e é a mesma que a "AadClientId" do link acima.
    * aadServicePrincipalSecret-essa é a senha da entidade de serviço e é a mesma que a "AadSecret" do link acima.

    Abaixo estão algumas propriedades adicionais **recomendadas** que podem ser adicionadas ao JSON e ajudarão a monitorar o módulo. Para obter mais informações, consulte [monitoramento e registro em log](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    A seguir, algumas propriedades **opcionais** que você pode adicionar ao JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > A propriedade **biallowUnsecuredEndpoints** é definida como true para a finalidade dos tutoriais e dos guias de início rápido.   
   Você deve definir essa propriedade como **false** ao executar no ambiente de produção. Isso garantirá que o aplicativo bloqueará todos os pontos de extremidade não seguros e, para executar as topologias do grafo, serão necessárias credenciais de conexão válidas.  
   
    Selecione Adicionar para adicionar as propriedades do módulo.
1. Selecione **Avançar: rotas** para continuar na seção rotas.
    Especifique as rotas.

Mantenha as rotas padrão e selecione **Avançar: revisar + criar** para continuar na seção revisão.

#### <a name="review-deployment"></a>Rever implantação

A seção de revisão mostra o manifesto de implantação JSON que foi criado baseado nas suas seleções nas duas seções anteriores. Também há dois módulos declarados que você não adicionou: $edgeAgent e $edgeHub. Estes dois módulos enganam o runtime do IoT Edge e são padrões requeridos em toda implantação.

Examine as informações de implantação e, em seguida, selecione criar.

### <a name="verify-your-deployment"></a>Verificar sua implantação

Depois de criar a implantação, você retorna para a página de IoT Edge do Hub IoT.

1. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes.
2. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação e Relatado por dispositivo**.

Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.
Código de status: 200 – OK significa que [o tempo de execução do IOT Edge](../../iot-edge/iot-edge-runtime.md) está íntegro e funcionando bem.

![Captura de tela mostra um valor de status para um tempo de execução IoT Edge.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Invocar um método direto

Em seguida, vamos testar o exemplo invocando um método direto. Leia [métodos diretos para análise de vídeo ao vivo em IOT Edge](direct-methods.md) para entender todos os métodos diretos fornecidos pelo nosso módulo lvaEdge.

1. Ao clicar no módulo de borda criado, você será levado para sua página de configuração.  

    ![Captura de tela mostra a página de configuração de um módulo de borda.](./media/deploy-iot-edge-device/modules.png)
1. Clique na opção de menu método direto.

    > [!NOTE] 
    > Você precisará adicionar um valor nas seções cadeia de conexão como pode ver na página atual. Você não precisa ocultar ou alterar nada na seção nome da **configuração** . Não há problema em deixá-lo público.

    ![Método direto](./media/deploy-iot-edge-device/module-details.png)
1. Em seguida, digite "GraphTopologyList" na caixa nome do método.
1. Em seguida, copie e cole a carga JSON abaixo na caixa carga.
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. Clique na opção "invocar método" na parte superior da página

    ![Métodos diretos](./media/deploy-iot-edge-device/direct-method.png)
1. Você deverá ver uma mensagem de status 200 na caixa resultado

    ![A mensagem de status 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Próximas etapas

Experimente o [início rápido: introdução-análise de vídeo ao vivo em IOT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Se você continuar com o início rápido acima, ao invocar os métodos diretos usando Visual Studio Code, você usará o dispositivo que foi adicionado ao Hub IoT por este artigo, em vez do padrão `lva-sample-device` .