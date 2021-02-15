---
title: Opções de configuração da CLI de Fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como criar e gerenciar arquivos de configuração para uso com a CLI de Fala do Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540215"
---
# <a name="speech-cli-configuration-options"></a>Opções de configuração da CLI de Fala

O comportamento da CLI de Fala pode depender das configurações dos arquivos de configuração, que podem ser referenciados usando um símbolo `@`. A CLI de Fala salva uma nova configuração em um novo subdiretório `./spx/data` criado no diretório de trabalho atual para a CLI de Fala. Ao buscar um valor de configuração, a CLI de Fala pesquisa esse valor no diretório de trabalho atual, no armazenamento de dados em `./spx/data` e em outros armazenamentos de dados, incluindo um armazenamento de dados final somente leitura no binário `spx`. 

No início rápido da CLI de Fala, você usou o armazenamento de dados para salvar seus valores `@key` e `@region`, portanto, você não precisou especificá-los com cada comando `spx`. Tenha em mente que você pode usar arquivos de configuração para armazenar as próprias definições de configuração ou até mesmo usá-las para passar URLs ou outro conteúdo dinâmico gerado em runtime.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Criar e gerenciar arquivos de configuração no armazenamento de dados

Esta seção mostra como usar um arquivo de configuração no armazenamento de dados local para armazenar e buscar configurações de comando usando `spx config` e armazenar a saída da CLI de Fala usando a opção `--output`.

O exemplo a seguir limpa o arquivo de configuração `@my.defaults`, adiciona pares chave-valor para **chave** e **região** no arquivo e usa a configuração em uma chamada para `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Você também pode gravar o conteúdo dinâmico em um arquivo de configuração. Por exemplo, o comando a seguir cria um modelo de fala personalizada e armazena a URL do novo modelo em um arquivo de configuração. O próximo comando aguarda até que o modelo na URL esteja pronto para ser usado antes de retornar.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

O exemplo a seguir grava duas URLs no arquivo de configuração `@my.datasets.txt`. Nesse cenário, `--output` pode incluir uma palavra-chave **adicionar** opcional para criar um arquivo de configuração ou acrescentar à existente.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Para obter mais detalhes sobre arquivos do armazenamento de dados, incluindo o uso de arquivos de configuração padrão (`@spx.default`, `@default.config` e `@*.default.config` para configurações padrão específicas de comando), digite este comando:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Próximas etapas 

* [Operações em lote com a CLI de Fala](./spx-batch-operations.md)