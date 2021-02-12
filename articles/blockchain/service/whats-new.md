---
title: Quais são as novidades? Notas de versão – serviço Blockchain do Azure
description: Saiba o que há de novo no serviço Blockchain do Azure, como as últimas notas de versão, versões, problemas conhecidos e alterações futuras.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335019"
---
# <a name="whats-new-in-azure-blockchain-service"></a>O que há de novo no serviço Blockchain do Azure?

> Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` em seu ícone do [ ![ leitor de RSS feed](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)do leitor de RSS feed.

O serviço Blockchain do Azure recebe melhorias em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- Novos recursos
- Atualizações de versão
- Problemas conhecidos

---

## <a name="june-2020"></a>Junho de 2020

### <a name="version-upgrades"></a>Atualizações de versão

- Atualização da versão de quorum para 2.6.0. Com a versão 2.6.0, você pode enviar transações privadas assinadas. Para obter mais informações sobre como enviar transações privadas, consulte a [documentação da API de quorum](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- Atualização da versão do Tessera para 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Tamanho do contrato e tamanho da transação aumentados para 128 KB

Tipo: alteração de configuração

O tamanho do contrato (MaxCodeSize) foi aumentado para 128 KB para que você possa implantar contratos inteligentes de tamanho maior. Além disso, o tamanho da transação (txnSizeLimit) foi aumentado para 128 KB. As alterações de configuração se aplicam a novos consórcio criados no serviço Blockchain do Azure depois de junho de 19 2020.

### <a name="trietimeout-value-reduced"></a>Valor de TrieTimeout reduzido

Tipo: alteração de configuração

O valor de TrieTimeout foi reduzido para que o estado na memória seja gravado no disco com mais frequência. O valor mais baixo garante uma recuperação mais rápida de um nó no caso raro de uma falha de nó.

## <a name="may-2020"></a>Maio de 2020

### <a name="version-upgrades"></a>Atualizações de versão

- Atualização da versão do Ubuntu para 18, 4
- Atualização da versão de quorum para 2.5.0
- Tessera de atualização de versão do 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>O serviço Blockchain do Azure dá suporte ao envio de transações rawPrivate

Tipo: recurso

Os clientes podem assinar transações privadas fora da conta no nó.

### <a name="two-phase-member-provisioning"></a>Provisionamento de membro de duas fases

Tipo: aprimoramento

Duas fases ajudam a otimizar cenários em que um membro está sendo criado em um consórcio longo existente. A infraestrutura de membro é provisionada na primeira fase. Na segunda fase, o membro é sincronizado com blockchain. O provisionamento de duas fases ajuda a evitar a falha na criação de membros devido a tempos limite.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>a função ETH. estimateGas gera uma exceção no quorum v 2.6.0

No quorum v 2.6.0, as chamadas para a função *ETH. estimateGas* sem fornecer o parâmetro de *valor* adicional causam uma falha na exceção do *manipulador de método* . A equipe de quorum foi notificada e uma correção é esperada no final de julho de 2020. Você pode usar as seguintes soluções alternativas até que uma correção esteja disponível:

- Evite usar *ETH. estimateGas* , já que ele pode afetar o desempenho. Para obter mais informações sobre problemas de desempenho ETH. estimateGas, consulte [chamando a função ETH. estimateGas reduz o desempenho](#calling-ethestimategas-function-reduces-performance). Inclua um valor de gás para cada transação. A maioria das bibliotecas chamará ETH. estimateGas se um valor de gás não for fornecido, fazendo com que o quorum v 2.6.0 falhe.
- Se você precisar chamar *ETH. estimateGas* , a equipe de quorum sugere que você passe o *valor* de parâmetro adicional como *0* como uma solução alternativa.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>A mineração será interrompida se menos de quatro nós de validação

As redes de produção devem ter pelo menos quatro nós de validador. O quorum recomenda que pelo menos quatro nós de validador sejam necessários para atender à tolerância a falhas de falha IBFT (3F + 1). Você deve ter pelo menos dois nós da camada *Standard* do serviço Blockchain do Azure para obter quatro nós de validação. Um nó padrão é provisionado com dois nós de validação.  

Se a rede Blockchain no serviço Blockchain do Azure não tiver quatro nós de validador, a mineração poderá parar na rede. Você pode detectar que a mineração foi interrompida definindo um alerta em blocos processados. Em uma rede íntegra, o bloco processado será de 60 blocos por nó por cinco minutos.

Como uma mitigação, a equipe do serviço Blockchain do Azure precisa reiniciar o nó. Os clientes precisam abrir uma solicitação de suporte para reiniciar o nó. A equipe do serviço Blockchain do Azure está trabalhando para detectar e corrigir problemas de mineração automaticamente.

Use a camada *Standard* para implantações de nível de produção. Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Gerenciador de Dados requer o nó da camada Standard

Use a camada *Standard* se você estiver usando o Blockchain Gerenciador de dados. A camada *básica* tem apenas 4 GB de memória. Portanto, não é possível dimensionar para o uso exigido pelo Blockchain Gerenciador de Dados e outros serviços em execução nele.

Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Grande volume de chamadas de conta de desbloqueio faz com que o Geth falhe

Um grande volume de chamadas de conta de desbloqueio ao enviar a transação pode fazer com que o Geth falhe em um nó de transação. Como resultado, você precisa parar de ingerir as transações. Caso contrário, a fila de transações pendentes aumentará.

Geth reinicia automaticamente em menos de um minuto. Dependendo do nó, a sincronização pode levar muito tempo. A equipe do serviço Blockchain do Azure está habilitando um recurso de arquivamento que reduzirá o tempo de sincronização.

Para identificar falhas do Geth, você pode verificar os logs em busca de qualquer mensagem de erro em mensagens do Blockchain nos logs do aplicativo. Você também pode verificar se os blocos processados diminuem enquanto as transações pendentes aumentam.

Para atenuar o problema, envie transações assinadas em vez de enviar transações não assinadas com um comando para desbloquear a conta. Para transações que já foram assinadas externamente, não é necessário desbloquear a conta.

Se você quiser enviar transações não assinadas, desbloqueie a conta por tempo infinito enviando 0 como o parâmetro de hora no comando de desbloqueio. Você pode bloquear a conta novamente depois que todas as transações forem enviadas.  

A seguir estão os parâmetros de Geth que o serviço de Blockchain do Azure usa. Você não pode ajustar esses parâmetros.

- Período de bloqueio de Istambul: 5 segundos
- Limite de gás do piso: 700 milhões
- Limite de gás do Ceil: 800 milhões

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Grande volume de transações privadas reduz o desempenho

Se você estiver usando a camada básica do serviço Blockchain do Azure e transações privadas, Tessera poderá falhar.

Você pode detectar a falha do Tessera examinando os logs do aplicativo Blockchain e pesquisando a mensagem `Tessera crashed. Restarting Tessera...` .

O serviço Blockchain do Azure reinicia o Tessera quando há uma falha. A reinicialização leva cerca de um minuto.

Use a camada *Standard* se você estiver enviando um alto volume de transações privadas. Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

### <a name="calling-ethestimategas-function-reduces-performance"></a>A chamada a função ETH. estimateGas reduz o desempenho

Chamar a função *ETH. estimateGas* várias vezes reduz as transações por segundo drasticamente. Não use a função *ETH. estimateGas* para cada envio de transação. A função *ETH. estimateGas* tem uso intensivo de memória.

Se possível, use um valor de gás conservador para enviar transações e minimizar o uso de *ETH. estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Loops não associados em contratos inteligentes reduzem o desempenho

Evite loops não associados em contratos inteligentes, pois eles podem reduzir o desempenho. Para obter mais informações, consulte os seguintes recursos:

- [Evitar loops não associados](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Práticas recomendadas de segurança de contrato inteligente](https://github.com/ConsenSys/smart-contract-best-practices)
- [Diretrizes de contrato inteligente fornecidas pelo quorum](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Diretrizes sobre limites e loops de gás fornecidos pela solidez](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)