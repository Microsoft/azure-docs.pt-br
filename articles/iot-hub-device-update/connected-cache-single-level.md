---
title: Exemplos de cenário de implantação da versão prévia do Cache Conectado da Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutoriais de exemplos de cenário de implantação da versão prévia do Cache Conectado da Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658606"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Exemplos de cenário de implantação da versão prévia do Cache Conectado da Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Gateway do Azure IoT Edge de nível único sem proxy

O diagrama a seguir descreve o cenário que tem um gateway do Azure IoT Edge com acesso direto aos recursos da CDN e um dispositivo de folha do IoT do Azure, como um Raspberry PI, que é um dos dispositivos filho isolados da Internet do gateway do Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Atualização de dispositivo desconectado do Cache Conectado da Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Adicione o módulo de Cache Conectado da Microsoft à sua implantação de dispositivo de gateway do Azure IoT Edge no Hub IoT do Azure (confira`MCC concepts` para obter detalhes sobre como obter o módulo).
2. Adicione as variáveis de ambiente para a implantação. Veja abaixo um exemplo das variáveis de ambiente.

    **Variáveis de ambiente**
    
    | Name                 | Valor                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_ID                   | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_KEY                  | Confira a descrição da variável de ambiente acima. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Adicione as opções de criação de contêiner para a implantação. Veja abaixo um exemplo das opções de criação de contêiner.

### <a name="container-create-options"></a>Opções de criação de contêiner

```markdown
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

Para a validar se o Cache Conectado da Microsoft está funcionando corretamente, execute o comando a seguir no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway do Azure IoT Edge de nível único com proxy não autenticado de saída

Nesse cenário, há um gateway do Azure IoT Edge com acesso aos recursos da CDN por meio de um proxy não autenticado de saída. O Cache Conectado da Microsoft está sendo configurado para armazenar em cache o conteúdo de um repositório personalizado, e o relatório de resumo passou a estar visível para qualquer pessoa na rede. Veja abaixo um exemplo das variáveis de ambiente do MCC que seriam definidas.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy de nível único do Cache Conectado da Microsoft" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Adicione o módulo de Cache Conectado da Microsoft à sua implantação de dispositivo de gateway do Azure IoT Edge no Hub IoT do Azure.
2. Adicione as variáveis de ambiente para a implantação. Veja abaixo um exemplo das variáveis de ambiente.

    **Variáveis de ambiente**

    | Name                 | Valor                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_ID                   | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_KEY                  | Confira a descrição da variável de ambiente acima. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | IP do servidor proxy ou FQDN                     |

3. Adicione as opções de criação de contêiner para a implantação. Não há nenhuma diferença nas opções de criação de contêiner do MCC do exemplo anterior. Veja abaixo um exemplo das opções de criação de contêiner.

### <a name="container-create-options"></a>Opções de criação de contêiner

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

Para a validar se o Cache Conectado da Microsoft está funcionando corretamente, execute o comando a seguir no terminal do dispositivo Azure IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
