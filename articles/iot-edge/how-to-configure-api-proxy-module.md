---
title: Configurar módulo de proxy de API-Azure IoT Edge | Microsoft Docs
description: Saiba como personalizar o módulo de proxy de API para hierarquias de gateway IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1070a4c8daecfedae513f2fd8738c27abfb33078
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200574"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Configurar o módulo de proxy de API para seu cenário de hierarquia de gateway (versão prévia)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

O módulo proxy de API permite que IoT Edge dispositivos enviem solicitações HTTP por meio de gateways em vez de fazer conexões diretas com os serviços de nuvem. Este artigo percorre as opções de configuração para que você possa personalizar o módulo para dar suporte aos requisitos de hierarquia do gateway.

>[!NOTE]
>Este recurso exige o IoT Edge versão 1.2, que está em versão prévia pública, que execute contêineres do Linux.

Em algumas arquiteturas de rede, IoT Edge dispositivos por trás de gateways não têm acesso direto à nuvem. Todos os módulos que tentarem se conectar aos serviços de nuvem falharão. O módulo proxy de API dá suporte a dispositivos downstream IoT Edge nessa configuração, redirecionando as conexões do módulo para percorrer as camadas de uma hierarquia de gateway, em vez disso. Ele fornece uma maneira segura para que os clientes se comuniquem com vários serviços via HTTPS sem túnel, mas encerrando as conexões em cada camada. O módulo de proxy de API atua como um módulo de proxy entre os dispositivos IoT Edge em uma hierarquia de gateway até que eles atinjam o dispositivo IoT Edge na camada superior. Nesse ponto, os serviços em execução na camada superior IoT Edge dispositivo lidam com essas solicitações e o módulo de proxy de API executa um proxy de todo o tráfego HTTP dos serviços locais para a nuvem por meio de uma única porta.

O módulo proxy de API pode habilitar muitos cenários para hierarquias de gateway, incluindo permitir que dispositivos de camada inferior recebam imagens de contêiner ou BLOBs de push para armazenamento. A configuração das regras de proxy define como os dados são roteados. Este artigo aborda várias opções de configuração comuns.

## <a name="deploy-the-proxy-module"></a>Implantar o módulo de proxy

