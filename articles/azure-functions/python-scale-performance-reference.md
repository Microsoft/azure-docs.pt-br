---
title: Melhorar o desempenho da taxa de transferência de aplicativos Python no Azure Functions
description: Saiba como desenvolver Azure Functions aplicativos usando Python que são altamente com alto desempenho e dimensionam bem sob carga.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786099"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Melhorar o desempenho da taxa de transferência de aplicativos Python no Azure Functions

Ao desenvolver para Azure Functions usando o Python, você precisa entender como suas funções são executadas e como esse desempenho afeta a maneira como seu aplicativo de funções é dimensionado. A necessidade é mais importante ao criar aplicativos altamente com alto desempenho. Os principais fatores a serem considerados ao criar, gravar e configurar seus aplicativos de funções são as configurações de escala horizontal e desempenho da taxa de transferência.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal
Por padrão, o Azure Functions monitora automaticamente a carga em seu aplicativo e cria instâncias de host adicionais para Python conforme a necessidade. Azure Functions usa limites internos para diferentes tipos de gatilhos para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para QueueTrigger. Esses limites não são configuráveis pelo usuário. Para obter mais informações, consulte [dimensionamento controlado por eventos em Azure Functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Melhorando o desempenho da taxa de transferência

As configurações padrão são adequadas para a maioria dos aplicativos Azure Functions. No entanto, você pode melhorar o desempenho da taxa de transferência de seus aplicativos empregando configurações com base em seu perfil de carga de trabalho. A primeira etapa é entender o tipo de carga de trabalho que você está executando.

| Tipo de carga de trabalho | Características do aplicativo de funções       | Exemplos                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **Associado à e/s**     | • O aplicativo precisa lidar com muitas invocações simultâneas.<br>• O aplicativo processa um grande número de eventos de e/s, como chamadas de rede e leitura/gravação em disco. | • APIs Web                                          |
| **Associado à CPU**     | • O aplicativo faz cálculos de execução longa, como o redimensionamento de imagem.<br>• O aplicativo faz a transformação de dados.                                                | • Processamento de dados<br>• Inferência de Machine Learning<br> |

 
Como as cargas de trabalho de função do mundo real geralmente são uma combinação de e/s e CPU associada, você deve criar o perfil do aplicativo em cargas de produção realistas.


### <a name="performance-specific-configurations"></a>Configurações específicas de desempenho

Depois de entender o perfil de carga de trabalho do seu aplicativo de funções, veja a seguir as configurações que você pode usar para melhorar o desempenho da taxa de transferência de suas funções.

* [Async](#async)
* [Trabalho de vários idiomas](#use-multiple-language-worker-processes)
* [Máximo de trabalhadores dentro de um processo de trabalho de idioma](#set-up-max-workers-within-a-language-worker-process)
* [Loop de eventos](#managing-event-loop)
* [Dimensionamento vertical](#vertical-scaling)



#### <a name="async"></a>Async

Como o [Python é um tempo de execução de thread único](https://wiki.python.org/moin/GlobalInterpreterLock), uma instância de host para Python pode processar apenas uma invocação de função por vez, por padrão. Para aplicativos que processam um grande número de eventos de e/s e/ou são associados a e/s, você pode melhorar significativamente o desempenho executando funções de forma assíncrona.

Para executar uma função de forma assíncrona, use a instrução `async def`, que executa a função com [asyncio](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Aqui está um exemplo de uma função com um gatilho HTTP que usa o cliente http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Uma função sem a `async` palavra-chave é executada automaticamente em um pool de threads ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Para obter o benefício total da execução de funções de forma assíncrona, a operação/biblioteca de e/s usada em seu código também precisa ter a implementação assíncrona. O uso de operações de e/s síncronas em funções que são definidas como assíncronas **pode prejudicar** o desempenho geral. Se as bibliotecas que você está usando não tiverem a versão assíncrona implementada, você ainda poderá se beneficiar com a execução de seu código de forma assíncrona, [Gerenciando o loop de eventos](#managing-event-loop) em seu aplicativo. 

Aqui estão alguns exemplos de bibliotecas de cliente que implementaram o padrão assíncrono:
- [aiohttp](https://pypi.org/project/aiohttp/) -cliente/servidor http para asyncio 
- [API de fluxos](https://docs.python.org/3/library/asyncio-stream.html) -primitivos assíncronos/em espera de alto nível para trabalhar com a conexão de rede
- Fila [Janus](https://pypi.org/project/janus/) – fila com reconhecimento de asyncio seguro para o Python
- [pyzmq](https://pypi.org/project/pyzmq/) -associações do Python para ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Noções básicas sobre Async no trabalho do Python

Quando você define `async` na frente de uma assinatura de função, o Python marca a função como uma corrotina. Ao chamar a corrotina, ela pode ser agendada como uma tarefa em um loop de evento. Quando você chama `await` uma função Async, ele registra uma continuação no loop de eventos e permite que o loop de eventos processe a próxima tarefa durante o tempo de espera.

Em nosso trabalho do Python, o trabalho compartilha o loop de eventos com a `async` função do cliente e é capaz de lidar com várias solicitações simultaneamente. Recomendamos que nossos clientes façam uso de bibliotecas compatíveis com asyncio (por exemplo, [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). O emprego dessas recomendações aumentará muito a taxa de transferência da função em comparação com as bibliotecas implementadas de maneira síncrona.

> [!NOTE]
>  Se sua função for declarada como `async` sem qualquer `await` dentro de sua implementação, o desempenho de sua função será seriamente afetado, pois o loop de evento será bloqueado, o que proíbe o trabalho do Python a manipular solicitações simultâneas.

#### <a name="use-multiple-language-worker-processes"></a>Usar vários processos de trabalho de linguagem

Por padrão, cada instância de host do Functions tem um processo de trabalho de linguagem único. Você pode aumentar o número de processos de trabalho por host (até 10) com a configuração de aplicativo [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). O Azure Functions tenta distribuir uniformemente invocações de função simultâneas entre esses trabalhos.

Para aplicativos associados à CPU, você deve definir o número de trabalho de linguagem como igual ou maior que o número de núcleos disponíveis por aplicativo de funções. Para saber mais, confira [SKUs de instância disponíveis](functions-premium-plan.md#available-instance-skus). 

Os aplicativos associados à e/s também podem se beneficiar do aumento do número de processos de trabalho além do número de núcleos disponíveis. Tenha em mente que definir o número de trabalhadores muito alto pode afetar o desempenho geral devido ao maior número de alternâncias de contexto necessárias. 

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que o Functions cria quando escala horizontalmente seu aplicativo para atender à demanda.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Configurar trabalhos máximos dentro de um processo de trabalho de idioma

Conforme mencionado na [seção](#understanding-async-in-python-worker)Async, o trabalho do Python Language trata as funções e as [corrotinas](https://docs.python.org/3/library/asyncio-task.html#coroutines) de forma diferente. Uma corrotina é executada dentro do mesmo loop de evento em que o trabalho de idioma é executado. Por outro lado, uma invocação de função é executada em um [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), que é mantido pelo operador de idioma, como um thread.

Você pode definir o valor máximo de trabalhadores permitidos para executar funções de sincronização usando a configuração [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) aplicativo. Esse valor define o `max_worker` argumento do objeto ThreadPoolExecutor, que permite que o Python use um pool de no máximo `max_worker` threads para executar chamadas de forma assíncrona. O `PYTHON_THREADPOOL_THREAD_COUNT` aplica-se a cada trabalho criado pelo host do Functions e o Python decide quando criar um novo thread ou reutilizar o thread ocioso existente. Para versões anteriores do Python (ou seja,, `3.8` `3.7` e `3.6` ), o `max_worker` valor é definido como 1. Para a versão do Python `3.9` , `max_worker` é definido como `None` .

Para aplicativos associados à CPU, você deve manter a configuração para um número baixo, começando em 1 e aumentando à medida que experimenta sua carga de trabalho. Essa sugestão é reduzir o tempo gasto em alternâncias de contexto e permitir que tarefas vinculadas à CPU sejam concluídas.

Para aplicativos associados à e/s, você deve ver ganhos substanciais aumentando o número de threads trabalhando em cada invocação. a recomendação é começar com o padrão do Python – o número de núcleos + 4 e, em seguida, ajustar com base nos valores de taxa de transferência que você está vendo.

Para aplicativos de cargas de trabalho de combinação, você deve balancear as duas `FUNCTIONS_WORKER_PROCESS_COUNT` e as `PYTHON_THREADPOOL_THREAD_COUNT` configurações para maximizar a taxa de transferência. Para entender o que seus aplicativos de funções gastam mais tempo, é recomendável criar o perfil deles e definir os valores de acordo com o comportamento que eles apresentam. Consulte também esta [seção](#use-multiple-language-worker-processes) para saber mais sobre FUNCTIONS_WORKER_PROCESS_COUNT configurações do aplicativo.

> [!NOTE]
>  Embora essas recomendações se apliquem a funções disparadas por HTTP e não HTTP, talvez seja necessário ajustar outras configurações específicas de gatilho para funções disparadas não HTTP para obter o desempenho esperado de seus aplicativos de funções. Para obter mais informações sobre isso, consulte este [artigo](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Gerenciando o loop de eventos

Você deve usar bibliotecas de terceiros compatíveis com o asyncio. Se nenhuma das bibliotecas de terceiros atender às suas necessidades, você também poderá gerenciar os loops de eventos no Azure Functions. O gerenciamento de loops de eventos proporciona mais flexibilidade no gerenciamento de recursos de computação e também possibilita encapsular bibliotecas de e/s síncronas em corotinas.

Há muitos documentos práticos de Python úteis discutindo as [corrotinas e tarefas](https://docs.python.org/3/library/asyncio-task.html) e o [loop de eventos](https://docs.python.org/3.8/library/asyncio-eventloop.html) usando a biblioteca **asyncio** interna.

Use a biblioteca de [solicitações](https://github.com/psf/requests) a seguir como exemplo, esse trecho de código usa a biblioteca **asyncio** para encapsular o `requests.get()` método em uma corrotina, executando várias solicitações da Web para SAMPLE_URL simultaneamente.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Dimensionamento vertical
Para mais unidades de processamento, especialmente em operação associada à CPU, você poderá obtê-las Atualizando para o plano Premium com especificações mais altas. Com unidades de processamento mais altas, você pode ajustar o número de contagem de processos de trabalho de acordo com o número de núcleos disponíveis e atingir um grau maior de paralelismo. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Azure Functions desenvolvimento em Python, consulte os seguintes recursos:

* [Guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md)
* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)

