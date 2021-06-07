---
title: 'Guia de início rápido: enviar eventos usando a linguagem C – Hubs de Eventos do Azure'
description: 'Guia de início rápido: este artigo fornecerá instruções detalhadas para a criação de um aplicativo em C que envia eventos aos Hubs de Eventos do Azure.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85315665"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Guia de início rápido: enviar eventos aos Hubs de Eventos do Azure usando a linguagem C

## <a name="introduction"></a>Introdução
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos usando um aplicativo de console em C. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Um ambiente de desenvolvimento C. Este tutorial considera a pilha gcc em uma VM Linux do Azure com o Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Criar um namespace de Hubs de Eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de Eventos e obter as credenciais de gerenciamento que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Obtenha o valor da chave de acesso para o hub de eventos seguindo as instruções do artigo: [Obter uma cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A chave de acesso será usada no código que você escreverá posteriormente no tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Escrever código para enviar mensagens aos Hubs de Eventos
Esta seção mostra como gravar um aplicativo em C para enviar eventos ao hub de eventos. O código usa a biblioteca Proton AMQP do [projeto Apache Qpid](https://qpid.apache.org/). Isso é semelhante a usar tópicos e filas do Barramento de Serviço com AMQP do C, como é mostrado [neste exemplo](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [Documentação do Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Na [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), siga as instruções para instalar o Qpid Proton, de acordo com o ambiente.
2. Para compilar a biblioteca Proton, instale os pacotes a seguir:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Baixe a [biblioteca Qpid Proton](https://qpid.apache.org/proton/index.html) e extraia-a, por exemplo:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Crie um diretório de compilação, compile e instale:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. No seu diretório de trabalho, crie um novo arquivo chamado **sender.c** com o código a seguir. Lembre-se de substituir os valores de chave/nome de SAS, de nome do hub de eventos e de namespace. Você também deve substituir uma versão codificada em URL da chave para o **SendRule** criado anteriormente. Você pode codificar a URL [aqui](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compile o arquivo, supondo que **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Esse código usa uma janela de saída igual a 1 para forçar o envio de mensagens assim que possível. É recomendável que o aplicativo tente enviar mensagens em lote para aumentar a produtividade. Confira a [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para obter informações sobre como usar a biblioteca Qpid Proton neste e em outros ambientes e de plataformas para as quais associações são fornecidas (atualmente Perl, PHP, Python e Ruby).

Executar o aplicativo para enviar mensagens ao hub de eventos. 

Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
