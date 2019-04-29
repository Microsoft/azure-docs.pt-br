---
title: Interface de linha de comando - API de Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Use a interface de linha de comando para criar arquivos da gramática e índices a partir dos dados estruturados e, em seguida, implantá-los como serviços Web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815148"
---
# <a name="command-line-interface"></a>Interface de Linha de Comando

A interface de linha de comando do KES (Serviço de Exploração de Conhecimento) fornece a capacidade para criar arquivos da gramática e índices a partir dos dados estruturados e implantá-los como serviços Web.  Ela usa a sintaxe geral: `kes.exe <command> <required_args> [<optional_args>]`.  Você pode executar `kes.exe` sem argumentos para exibir uma lista de comandos, ou `kes.exe <command>` para exibir uma lista de argumentos disponíveis para o comando especificado.  Abaixo está uma lista de comandos disponíveis:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>Comando build_index

O comando **build_index** cria um arquivo de índice binário de um arquivo de definição de esquema e um arquivo de dados de objetos a serem indexados.  O arquivo de índice resultante pode ser usado para avaliar as expressões de consulta estruturada, ou para gerar interpretações de consultas em linguagem natural em conjunto com um arquivo de gramática compilados.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parâmetro      | DESCRIÇÃO               |
|----------------|---------------------------|
| `<schemaFile>` | Caminho do esquema de entrada |
| `<dataFile>`   | Caminho de dados de entrada   |
| `<indexFile>`  | Caminho do índice de saída |
| `--description <description>` | Cadeia de caracteres de descrição |
| `--remote <vmSize>`           | Tamanho da máquina virtual para a compilação remota |

Esses arquivos podem ser especificados por caminhos de arquivo local ou caminhos de URL para blobs do Azure.  O arquivo de esquema descreve a estrutura dos objetos que está sendo indexada, bem como as operações com suporte (consulte [Formato de esquema](SchemaFormat.md)).  O arquivo de dados enumera os objetos e os valores de atributo a serem indexados (consulte [Formato de dados](DataFormat.md)).  Quando a compilação é bem-sucedida, o arquivo de índice de saída contém uma representação compactada dos dados de entrada que oferece suporte às operações desejadas.  

Uma cadeia de caracteres de descrição pode ser especificada opcionalmente para identificar subsequentemente um índice binário usando o comando **describe_index**.  

Por padrão, o índice é criado no computador local.  Fora do ambiente do Azure, compilações locais são limitadas a arquivos de dados contendo até 10.000 objetos.  Quando o sinalizador remoto for especificado, o índice será criado em uma VM do Azure criada temporariamente do tamanho especificado.  Isso permite que os índices grandes sejam criados com eficiência usando máquinas virtuais do Azure com mais memória.  Para evitar a paginação que desacelera o processo de compilação, é recomendável usar uma máquina virtual com 3 vezes a quantidade de RAM do tamanho do arquivo de dados de entrada.  Para obter uma lista de tamanhos de VM disponíveis, consulte [Tamanhos para máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Para compilações mais rápidas, classifique previamente os objetos no arquivo de dados, diminuindo a probabilidade de log.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>Comando build_grammar

O comando **build_grammar** compila uma gramática especificada em XML para um arquivo de gramática binária.  O arquivo de gramática resultante pode ser usado em conjunto com um arquivo de índice para gerar interpretações de consultas em linguagem natural.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parâmetro       | DESCRIÇÃO               |
|-----------------|---------------------------|
| `<xmlFile>`     | Caminho de entrada de especificação de gramática XML |
| `<grammarFile>` | Caminho de saída de gramática compilada         |

Esses arquivos podem ser especificados por caminhos de arquivo local ou caminhos de URL para blobs do Azure.  A especificação de gramática descreve o conjunto de expressões de linguagem natural ponderada e suas interpretações semânticas (consulte [Formato de gramática](GrammarFormat.md)).  Quando a compilação é bem-sucedida, o arquivo de gramática de saída contém uma representação binária da especificação de gramática para habilitar a decodificação rápida.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>Comando host_service

O comando **host_service** hospeda uma instância do serviço KES no computador local.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parâmetro       | DESCRIÇÃO                |
|-----------------|----------------------------|
| `<grammarFile>` | Caminho de entrada de gramática binária         |
| `<indexFile>`   | Caminho de entrada de índice binário           |
| `--port <port>` | Número da porta local.  Padrão: 8000 |

Esses arquivos podem ser especificados por caminhos de arquivo local ou caminhos de URL para blobs do Azure.  Um serviço web será hospedado em http://localhost:&lt; porta&gt;/.  Consulte [APIs da Web](WebAPI.md) para obter uma lista de operações com suporte.

Fora do ambiente do Azure, os serviços hospedados localmente são limitados aos arquivos de índice de até 1 MB em tamanho, 10 solicitações por segundo e total de 1000 chamadas.  Para superar essas limitações, execute **host_service** dentro de uma VM do Azure, ou implantar um serviço de nuvem do Azure usando **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>Comando deploy_service

O comando **deploy_service** implanta uma instância do serviço KES para um serviço de nuvem do Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parâmetro       | DESCRIÇÃO                  |
|-----------------|------------------------------|
| `<grammarFile>` | Caminho de entrada de gramática binária           |
| `<indexFile>`   | Caminho de entrada de índice binário             |
| `<serviceName>` | Nome do serviço de nuvem de destino |
| `<vmSize>`      | Tamanho da VM do serviço de nuvem     |
| `--slot <slot>` | Slot de serviço de nuvem: "preparação" (padrão), "produção" |

Esses arquivos podem ser especificados por caminhos de arquivo local ou caminhos de URL para blobs do Azure.  O nome do serviço especifica um serviço de nuvem do Azure pré-configurado (consulte [Como criar e implantar um serviço de nuvem](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  O comando implantará automaticamente o serviço KES para o serviço de nuvem do Azure especificado, usando as máquinas virtuais do tamanho especificado.  Para evitar a paginação que reduz significativamente o desempenho, é recomendável usar uma máquina virtual com 1 GB de RAM a mais que o tamanho do arquivo de índice de entrada.  Para obter uma lista de tamanhos de VM disponíveis, consulte [Tamanhos para Serviços de Nuvem](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Por padrão, o serviço é implantado no ambiente de preparação, opcionalmente, substituído por meio do parâmetro --slot.  Consulte [APIs da Web](WebAPI.md) para obter uma lista de operações com suporte.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>Comando describe_index

O comando **describe_index** gera informações sobre um arquivo de índice, inclusive o esquema e a descrição.

`kes.exe describe_index <indexFile>`

| Parâmetro     | DESCRIÇÃO      |
|---------------|------------------|
| `<indexFile>` | Caminho de saída do índice |

Esse arquivo pode ser especificado por um caminho de arquivo local ou um caminho de URL para um blob do Azure.  A cadeia de caracteres de descrição de saída pode ser especificada usando o parâmetro --descrição do comando **build_index**.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>Comando describe_grammar

O comando **describe_grammar** gera a especificação de gramática original usada para construir a gramática binária.

`kes.exe describe_grammar <grammarFile>`

| Parâmetro       | DESCRIÇÃO      |
|-----------------|------------------|
| `<grammarFile>` | Caminho de entrada de gramática |

Esse arquivo pode ser especificado por um caminho de arquivo local ou um caminho de URL para um blob do Azure.

