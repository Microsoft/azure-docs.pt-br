---
title: Kit de processamento em lote para contêineres de fala
titleSuffix: Azure Cognitive Services
description: Use o kit de processamento em lote para dimensionar solicitações de contêiner de fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631985"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Kit de processamento em lote para contêineres de fala

Use o kit de processamento em lote para complementar e expandir cargas de trabalho em contêineres de fala. Disponível como um contêiner, esse utilitário de código-fonte aberto ajuda a facilitar a transcrição do lote para grandes números de arquivos de áudio, em qualquer número de pontos de extremidade de contêiner de fala locais e baseados em nuvem. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Um diagrama mostrando um exemplo de fluxo de trabalho de contêiner do kit de lote.":::

O contêiner do kit do lote está disponível gratuitamente no [GitHub](https://github.com/microsoft/batch-processing-kit) e no   [Hub do Docker](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Você é [cobrado](speech-container-howto.md#billing) apenas pelos contêineres de fala que usa.

| Recurso  | Descrição  |
|---------|---------|
| Distribuição de arquivos de áudio em lotes     | Despache automaticamente grandes números de arquivos para pontos de extremidade de contêiner de fala locais ou baseados em nuvem. Os arquivos podem estar em qualquer volume compatível com POSIX, incluindo sistemas de arquivos de rede.       |
| Integração do SDK de fala | Passe sinalizadores comuns para o SDK de fala, incluindo: n-melhores hipóteses, diarization, linguagem e mascaramento de profanação.  |
|Modos de execução     | Execute o cliente do lote uma vez, continuamente em segundo plano, ou crie pontos de extremidade HTTP para arquivos de áudio.         |
| Tolerância a falhas | Repetir automaticamente e continuar a transcrição sem perder o progresso e diferenciar entre quais erros podem e não pode ser repetido. |
| Detecção de disponibilidade de ponto de extremidade | Se um ponto de extremidade ficar indisponível, o cliente do lote continuará a transcrever, usando outros pontos de extremidade do contêiner. Depois de se tornar disponível novamente, o cliente começará automaticamente a usar o ponto de extremidade.   |
| Troca automática de ponto de extremidade | Adicione, remova ou modifique pontos de extremidade de contêiner de fala durante o tempo de execução sem interromper o andamento do lote. As atualizações são imediatas. |
| Log em tempo real | Log em tempo real de tentativas de solicitações, carimbos de data/hora e motivos de falha, com arquivos de log do SDK de fala para cada arquivo de áudio. |

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use o comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar o contêiner do kit do lote mais recente.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Configuração de ponto de extremidade

O cliente de lote usa um arquivo de configuração YAML que especifica os pontos de extremidade do contêiner local. O exemplo a seguir pode ser gravado `/mnt/my_nfs/config.yaml` , que é usado nos exemplos abaixo. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Este exemplo de YAML especifica três contêineres de fala em três hosts. O primeiro host é especificado por um endereço IPv4, o segundo é executado na mesma VM que o cliente de lote, e o terceiro contêiner é especificado pelo nome de host DNS de outra VM. O `concurrency` valor especifica o máximo de transcrições de arquivos simultâneos que podem ser executados no mesmo contêiner. O `rtf` valor (fator em tempo real) é opcional e pode ser usado para ajustar o desempenho.
 
O cliente de lote pode detectar dinamicamente se um ponto de extremidade se torna indisponível (por exemplo, devido a um problema de reinicialização de contêiner ou de rede) e quando ele se torna disponível novamente. As solicitações de transcrição não serão enviadas aos contêineres que estão indisponíveis e o cliente continuará usando outros contêineres disponíveis. Você pode adicionar, remover ou editar pontos de extremidade a qualquer momento sem interromper o progresso do lote.


## <a name="run-the-batch-processing-container"></a>Executar o contêiner de processamento em lotes
  
> [!NOTE] 
> * Este exemplo usa o mesmo diretório ( `/my_nfs` ) para o arquivo de configuração e os diretórios de entradas, saídas e logs. Você pode usar diretórios montados ou com o NFS para essas pastas.
> * Executar o cliente do com o `–h` listará os parâmetros de linha de comando disponíveis e seus valores padrão. 
> * O contêiner de processamento em lotes só tem suporte no Linux.

Use o comando Docker `run` para iniciar o contêiner. Isso iniciará um shell interativo dentro do contêiner.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Para executar o cliente do lote:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Para executar o cliente e o contêiner do lote em um único comando:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


O cliente começará a ser executado. Se um arquivo de áudio já tiver sido transcrita em uma execução anterior, o cliente irá ignorar automaticamente o arquivo. Os arquivos são enviados com uma nova tentativa automática se ocorrerem erros transitórios e você pode diferenciar entre quais erros você deseja que o cliente tente novamente. Em um erro de transcrição, o cliente continuará a transcrição e poderá tentar novamente sem perder o progresso.  

## <a name="run-modes"></a>Modos de execução 

O kit de processamento em lote oferece três modos, usando o `--run-mode` parâmetro.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` o modo transcreve um único lote de arquivos de áudio (de um diretório de entrada e uma lista de arquivos opcionais) para uma pasta de saída.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Um diagrama que mostra o contêiner do kit do lote Processando arquivos no modo oneshot.":::

1. Defina os pontos de extremidade do contêiner de fala que serão usados pelo cliente do lote no `config.yaml` arquivo. 
2. Coloque os arquivos de áudio para transcrição em um diretório de entrada.  
3. Invoque o contêiner no diretório, que começará a processar os arquivos. Se o arquivo de áudio já tiver sido transcrita em uma execução anterior com o mesmo diretório de saída (mesmo nome de arquivo e soma de verificação), o cliente irá ignorar o arquivo. 
4. Os arquivos são expedidos para os pontos de extremidade do contêiner da etapa 1.
5. Os logs e a saída do contêiner de fala são retornados para o diretório de saída especificado. 

#### <a name="daemon"></a>[Daemon](#tab/daemon)

> [!TIP]
> Se vários arquivos forem adicionados ao diretório de entrada ao mesmo tempo, você poderá melhorar o desempenho em vez de adicioná-los em um intervalo regular.

`DAEMON` o modo transcreve os arquivos existentes em uma determinada pasta e, continuamente, transcreve novos arquivos de áudio à medida que eles são adicionados.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Um diagrama que mostra os arquivos de processamento do contêiner do kit do lote no modo daemon.":::

1. Defina os pontos de extremidade do contêiner de fala que serão usados pelo cliente do lote no `config.yaml` arquivo. 
2. Invocar o contêiner em um diretório de entrada. O cliente do lote começará a monitorar o diretório em busca de arquivos de entrada. 
3. Configure a entrega de arquivo de áudio contínua para o diretório de entrada. Se o arquivo de áudio já tiver sido transcrita em uma execução anterior com o mesmo diretório de saída (mesmo nome de arquivo e soma de verificação), o cliente irá ignorar o arquivo. 
4. Quando um sinal de gravação de arquivo ou POSIX é detectado, o contêiner é disparado para responder.
5. Os arquivos são expedidos para os pontos de extremidade do contêiner da etapa 1.
6. Os logs e a saída do contêiner de fala são retornados para o diretório de saída especificado. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` Mode é um modo de servidor de API que fornece um conjunto básico de pontos de extremidade HTTP para envio de lote de arquivos de áudio, verificação de status e sondagem longa. Também habilita o consumo programático usando uma extensão de módulo python ou a importação como um submódulo.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Um diagrama que mostra o contêiner do kit do lote Processando arquivos no modo REST.":::

1. Defina os pontos de extremidade do contêiner de fala que serão usados pelo cliente do lote no `config.yaml` arquivo. 
2. Envie uma solicitação de solicitação HTTP para um dos pontos de extremidade do servidor de API. 
        
    |Ponto de extremidade  |Descrição  |
    |---------|---------|
    |`/submit`     | Ponto de extremidade para criar novas solicitações em lote.        |
    |`/status`     | Ponto de extremidade para verificar o status de uma solicitação em lote. A conexão permanecerá aberta até que o lote seja concluído.       |
    |`/watch`     | Ponto de extremidade para usar sondagem longa HTTP até que o lote seja concluído.        |

3. Os arquivos de áudio são carregados do diretório de entrada. Se o arquivo de áudio já tiver sido transcrita em uma execução anterior com o mesmo diretório de saída (mesmo nome de arquivo e soma de verificação), o cliente irá ignorar o arquivo. 
4. Se uma solicitação for enviada ao ponto de `/submit` extremidade, os arquivos serão expedidos para os pontos de extremidades do contêiner da etapa 1.
5. Os logs e a saída do contêiner de fala são retornados para o diretório de saída especificado. 

---

## <a name="logging"></a>Registro em log

> [!NOTE]
> O cliente do lote pode substituir o arquivo *Run. log* periodicamente se ele ficar muito grande.

O cliente cria um arquivo *Run. log* no diretório especificado pelo `-log_folder` argumento no comando Docker `run` . Os logs são capturados no nível de depuração por padrão. Os mesmos logs são enviados para o `stdout/stderr` e filtrados dependendo dos `-file_log_level` `console_log_level` argumentos ou. Esse log só é necessário para depuração, ou se você precisar enviar um rastreamento para suporte. A pasta de log também contém os logs do SDK de fala para cada arquivo de áudio.

O diretório de saída especificado por `-output_folder` conterá um *run_summary.jsno*   arquivo, que é regravado periodicamente a cada 30 segundos ou sempre que novas transcrições são concluídas. Você pode usar esse arquivo para verificar o andamento conforme o lote continua. Ele também conterá as estatísticas de execução final e o status final de cada arquivo quando o lote for concluído. O lote é concluído quando o processo tem uma saída limpa. 

## <a name="next-steps"></a>Próximas etapas

* [Como instalar e executar contêineres](speech-container-howto.md)
