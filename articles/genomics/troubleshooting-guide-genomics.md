---
title: 'Microsoft Genomics: Guia de solução de problemas | Microsoft Docs'
titleSuffix: Azure
description: Saiba mais sobre estratégias para solucionar problemas
keywords: solução de problemas, erro, depuração
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 78084e6beac7b390b1ea1afe888030c5224856b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790497"
---
# <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Confira algumas dicas de solução de problemas comuns que podem acontecer ao usar o serviço do MSGEN, o Microsoft Genomics.

 Para ver as perguntas frequentes não relacionadas com a solução de problemas, confira [Perguntas comuns](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Etapa 1: Localize os códigos de erro associados com o fluxo de trabalho

É possível localizar as mensagens de erro associadas ao fluxo de trabalho destas formas:

1. Usando a linha de comando e digitando `msgen status`
2. Examinando o conteúdo de standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Usando a linha de comando `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Há três argumentos necessários:

* URL - o URI de base para a API
* KEY – a chave de acesso à conta do Genomics
    * Para localizar URL e KEY, acesse o portal do Azure e abra a página da conta do Microsoft Genomics. No cabeçalho **Gerenciamento**, escolha **Chaves de acesso**. Lá, você encontra a URL de API e suas chaves de acesso.

  
* ID - a ID do fluxo de trabalho
    * Para localizar o tipo da ID do fluxo de trabalho no comando `msgen list`. Se o arquivo de configuração contiver a URL e as chaves de acesso e estiver na mesma localização que msgen exe, o comando será assim: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        A saída desse comando será assim:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Outra opção é incluir o caminho para o arquivo de configuração em vez de inserir a URL e KEY diretamente. Se você incluir esses argumentos na linha de comando junto com o arquivo de configuração, os argumentos da linha de comando terão precedência.  

Para o fluxo de trabalho ID 1001 e o arquivo config.txt localizado no mesmo caminho que o executável msgen, o comando será assim:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Examine o conteúdo de standardoutput.txt 
Localize o contêiner de saída do fluxo de trabalho em questão. O MSGEN cria uma pasta `[workflowfilename].logs.zip` após cada execução de fluxo de trabalho. Descompacte a pasta para exibir seu conteúdo:

* outputFileList.txt - uma lista dos arquivos de saída produzida durante o fluxo de trabalho
* standarderror.txt - este arquivo está em branco.
* standardoutput.txt – registra todas as mensagens de status de nível superior, incluindo os erros ocorridos ao executar o fluxo de trabalho.
* Arquivos de Log GATK - todos os outros arquivos na pasta `logs`

Para solucionar o problema, examine o conteúdo de standardoutput.txt e observe todas as mensagens de erro exibidas.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Etapa 2: Experimente as etapas recomendadas para erros comuns

Esta seção descreve brevemente os erros comuns gerados pelo serviço do msgen (Microsoft Genomics) e as estratégias que você pode usar para resolvê-los. 

O serviço do msgen (Microsoft Genomics) pode gerar dois tipos de erros:

1. Erros de serviço interno: Erros que são internos ao serviço, que pode não ser resolvido, corrigindo parâmetros ou arquivos de entrada. Às vezes, enviar novamente o fluxo de trabalho pode corrigir esses erros.
2. Erros de entrada: Formatos de arquivo de erros que podem ser resolvidos usando os argumentos corretos ou correção.

### <a name="1-internal-service-errors"></a>1. Erros de serviço interno

Um erro de serviço interno não é acionável pelo usuário. Você pode reenviar o fluxo de trabalho, mas se isso não funcionar, entre em contato com o suporte do Microsoft Genomics

| Mensagem de erro                                                                                                                            | Etapas de solução de problemas recomendadas                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorreu um erro interno. Tente enviar o fluxo de trabalho novamente. Se esse erro ocorrer novamente, entre em contato com o suporte da Microsoft Genomics para obter assistência | Envie o fluxo de trabalho novamente. Contate o suporte do Microsoft Genomics para obter assistência se o problema persistir criando um [tíquete](file-support-ticket-genomics.md ) de suporte. |

### <a name="2-input-errors"></a>2. Erros de entrada

Esses erros são acionáveis pelo usuário. Com base no tipo de arquivo e o código de erro, o serviço do Microsoft Genomics gera códigos de erro distintos. Siga as etapas de solução de problemas recomendadas listadas abaixo.

| Tipo de arquivo | Código do erro | Mensagem de erro                                                                           | Etapas de solução de problemas recomendadas                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualquer          | 701        | A leitura [readId] tem [numberOfBases] bases, mas o limite é [maxReadLength]           | O motivo mais comum para esse erro é um arquivo corrompido, o que leva à concatenação de duas leituras. Verifique os arquivos de entrada. |
| BAM          | 200        |   Não é possível ler o arquivo "[yourFileName]".                                                                                       | Verifique o formato do arquivo BAM. Envie o fluxo de trabalho novamente com um arquivo formatado corretamente.                                                                           |
| BAM          | 201        |  Não é possível ler o arquivo BAM [File_name].                                                                                      |Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 202        | Não é possível ler o arquivo BAM [File_name]. Arquivo muito pequeno e cabeçalho ausente.                                                                                        | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 203        |   Não é possível ler o arquivo BAM [File_name]. O cabeçalho do arquivo foi corrompido.                                                                                      |Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                           |
| BAM          | 204        |    Não é possível ler o arquivo BAM [File_name]. O cabeçalho do arquivo foi corrompido.                                                                                     | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                           |
| BAM          | 205        |    Não é possível ler o arquivo BAM [File_name]. O cabeçalho do arquivo foi corrompido.                                                                                     | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 206        |   Não é possível ler o arquivo BAM [File_name]. O cabeçalho do arquivo foi corrompido.                                                                                      | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 207        |  Não é possível ler o arquivo BAM [File_name]. O arquivo truncado próximo ao deslocamento [offset].                                                                                       | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 208        |   Arquivo BAM inválido. A ReadID [Read_Id] não tem uma sequência no arquivo [File_name].                                                                                      | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                             |
| FASTQ        | 300        |  Não é possível ler o arquivo FASTQ. [File_name] não termina com uma nova linha.                                                                                     | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                           |
| FASTQ        | 301        |   Não é possível ler o arquivo FASTQ [File_name]. O registro do FASTQ é maior que o tamanho do buffer no deslocamento: [_offset]                                                                                      | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 302        |     Erro de sintaxe do FASTQ. O arquivo [File_name] tem uma linha em branco.                                                                                    | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 303        |       Erro de sintaxe do FASTQ. O arquivo [File_name] tem um caractere inicial inválido no deslocamento: [_offset], tipo de linha: [line_type], caractere: [_char]                                                                                  | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 304      |  Erro de sintaxe do FASTQ na readID [_ReadID].  Na primeira leitura do lote, a readID não termina em /1 no arquivo [File_name]                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 305        |  Erro de sintaxe do FASTQ na readID [_readID]. Na segunda leitura do lote, a readID não termina em /2 no arquivo [File_name]                                                                                      | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 306        |  Erro de sintaxe do FASTQ na readID [_ReadID]. A primeira leitura do par não tem uma ID que termina em /1 no arquivo [File_name]                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 307        |   Erro de sintaxe do FASTQ na readID [_ReadID]. ReadID não termina em /1 ou /2. O arquivo [File_name] não pode ser usado como par do arquivo FASTQ.                                                                                      |Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 308        |  Erro de leitura do FASTQ. As leituras de ambas as extremidades tiveram respostas diferentes. Você escolheu os arquivos FASTQ corretos?                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Etapa 3: Entre em contato com o suporte do Microsoft Genomics

Se você continuar a ter falhas de trabalho, ou se você tiver outras dúvidas, contate o suporte do Microsoft Genomics a partir do portal do Azure. Informações adicionais sobre como enviar uma solicitação de suporte podem ser encontradas [aqui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como solucionar problemas e resolver problemas comuns com o serviço Microsoft Genomics. Para obter mais informações e perguntas frequentes mais gerais, consulte [Perguntas comuns](frequently-asked-questions-genomics.md). 
