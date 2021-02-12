---
title: Testar Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Explica como executar o comando de teste do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632545"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Testar Aplicativo Azure ferramenta de instantâneo consistente (visualização)

Este artigo explica como executar o comando de teste do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

Um teste da configuração é feito usando o `azacsnap -c test` comando.

## <a name="command-options"></a>Opções de comando

O `-c test` comando tem as seguintes opções:

- `--test hana`  testa a conexão com a instância de SAP HANA.

- `--test storage` testa a comunicação com a interface de armazenamento subjacente criando um instantâneo de armazenamento temporário em todos os volumes configurados `data` e, em seguida, removendo-os. 

- `--test all` executará os `hana` testes e `storage` em sequência.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de arquivo de configuração personalizada.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Verificar a conectividade com SAP HANA `azacsnap -c test --test hana`

Esse comando verifica a conectividade do HANA para todas as instâncias do HANA no arquivo de configuração. Ele usa o HDBuserstore para se conectar ao SYSTEMDB e busca as informações de SID.

Para SSL, esse comando pode usar o seguinte argumento opcional:

- `--ssl=` força uma conexão criptografada com o banco de dados e define o método de criptografia usado para se comunicar com SAP HANA, `openssl` ou `commoncrypto` . Se definido, esse comando espera encontrar dois arquivos no mesmo diretório, esses arquivos devem ser nomeados após o SID correspondente. Consulte [usando SSL para comunicação com o SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Saída do `azacsnap -c test --test hana` comando

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Verificar a conectividade com o armazenamento `azacsnap -c test --test storage`

O `azacsnap` comando usará um instantâneo para todos os volumes de dados configurados para verificar se ele tem o acesso correto aos volumes para cada instância de SAP Hana. Um instantâneo temporário é criado e, em seguida, removido para cada volume de dados para verificar o acesso ao instantâneo de cada sistema de arquivos.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Saída do `azacsnap -c test --test storage` comando

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Para a instância grande do Azure, `azacsnap -c test --test storage` o comando extrapola a geração de armazenamento e a SKU de HLI.  Com base nessas informações, ele fornece orientação sobre como configurar instantâneos de ' inicialização ' (consulte a linha que começa com `Action:` saída).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Próximas etapas

- [Backup de instantâneo com ferramenta de instantâneo consistente Aplicativo Azure](azacsnap-cmd-ref-backup.md)