O módulo de proxy de API está disponível no registro de contêiner da Microsoft (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

Você também pode implantar o módulo de proxy de API diretamente do Azure Marketplace: [IOT Edge proxy de API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Entender o módulo de proxy

O módulo proxy de API aproveita um proxy reverso Nginx para rotear dados por meio de camadas de rede. Um proxy é inserido no módulo, o que significa que a imagem do módulo precisa dar suporte à configuração de proxy. Por exemplo, se o proxy estiver escutando em uma determinada porta, o módulo precisará ter essa porta aberta.

O proxy começa com um arquivo de configuração padrão inserido no módulo. Você pode passar uma nova configuração para o módulo da nuvem usando seu [módulo](../iot-hub/iot-hub-devguide-module-twins.md). Além disso, você pode usar variáveis de ambiente para ativar ou desativar as definições de configuração no momento da implantação.

Este artigo se concentra primeiro no arquivo de configuração padrão e como usar variáveis de ambiente para habilitar suas configurações. Em seguida, discutiremos a personalização do arquivo de configuração no final.

### <a name="default-configuration"></a>Configuração padrão

O módulo proxy de API vem com uma configuração padrão que dá suporte a cenários comuns e permite a personalização. Você pode controlar a configuração padrão por meio de variáveis de ambiente do módulo.

Atualmente, as variáveis de ambiente padrão incluem:

| Variável de ambiente | Descrição |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Liste todas as variáveis que você pretende atualizar em uma lista separada por vírgulas. Esta etapa impede a modificação acidental das definições de configuração erradas.
| `NGINX_DEFAULT_PORT` | Altera a porta que o proxy Nginx ouve. Se você atualizar essa variável de ambiente, certifique-se de que a porta selecionada também seja exposta no módulo dockerfile e declarada como uma associação de porta no manifesto de implantação.<br><br>O padrão é 443.<br><br>Quando implantado no Azure Marketplace, a porta padrão é atualizada para 8000, para evitar conflitos com o módulo edgeHub. Para obter mais informações, consulte [minimizar portas abertas](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Endereço para rotear solicitações do Docker. Modifique essa variável no dispositivo de camada superior para apontar para o módulo do registro.<br><br>O padrão é o nome do host pai. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Endereço para rotear solicitações de registro de BLOB. Modifique essa variável no dispositivo de camada superior para apontar para o módulo de armazenamento de BLOBs.<br><br>O padrão é o nome do host pai. |

## <a name="minimize-open-ports"></a>Minimizar portas abertas

Para minimizar o número de portas abertas, o módulo proxy de API deve retransmitir todo o tráfego HTTPS (porta 443), incluindo o tráfego destinado ao módulo edgeHub. O módulo de proxy de API é configurado por padrão para rotear novamente todo o tráfego edgeHub na porta 443.

Use as etapas a seguir para configurar sua implantação para minimizar as portas abertas:

1. Atualize as configurações do módulo edgeHub para não associar na porta 443, caso contrário haverá conflitos de associação de porta. Por padrão, o módulo edgeHub associa as portas 443, 5671 e 8883. Exclua a associação da porta 443 e deixe as outras duas em vigor:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Configure o módulo de proxy de API para ligar na porta 443.

   1. Defina o valor da variável de ambiente **NGINX_DEFAULT_PORT** como `443` .
   1. Atualize as opções de criação de contêiner para ligar na porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Se você não precisar minimizar portas abertas, poderá deixar que o módulo edgeHub use a porta 443 e configurar o módulo de proxy de API para escutar em outra porta. Por exemplo, o módulo proxy de API pode escutar na porta 8000 definindo o valor da variável de ambiente **NGINX_DEFAULT_PORT** como `8000` e criando uma associação de porta para a porta 8000.

## <a name="enable-container-image-download"></a>Habilitar download de imagem de contêiner

Um caso de uso comum para o módulo de proxy de API é habilitar IoT Edge dispositivos em camadas inferiores para extrair imagens de contêiner. Esse cenário usa o [módulo do registro do Docker](https://hub.docker.com/_/registry) para recuperar imagens de contêiner da nuvem e armazená-las em cache na camada superior. O proxy de API transmite todas as solicitações HTTPS para baixar uma imagem de contêiner das camadas inferiores a serem servidas pelo módulo registro na camada superior.

Esse cenário requer que os dispositivos de IoT Edge downstream apontem para o nome de domínio `$upstream` seguido pelo número da porta do módulo de proxy de API em vez do registro de contêiner de uma imagem. Por exemplo: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Esse caso de uso é demonstrado no tutorial [criar uma hierarquia de dispositivos IOT Edge usando gateways](tutorial-nested-iot-edge.md).

Configure os seguintes módulos na **camada superior**:

* Um módulo de registro do Docker
  * Configure o módulo com um nome fácil de memorização como o *registro* e expor uma porta no módulo para receber solicitações.
  * Configure o módulo para mapear para o registro de contêiner.
* Um módulo de proxy de API
  * Configure as seguintes variáveis de ambiente:

    | Nome | Valor |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | O nome do módulo do registro e a porta aberta. Por exemplo, `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy Nginx escuta para solicitações de dispositivos downstream. Por exemplo, `8000`. |

  * Configure as seguintes opções de criação:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure o seguinte módulo em qualquer **camada inferior** para este cenário:

* Um módulo de proxy de API
  * Configure as seguintes variáveis de ambiente:

    | Nome | Valor |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy Nginx escuta para solicitações de dispositivos downstream. Por exemplo, `8000`. |

  * Configure as seguintes opções de criação:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Habilitar carregamento de BLOB

Outro caso de uso para o módulo de proxy de API é habilitar IoT Edge dispositivos em camadas inferiores para carregar BLOBs. Esse caso de uso permite a funcionalidade de solução de problemas em dispositivos de camada inferior, como carregar logs de módulo ou carregar o pacote de suporte.

Esse cenário usa o [armazenamento de BLOBs do Azure no módulo IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) na camada superior para lidar com a criação e o carregamento de BLOBs.

Configure os seguintes módulos na **camada superior**:

* Um armazenamento de BLOBs do Azure no módulo IoT Edge.
* Um módulo de proxy de API
  * Configure as seguintes variáveis de ambiente:

    | Nome | Valor |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | O nome do módulo de armazenamento de BLOBs e a porta aberta. Por exemplo, `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy Nginx escuta para solicitações de dispositivos downstream. Por exemplo, `8000`. |

  * Configure as seguintes opções de criação:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure o seguinte módulo em qualquer **camada inferior** para este cenário:

* Um módulo de proxy de API
  * Configure as seguintes variáveis de ambiente:

    | Nome | Valor |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy Nginx escuta para solicitações de dispositivos downstream. Por exemplo, `8000`. |

  * Configure as seguintes opções de criação:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Use as etapas a seguir para carregar o pacote de suporte ou o arquivo de log para o módulo de armazenamento de BLOBs localizado na camada superior:

1. Crie um contêiner de BLOBs usando Gerenciador de Armazenamento do Azure ou as APIs REST. Para obter mais informações, consulte [armazenar dados na borda com o armazenamento de BLOBs do Azure em IOT Edge](how-to-store-data-blob.md).
1. Solicite um upload de log ou suporte de pacote de acordo com as etapas em [recuperar logs de implantações IOT Edge](how-to-retrieve-iot-edge-logs.md), mas use o nome de domínio `$upstream` e a porta de proxy aberta no lugar do endereço do módulo de armazenamento de BLOBs. Por exemplo:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Editar a configuração de proxy

Um arquivo de configuração padrão é inserido no módulo proxy de API, mas você pode passar uma nova configuração para o módulo por meio da nuvem usando o módulo inserir.

Ao escrever sua própria configuração, você ainda pode usar o ambiente para ajustar as configurações por implantação. Use a seguinte sintaxe:

* Use `${MY_ENVIRONMENT_VARIABLE}` para recuperar o valor de uma variável de ambiente.
* Use instruções condicionais para ativar ou desativar as configurações com base no valor de uma variável de ambiente:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Quando o módulo de proxy de API analisa uma configuração de proxy, ele primeiro substitui todas as variáveis de ambiente listadas no `PROXY_CONFIG_ENV_VAR_LIST` com seus valores fornecidos usando a substituição. Em seguida, tudo entre `#if_tag` um `#endif_tag` par e é substituído. Em seguida, o módulo fornece a configuração analisada para o proxy reverso nginx.

Para atualizar a configuração de proxy dinamicamente, use as seguintes etapas:

1. Escreva o arquivo de configuração. Você pode usar este modelo padrão como uma referência: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Copie o texto do arquivo de configuração e converta-o em base64.
1. Cole o arquivo de configuração codificado como o valor da `proxy_config` propriedade desejada no módulo "copiar".

   ![Colar arquivo de configuração codificado como valor da propriedade proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Próximas etapas

Use o módulo proxy de API para [conectar um dispositivo de IOT Edge downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md).