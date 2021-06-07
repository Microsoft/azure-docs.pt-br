---
title: Versões do razão do serviço Blockchain do Azure, aplicação de patches, atualização &
description: Visão geral das versões de razões com suporte no serviço Blockchain do Azure. Incluindo políticas para aplicação de patches e atualizações de sistemas.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85807733"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões do razão do serviço Blockchain do Azure com suporte

O serviço Blockchain do Azure usa o razão de [Quorum](https://www.goquorum.com/developers) baseado em Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no serviço Blockchain do Azure.

Atualmente, o serviço Blockchain do Azure dá suporte à [versão de quorum 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) e [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades

O controle de versão em quorum é feito por meio de versões principais, secundárias e de patches. Por exemplo, se a versão de quorum for 2.0.1, o tipo de versão será Categorizado da seguinte maneira:

|Principal | Secundária  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O serviço Blockchain do Azure atualiza automaticamente as versões de patch do quorum para os membros em execução existentes dentro de 30 dias após serem disponibilizados do quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões do razão

O serviço Blockchain do Azure fornece as últimas versões principais e secundárias do razão de quorum dentro de 60 dias de disponibilidade do fabricante do quorum. No máximo quatro versões secundárias são fornecidas para que consortia escolha ao provisionar um novo membro e consórcio. No momento, não há suporte para a atualização do para uma versão principal ou secundária. Por exemplo, se você estiver executando a versão 2. x, não há suporte atualmente para a atualização para a versão 3. x. Da mesma forma, se você estiver executando a versão 2,2, não há suporte para uma atualização para a versão 2,3 no momento.

## <a name="how-to-check-quorum-ledger-version"></a>Como verificar a versão do razão do quorum

Você pode verificar a versão de quorum em seu membro do serviço Blockchain do Azure anexando ao seu nó usando Geth ou exibindo logs de diagnóstico.

### <a name="using-geth"></a>Usando Geth

Anexe ao nó do serviço Blockchain do Azure usando o Geth. Por exemplo, `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Quando o nó estiver conectado, o Geth relatará a versão de quorum semelhante à seguinte saída:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Para obter mais informações sobre como usar o Geth, consulte [início rápido: usar o Geth para anexar a um nó de transação do serviço Blockchain do Azure](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Usando logs de diagnóstico

Se você habilitar os logs de diagnóstico, a versão de quorum será relatada para nós de transação. Por exemplo, a seguinte mensagem de log informativo de nó inclui a versão de quorum.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Para obter mais informações sobre logs de diagnóstico, consulte [monitorar o serviço Blockchain do Azure por meio de Azure monitor](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Como verificar o conteúdo do arquivo Genesis

Para verificar o conteúdo do arquivo Genesis do nó blockchain, você pode usar a seguinte API JavaScript do Ethereum:

``` bash
admin.nodeInfo.protocols
```
Você pode chamar a API usando um console do Geth ou uma biblioteca do Web3. Para obter mais informações sobre como usar o Geth, consulte [início rápido: usar o Geth para anexar a um nó de transação do serviço Blockchain do Azure](connect-geth.md).

## <a name="next-steps"></a>Próximas etapas

[Limites no serviço Blockchain do Azure](limits.md)
