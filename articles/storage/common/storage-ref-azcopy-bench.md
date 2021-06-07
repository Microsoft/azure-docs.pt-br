---
title: bancada de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy banca.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282000"
---
# <a name="azcopy-benchmark"></a>parâmetro de comparação azcopy

Executa um parâmetro de comparação de desempenho carregando ou baixando dados de teste de ou para um destino especificado. Para carregamentos, os dados de teste são gerados automaticamente.

O comando de parâmetro de comparação executa o mesmo processo que ' Copy ', exceto que: 

  - Em vez de exigir os parâmetros de origem e de destino, o parâmetro de comparação leva apenas um. Este é o contêiner de BLOB, compartilhamento de arquivos do Azure ou Azure Data Lake Storage Gen2 sistema de arquivos que você deseja carregar ou baixar.

  - O parâmetro ' Mode ' descreve se AzCopy deve testar uploads ou downloads de um determinado destino. Os valores válidos são ' upload ' e ' download '. O valor padrão é ' upload '.

  - Para os parâmetros de comparação de upload, a carga é descrita por parâmetro de linha de comando, que controlam quantos arquivos são gerados automaticamente e quão significativos são os arquivos. O processo de geração ocorre inteiramente na memória. O disco não é usado.

  - Para downloads, a carga consiste em qualquer arquivo que já exista na origem. (Veja o exemplo abaixo sobre como gerar arquivos de teste, se necessário).
  
  - Há suporte apenas para alguns dos parâmetros opcionais disponíveis para o comando de cópia.
  
  - Diagnósticos adicionais são medidos e relatados.
  
  - Para carregamentos, o comportamento padrão é excluir os dados transferidos no final da execução de teste.  Para downloads, os dados nunca são salvos localmente.

O modo de parâmetro de comparação se ajustará automaticamente ao número de conexões TCP paralelas que fornece a taxa de transferência máxima. Ele exibirá esse número no final. Para evitar o ajuste automática, defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE como um número específico de conexões. 

Todos os tipos de autenticação usuais têm suporte. No entanto, a abordagem mais conveniente para upload de parâmetro de comparação normalmente é criar um contêiner vazio com um token SAS e usar a autenticação SAS. (O modo de download requer que um conjunto de dados de teste esteja presente no contêiner de destino.)

## <a name="examples"></a>Exemplos

```azcopy
azcopy benchmark [destination] [flags]
```

Execute um teste de benchmark com parâmetros padrão (adequado para redes de parâmetro de comparação de até 1 Gbps): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Execute um teste de benchmark que carregue 100 arquivos, cada um com 2 GiB de tamanho: (adequado para benchmarking em uma rede rápida, por exemplo, 10 Gbps): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Execute um teste de benchmark, mas use 50.000 arquivos, cada 8 MiB em tamanho e calcule seus hashes MD5 (da mesma forma que o `--put-md5` sinalizador faz isso no comando de cópia). A finalidade de `--put-md5` quando o benchmark é testar se a computação MD5 afeta a taxa de transferência para a contagem de arquivos e o tamanho selecionados:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Executar um teste de benchmark que baixa arquivos existentes de um destino

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Execute um upload que não exclua os arquivos transferidos. (Esses arquivos podem servir como a carga para um teste de download)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opções

**--blob-Type** cadeia de caracteres define o tipo de blob no destino. Usado para permitir o parâmetro de comparação de tipos diferentes de BLOB. Idêntico ao parâmetro de mesmo nome no comando de cópia (padrão "Detect").

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB). O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas-por exemplo, 0,25. Idêntico ao parâmetro de mesmo nome no comando de cópia.

**--comprimento da verificação**  Verifique o comprimento de um arquivo no destino após a transferência. Se houver uma incompatibilidade entre a origem e o destino, a transferência será marcada como com falha. (padrão true)

**--delete-test-data**  Se for true, os dados de parâmetro de comparação serão excluídos no final da execução do parâmetro de comparação.  Defina-a como false se você quiser manter os dados no destino, por exemplo, para usá-los para testes manuais fora do modo de parâmetro de comparação (padrão true).

**--contagem de arquivos** uint.  O número de arquivos de dados gerados automaticamente a serem usados (padrão 100).

**--ajuda**  Ajuda para o bancada

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO")

**--a cadeia de caracteres de modo** define se Azcopy deve testar uploads ou downloads desse destino. Os valores válidos são ' upload ' e ' download '. A opção padrão é ' upload '. (' carregar ' padrão)

**--número de pastas** uint If maior que 0, crie pastas para dividir os dados.

**--Put-MD5**  Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do blob/arquivo de destino. (Por padrão, o hash não é criado.) Idêntico ao parâmetro de mesmo nome no comando de cópia.

--tamanho da cadeia de caracteres de **tamanho por arquivo** de cada arquivo de dados gerado automaticamente. Deve ser um número imediatamente seguido por K, M ou G. por exemplo, 12K ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps float**  Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto").

**--a cadeia de caracteres Trusted-Microsoft-suffixs** especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.


## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
