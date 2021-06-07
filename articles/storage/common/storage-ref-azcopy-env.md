---
title: azcopy env | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ceddc8848df2f8e0456d2b0f4dab66a76e6eff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879115"
---
# <a name="azcopy-env"></a>azcopy env

Mostra as variáveis de ambiente que podem configurar o comportamento do AzCopy.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostra o conteúdo da ajuda para o comando env. |
|--Mostrar-diferenciar|Mostra variáveis de ambiente confidenciais/secretas.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps float|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft  | Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Veja também

- [azcopy](storage-ref-azcopy.md)
