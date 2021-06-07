---
title: Solução de problemas-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Solução de problemas na grade de eventos no IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171339"
---
# <a name="common-issues"></a>Problemas comuns

Se você tiver problemas ao usar a grade de eventos do Azure em IoT Edge em seu ambiente, use este artigo como um guia para solução de problemas e resolução.

## <a name="view-event-grid-module-logs"></a>Exibir logs do módulo de grade de eventos

Para solucionar problemas, talvez seja necessário acessar os logs do módulo de grade de eventos. Para fazer isso, na VM em que o módulo é implantado, execute o seguinte comando:

No Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

No Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Não é possível fazer solicitações HTTPS

* Primeiro, verifique se o módulo de grade de eventos tem **entrada: serverAuth: tlsPolicy** definido como **estrito** ou **habilitado**.

* Se suas comunicações módulo-para-módulo, certifique-se de que você está fazendo a chamada na porta **4438** e o nome do módulo corresponde ao que está implantado. 

  Por exemplo, se o módulo de grade de eventos foi implantado com o nome **eventgridmodule** , a URL deve ser **https://eventgridmodule:4438** . Verifique se o número da porta e a embalagem estão corretos.
    
* Se ele for de um módulo não IoT, verifique se a porta da grade de eventos está mapeada na máquina host durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Não é possível fazer solicitações HTTP

* Primeiro, verifique se o módulo de grade de eventos tem **entrada: serverAuth: tlsPolicy** definido como **habilitado** ou **desabilitado**.

* Se suas comunicações módulo-para-módulo, certifique-se de que você está fazendo a chamada na porta **5888** e o nome do módulo corresponde ao que está implantado. 

  Por exemplo, se o módulo de grade de eventos foi implantado com o nome **eventgridmodule** , a URL deve ser **http://eventgridmodule:5888** . Verifique se o número da porta e a embalagem estão corretos.
    
* Se ele for de um módulo não IoT, verifique se a porta da grade de eventos está mapeada na máquina host durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>A cadeia de certificados foi emitida por uma autoridade que não é confiável

Por padrão, o módulo de grade de eventos está configurado para autenticar clientes com certificado emitido pelo daemon de segurança IoT Edge. Verifique se o cliente está apresentando um certificado com raiz para esta cadeia.

A classe **IoTSecurity** no [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) mostra como recuperar certificados do daemon de segurança IOT Edge e usá-los para configurar chamadas de saída.

Se não for um ambiente de produção, você terá a opção de desativar a autenticação do cliente. Consulte [segurança e autenticação](security-authentication.md) para obter detalhes sobre como fazer isso.

## <a name="debug-events-not-received-by-subscriber"></a>Eventos de depuração não recebidos pelo assinante

Os motivos típicos para isso são:

* O evento nunca foi Postado com êxito. Um StatusCode HTTP de 200 (OK) deve ser recebido ao postar um evento no módulo de grade de eventos.

* Verifique a assinatura do evento para verificar:
    * A URL do ponto de extremidade é válida
    * Todos os filtros na assinatura não estão fazendo com que o evento seja "descartado".

* Verificar se o módulo do assinante está em execução

* Faça logon na VM em que o módulo de grade de eventos é implantado e exiba seus logs.

* Ative por log de entrega definindo **Broker: logDeliverySuccess = true** e reimplantando o módulo de grade de eventos e repetindo a solicitação. A ativação do registro em log por entrega pode afetar a taxa de transferência e a latência, assim, depois que a depuração for concluída, nossa recomendação será voltar para **Broker: logDeliverySuccess = false**  e reimplantar o módulo de grade de eventos.

* Ative as métricas definindo **métricas: reportType = console** e reimplante o módulo de grade de eventos. Qualquer operação depois disso fará com que as métricas sejam registradas no console do módulo de grade de eventos, que pode ser usado para depurar ainda mais. Nossa recomendação é ativar as métricas somente para depuração e, uma vez concluída, desativá-las definindo **métricas: reportType = None** e reimplantating Event grade Module.

## <a name="next-steps"></a>Próximas etapas

Relate quaisquer problemas, sugestões com o uso da grade de eventos em IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .