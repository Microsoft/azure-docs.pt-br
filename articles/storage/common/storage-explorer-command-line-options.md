---
title: Gerenciador de Armazenamento do Azure opções de linha de comando | Microsoft Docs
description: Documentação de Gerenciador de Armazenamento do Azure opções de linha de comando de inicialização
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744140"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opções de linha de comando Gerenciador de Armazenamento do Azure

Gerenciador de Armazenamento do Microsoft Azure tem um conjunto de opções de linha de comando que podem ser adicionadas ao iniciar o aplicativo. A maioria das opções de linha de comando são para fins de depuração ou solução de problemas.

## <a name="command-line-options"></a>Opções de linha de comando
Opção  | Descrição
:------- | :-----------
`--debug`/`--prod`  | Inicie o aplicativo no modo de depuração ou de produção. No modo de depuração, os dados do anexo local serão armazenados no armazenamento local do aplicativo e não serão criptografados. As propriedades ocultas serão exibidas no painel de propriedades para os nós de recursos selecionados. O nível de detalhes do log será definido para imprimir mensagens de depuração revelando a lógica de instalação interna do Gerenciador de Armazenamento. O valor padrão é `--prod`.
`--lang`  | Inicie o aplicativo com um determinado idioma. Por exemplo, `--lang="zh-Hans"`.
`--disable-gpu` | Inicie o aplicativo sem aceleração de GPU.
`--auto-open-dev-tools` | Deixe o aplicativo abrir a janela ferramentas de desenvolvedor assim que a janela do navegador for exibida. Essa opção é útil quando você deseja atingir um ponto de interrupção em uma linha no código de inicialização da janela do navegador.
`--verbosity` | Defina o nível de detalhes do log de Gerenciador de Armazenamento. Os níveis de detalhamento com suporte incluem,,,, `debug` `verbose` `info` `warn` `error` e `silent` . Por exemplo, `--verbosity=verbose`. Ao executar em modo de produção, o nível de verbosidade padrão é `info` . Quando executado no modo de depuração, o nível de detalhes do log sempre será `debug` .
`--log-dir` | Defina o diretório para salvar os arquivos de log. Por exemplo, `--log-dir=path_to_a_directory`.

Um exemplo de como iniciar Gerenciador de Armazenamento com opções de linha de comando personalizadas

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Essas opções de linha de comando podem ser alteradas em novas versões do Gerenciador de Armazenamento.

## <a name="when-to-use-command-line-options"></a>Quando usar opções de linha de comando

Algumas opções de linha de comando podem ser usadas para personalizar Gerenciador de Armazenamento. Para as opções que têm configurações de usuário correspondentes, como `--lang` . É recomendável usar as configurações de usuário em vez de usar a opção de linha de comando. 

As outras opções de linha de comando podem ser úteis para depuração e solução de problemas. Se você encontrar um problema no Gerenciador de Armazenamento, a reprodução do problema no modo de depuração pode nos ajudar a obter informações mais detalhadas a serem investigadas.